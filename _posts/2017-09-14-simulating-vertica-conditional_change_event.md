---
layout: post
title:  "Simulating Vertica's conditional_change_event"
author: jay
categories: [ code ]
tags: [ json, snowflake, window fucntions]
image: assets/images/headers/vertica.png
description: "Simulating Vertica's conditional_change_event"
featured: false
hidden: false
comments: false
#rating: 4.5
---



<p>Lately my team has spent a bunch of time migrating our data warehouse from <a href="https://www.vertica.com/" target="_blank">Vertica</a> to <a href="https://www.snowflake.net/" target="_blank">Snowflake</a>. While Snowflake has excellent support for <a href="https://docs.snowflake.net/manuals/sql-reference/functions-analytic.html" target="_blank">analytic functions</a>, Vertica has some functions that no other columnar database supports.&nbsp;</p>
<p>The <a href="https://my.vertica.com/docs/7.1.x/HTML/Content/Authoring/SQLReferenceManual/Functions/TimeSeries/CONDITIONAL_CHANGE_EVENTAnalytic.htm" target="_blank">conditional change event</a>&nbsp;function "assigns an event window number to each row, starting from 0, and increments by 1 when the result of evaluating the argument expression on the current row differs from that on the previous row".</p>
<pre style="color: #333333; font-family: Consolas, 'Courier New'; font-size: 12.16px; line-height: 1.25em;" xml:space="preserve">CONDITIONAL_CHANGE_EVENT ( <em>expression </em>) OVER ( 
... [ <a id="14435" class="MCXref xref" style="color: #800080; text-decoration-line: none;" href="https://my.vertica.com/docs/7.1.x/HTML/Content/Authoring/SQLReferenceManual/Functions/Analytic/window_partition_clause.htm">window_partition_clause</a> ] 
... <a id="14436" class="MCXref xref" style="color: #800080; text-decoration-line: none;" href="https://my.vertica.com/docs/7.1.x/HTML/Content/Authoring/SQLReferenceManual/Functions/Analytic/window_order_clause.htm">window_order_clause</a>  )</pre>
<p>&nbsp;</p>
<p><span style="text-decoration: underline;"><strong>An example using the conditional_change_event function</strong></span></p>
<p>So let's say we have the following table CCE_demo:&nbsp;</p>


<p><img src="{{ site.baseurl }}/assets/images/sf_vertica_1.png" alt="sf_vertica_1"/></p>


<p>This table contains chronological visits by users using a single browser. There were 2 visits from uid=10137196, then 2 visits by uid=15479000 and then 2 more visits by uid=10137196. &nbsp;</p>
<p>We are interested in identifying each of these 3 groups of visits. A simple "GROUP BY uid" would result in 2 groups.</p>

    SELECT
        browser_id
        , browser_visit_start
        , uid
        , (conditional_change_event(uid) 
            over (partition by browser_id order by browser_visit_start asc) + 1) 
            as cluster_id
    FROM CCE_Demo


<p>The above SQL in Vertica would result in the following:&nbsp;</p>


<p><img src="{{ site.baseurl }}/assets/images/sf_vertica_2.png" alt="sf_vertica_2"/></p>

<p><span style="text-decoration: underline;"><strong><br />How we can do this without conditional_change_event function</strong></span></p>

<p>TLDR; we will use a series of <a href="https://docs.snowflake.com/en/sql-reference/functions-analytic" target=_blank>window functions</a>.</p>

<p>1) we can define the first element in each group of visits with cluster_start=1 (using the <a href="https://docs.snowflake.net/manuals/sql-reference/functions/lag.html" target="_blank">LAG</a>&nbsp;analytic function):</p>


    -- identify the first record in each group
    SELECT
        browser_id
        , browser_visit_start
        , uid
        , CASE
            WHEN
                LAG(uid) over (partition by browser_id 
                    order by browser_visit_start asc) IS NULL THEN 1
            WHEN
                LAG(uid) over (partition by browser_id 
                    order by browser_visit_start asc) != uid THEN 1
        ELSE 0
        END  as cluster_start
    FROM CCE_Demo



<p><img src="{{ site.baseurl }}/assets/images/sf_vertica_3.png" alt="sf_vertica_3"/></p>

