---
layout: post
title:  "Snowflake's lateral flatten function on variant data type"
author: jay
categories: [ code ]
tags: [ json, snowflake ]
image: assets/images/headers/json_in_snowflake.png
description: "Snowflake's lateral flatten function on variant data type"
featured: false
hidden: false
comments: false
---

<p><a href="https://www.snowflake.net/" target="_blank">Snowflake</a> is a really interesting new data warehouse built on top of AWS. I like their architecture because they had the interesting idea to separate data storage (backed by small files on S3) and compute to run queries (EC2 instances running their API).</p>
<p>I inherited a project where we would store complex JSON in a string in a field as varchar(64000). Then we would use regex patterns to get the values we wanted from them. Sometimes these regexes would get really involved, yuck.</p>
<p><span style="text-decoration: underline;"><strong>The Variant data type</strong></span></p>
<p>Snowflake offers mechanism to store semi structured data in field that is easy to parse - the <a href="https://docs.snowflake.net/manuals/user-guide/semistructured-intro.html" target="_blank">Variant data type</a>.&nbsp;</p>
<p>In snowflake I have a table variant_demo with a single Variant field named json_data as shown below:</p>

<p><img src="{{ site.baseurl }}/assets/images/sf_variant.png" alt="sf_variant"/></p>

<p>If we were to click on the field, we could see the JSON elements as shown below:</p>

<p><img src="{{ site.baseurl }}/assets/images/sf_json_data.png" alt="sf_json_data"/></p>

<p>If I wanted to get the value of the "membership" field, I could write the following SQL:</p>

    SELECT json_data:membership::string as membership 
    FROM variant_demo;

<p><img src="{{ site.baseurl }}/assets/images/sf_flatten_1.png" alt="sf_flatten_1"/></p>

<p>Now what if I wanted to pull out the list associated with "products" and join them to our products table?</p>

<p><span style="text-decoration: underline;"><strong>Lateral Flatten Function</strong></span></p>

<p>Snowflake has this really cool function that allow us to normalize a row from a list from JSON attribute from variant field. The SQL below uses lateral flatten to take the items in the list from json_data:products make them their own dataset:</p>

    WITH p as (
    SELECT
        json_data
    FROM
        variant_demo
    )
    SELECT
        b.value::string as product_style
    FROM p,
        lateral flatten(input=> p.json_data:products) b  


<p><img src="{{ site.baseurl }}/assets/images/sf_flatten_2.png" alt="sf_flatten_2"/></p>

<p>Once you have a nice clean record set, we can join on data with other tables:</p>

    WITH p as (
        SELECT 
            json_data
        FROM
            variant_demo
    ) 
    SELECT 
        b.value::string as product_style
        , designer
        , list_price
    FROM p,
        lateral flatten(input=> p.json_data:products) b  
        INNER JOIN products prod  
            ON v.value::string=prod.style 



<p><img src="{{ site.baseurl }}/assets/images/sf_flatten_3.png" alt="sf_flatten_3"/></p>