<p>2) Identify the unique groups:</p>

    -- identify the first record in each group
    WITH a as (
        SELECT
            browser_id
            , browser_visit_start
            , uid
            , CASE
                WHEN
                    LAG(uid) over (partition by browser_id 
                        order by browser_visit_start asc) IS NULL THEN 1
                WHEN
                    LAG(uid) over (partition by browser_id 
                        order by browser_visit_start asc) != uid THEN 1
            ELSE 0
            END  as cluster_start
        FROM CCE_DEMO
    )
    -- get the unique groups
    SELECT
            browser_id
        , browser_visit_start
        , uid
            , cluster_start
        , ROW_NUMBER() over (partition by browser_id 
            order by browser_visit_start) as cluster_id
    FROM a
    WHERE  cluster_start=1
    ORDER BY browser_id, browser_visit_start, uid 




<p><img src="{{ site.baseurl }}/assets/images/sf_vertica_4.png" alt="sf_vertica_4"/></p>

<p>3) We can create date windows for each cluster since we know when each one starts:</p>

    -- identify the first record in each group
    WITH a as (
        SELECT 
            browser_id
            , browser_visit_start
            , uid 
            , CASE 
                WHEN 
                    LAG(uid) over (partition by browser_id 
                        order by browser_visit_start asc) IS NULL THEN 1 
                WHEN
                    LAG(uid) over (partition by browser_id 
                        order by browser_visit_start asc) != uid THEN 1 
            ELSE 0 
            END  as cluster_start
        FROM CCE_Demo
    ),
    -- get the unique groups
    b as (
        SELECT 
                browser_id
            , browser_visit_start
            , uid 
                , cluster_start
            , ROW_NUMBER() over (partition by browser_id 
                order by browser_visit_start) as cluster_id
        FROM a
        WHERE  cluster_start=1
        ORDER BY browser_id, browser_visit_start, uid  
    )
    -- assign end dates to the groups, set last group's end date to 2100-01-01 
    SELECT
            browser_id
        , browser_visit_start
        , uid
        , cluster_id
        , COALESCE(
                LEAD(browser_visit_start) over
                (partition by browser_id order by browser_visit_start asc),
                '2100-01-01') as end_date
            FROM b

<p><img src="{{ site.baseurl }}/assets/images/sf_vertica_5.png" alt="sf_vertica_5"/></p>

<p>4) Use the date ranges (between browser_visit_start and end_date) to designate the group:</p>

    -- identify the first record in each group
    WITH a as (
        SELECT 
            browser_id
            , browser_visit_start
            , uid 
            , CASE 
                WHEN 
                    LAG(uid) over (partition by browser_id 
                        order by browser_visit_start asc) IS NULL THEN 1 
                WHEN 
                    LAG(uid) over (partition by browser_id 
                        order by browser_visit_start asc) != uid THEN 1 
            ELSE 0 
            END  as cluster_start
        FROM CCE_Demo
    ),
    -- get the unique groups
    b as (
        SELECT 
                browser_id
            , browser_visit_start
            , uid 
                , cluster_start
            , ROW_NUMBER() over (partition by browser_id 
                order by browser_visit_start) as cluster_id
        FROM a
        WHERE  cluster_start=1
        ORDER BY browser_id, browser_visit_start, uid  
    ),
    -- assign end dates to the groups, set last group's end date to 2100-01-01 
    c as
    (
    SELECT 
            browser_id
        , browser_visit_start
        , uid
        , cluster_id
        , COALESCE(
                LEAD(browser_visit_start) over 
                (partition by browser_id order by browser_visit_start asc),
                '2100-01-01') as end_date 
            FROM b
    )
    -- use the window between browser_visit_start and end_date to assign the correct group to each record
    SELECT
        d.browser_id
        , d.browser_visit_start
        , d.uid
        , cluster_id
    FROM CCE_Demo d
    LEFT OUTER JOIN c
        ON d.browser_id=c.browser_id
        AND d.uid=c.uid
        AND d.browser_visit_start>=c.start_date
        AND d.browser_visit_start<c.end_date
    ORDER BY 
        d.browser_id, d.browser_visit_start 

<p><img src="{{ site.baseurl }}/assets/images/sf_vertica_6.png" alt="sf_vertica_6"/></p>


<p>&nbsp;&nbsp;</p>
<p><span style="text-decoration: underline;"><strong>Other things we could have done</strong></span></p>
<p>The real data we have in our database that this example was based off of a table with 250 million rows. So taking advantage of scalable query processing in an MPP database was desirable.</p>
<p>1) Build a custom user defined function (using javascript and a FOR loop).<br />2) Export it to a language like Python or Java and use a FOR loop.&nbsp;</p>
