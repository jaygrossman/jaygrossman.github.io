---
layout: post
title:  "Apache Superset Test Drive"
author: jay
tags: [  data engineering, superset, data visualization, open source] 
image: assets/images/headers/superset.png
description: "Apache Superset Test Drive"
featured: false
hidden: false
comments: false
---

<p>I have lately been playing with some commercial BI &amp; Dashboard tools. There is a certainly quite broad range when it comes to features, price, scalability, administration capabilities, how they can access data, and set up complexity. For a good sized enterprise (with 100+ users), some of these solutions can run you several hundred thousand dollars per year.</p>
<p>As part of my diligence, I felt like I need to look at the best of breed Open Source offering - Apache Superset created by AirBNB. So I went to&nbsp;<a href="https://superset.incubator.apache.org/" target="_blank">https://superset.incubator.apache.org/</a>&nbsp;to check out the features and documentation. I was impressed, so I gave it a whirl.</p>

<p><strong>Installation</strong></p>
<p>They had some <a href="https://superset.incubator.apache.org/installation.html#start-with-docker" target="_blank">pretty simple instructions</a> to get it running on Docker:</p>

    git clone https://github.com/apache/incubator-superset/
    cd incubator-superset/contrib/docker
    docker-compose run --rm superset ./docker-init.sh
    # you can run this command everytime you need to start superset now:
    docker-compose up







<p>This created 3 docker images (superset, postgres to hold the configuration, redis to hold cached data):</p>

    > docker ps

    CONTAINER ID       IMAGE              COMMAND                  CREATED             STATUS                PORTS                    NAMES
    dca6c22fa844 superset_superset "/entrypoint.sh" 6 days ago Up 4 days (healthy) 0.0.0.0:8088->8088/tcp superset_superset_1 
    f22e0ca50545 postgres:10 "docker-entrypoint.s…" 6 days ago Up 4 days 0.0.0.0:5432->5432/tcp superset_postgres_1 
    5ec0565adb58 redis:3.2 "docker-entrypoint.s…" 6 days ago Up 4 days 0.0.0.0:6379->6379/tcp superset_redis_1 
 


<p>In a browser, I went to http://localhost:8088 and saw this after logging in:</p>

<p><img src="{{ site.baseurl }}/assets/images/superset_testdrive_1.png" alt="" /></p>


<p><strong>The Use Case - Marc Lore's "Big 5" start up metrics</strong></p>
<p>I personally get much more out of learning or demo'ing some new tech when I can use it to find a solution to one of my (or my friend's or employer's) real life problems. So I wanted to build a dashboard in Superset that would help one of my personal projects.</p>
<p><a href="https://www.linkedin.com/in/marclore/" target="_blank">Marc Lore</a> has founded some B2C companies that have had successful exits (thepit.com, diapers.com, jet.com) and is now running commerce for Walmart. I follow him on LinkedIn and saw his post this week:&nbsp;</p>

<p><img src="{{ site.baseurl }}/assets/images/lore_quote.png" alt="" /></p>

<p><br />So I decided I would use Superset to show these metrics for my friend's company that I will not disclose the name (over 2 years old, profitable, growing, completely bootstrapped).</p>
<p><span style="color: #ff0000;"><em>In this blog entry, I will walk you through the ease of use of Superset's functionality by connecting to a datasource and building a visualization for the first item on Marc's list - tracking NPS.</em></span></p>
<p><span style="text-decoration: underline;"><strong>Setting up the data</strong></span></p>
<p>The first thing you need to do is set up connections to our data sources. Superset uses the SQLAlchemy&nbsp;python library for interfacing with database. If you are using a database other than sqlite, you may need to install the correct python library so that SQLAlchemy&nbsp;can reach your database as shown at <a href="https://superset.incubator.apache.org/installation.html#database-dependencies" target="_blank">https://superset.incubator.apache.org/installation.html#database-dependencies</a>.&nbsp;</p>
<p>I ran the following to install the mysql library on the docker running superset (calling the container_id from the <em>docker ps</em> command earlier:</p>

    docker exec -it dca6c22fa844 bash 
    pip install pymssql --user

<p>In the top menu I went to SOURCES &gt; DATABASES. I set up a Database entry called "dw" that connected to mysql database:</p>

<p><img src="{{ site.baseurl }}/assets/images/superset_testdrive_2.png" alt="" /></p>

<p><br />Superset allows us to then set up Tables that point at either a single table or view in whatever Database entries you create. You navigate to this by choosing the SOURCES &gt; TABLES from the top menu.&nbsp; Then I set up a table using the "dw" database pointing at a view in mysql called user_nps:</p>

<p><img src="{{ site.baseurl }}/assets/images/superset_testdrive_3.png" alt="" /></p>

<p><br />Superset will look in the database to define the columns - default names, types, and if we treat them as dimensions can be overridden. The user_nps table in our mysql database contained 3 fields:</p>
<table style="border: 1px solid #000000;" border="0" cellspacing="3" cellpadding="3">
<tbody>
<tr>
<td style="padding: 3px;"><strong>Field</strong></td>
<td style="padding: 3px;"><strong>Type</strong></td>
<td style="padding: 3px;"><strong>Description</strong></td>
</tr>
<tr>
<td>survey_date&nbsp; &nbsp;&nbsp;</td>
<td>timestamp&nbsp; &nbsp;&nbsp;</td>
<td>the date time of the survey</td>
</tr>
<tr>
<td>score</td>
<td>int</td>
<td>the score the user provided between 1 to 10</td>
</tr>
<tr>
<td>uid</td>
<td>varchar</td>
<td>the user's unique identifier</td>
</tr>
</tbody>
</table>
<p><br />I needed to understand categorize each user score as either a promoter, detractor, or passive in order to calculate an NPS score, as defined in&nbsp;<a href="https://customergauge.com/blog/how-to-calculate-the-net-promoter-score/" target="_blank">https://customergauge.com/blog/how-to-calculate-the-net-promoter-score/</a>. I could just show the calculated score per month, but I would prefer to show the breakdowns to get a better sense of the distributions of what my users think of this service.</p>
<p>A Table in Superset allows me to create calculated fields (much like you could do in a sql view). In the table below, I have created 3 additional calculated fields:</p>

<p><img src="{{ site.baseurl }}/assets/images/superset_testdrive_4.png" alt="" /></p>

<p><br />Here is the logic I used for the 3 calculated fields:</p>
<div class="highlight-default notranslate" style="box-sizing: border-box; border: 1px solid #e1e4e5; padding: 0px; overflow-x: auto; margin: 1px 0px 24px; color: #404040; font-family: Lato, proxima-nova, 'Helvetica Neue', Arial, sans-serif; font-size: 16px; background-color: #fcfcfc;">
<div class="highlight" style="box-sizing: border-box; background: #eeffcc; border: none; padding: 0px; overflow-x: auto; margin: 0px;">
<pre style="box-sizing: border-box; font-family: Consolas, 'Andale Mono WT', 'Andale Mono', 'Lucida Console', 'Lucida Sans Typewriter', 'DejaVu Sans Mono', 'Bitstream Vera Sans Mono', 'Liberation Mono', 'Nimbus Mono L', Monaco, 'Courier New', Courier, monospace; font-size: 12px; margin: 0px; padding: 12px; overflow: auto; line-height: normal;">survey_year = YEAR(survey_date)<br />survey_month = MONTH(survey_date)<br />nps_type = CASE
    WHEN  score&lt;7 THEN 'detractor'
    WHEN  score&gt;8 THEN 'promoter'
    ELSE 'passive'
 END </pre>
</div>
</div>
<p><span style="text-decoration: underline;"><strong>Building Visualizations</strong></span></p>
<p>In Superset, you can make Charts and you can insert those charts into Dashboards.</p>
<p>Once I have a Table created, making a chart is somewhat straight forward. I did the following:</p>
<ol>
<li>Clicked on the NEW button in top menu option and chose the "Chart" option.</li>
<li>I chose the table I was interested in using - workspace.user_nps.</li>
<li>I was taken to the screen shown below and I filled out the options on the left menu:<br />- Superset provides <a href="https://superset.incubator.apache.org/gallery.html" target="_blank">many types of visualization options</a>, so I choose Bar Chart. <br />- I provided the date range I wanted my chart to consider.<br />- I chose the metrics, filter criteria, X-axis (Series&nbsp;+Breakdowns)&nbsp;and Y-axis (Metrics)</li>
<li>I hit the "Run Query" button to visualize it and the "Save" button to save the chart for future use.</li>
</ol>

<p><img src="{{ site.baseurl }}/assets/images/superset_testdrive_5.png" alt="" /></p>

<p><br />As part of the command to save the Chart (show below), it provided the option to create a new Dashboard I called "Startup_Metrics":</p>


<p><img src="{{ site.baseurl }}/assets/images/superset_testdrive_6.png" alt="" /></p>

<p><strong><br /></strong>From the DASHBOARD top menu item, I can view any of the dashboards I have created. Below is a screenshot of the Startup_Metrics dashboard:</p>

<p><img src="{{ site.baseurl }}/assets/images/superset_testdrive_7.png" alt="" /></p>

<p><br />There are many features for Dashboards. I can:</p>
<ul>
<li>Control the layout of the dashboard. I can add columns / rows / tabs, resize charts, and can even add markdown.</li>
<li>Limit who may access or edit it (based on their roles).</li>
<li>Set a cache limit to determine how often the data is to be refreshed from the source database.</li>
<li>Create a permalink to send or email it to people.&nbsp;</li>
<li>Explore any chart (click on the the 3 stacked dots on the upper right corner), allowing folks with permissions to view how it was created and make their own version.</li>
</ul>
<p><strong>Limitations</strong></p>
<ul>
<li>Since it leverages SQLAlchemy, there is a limitation that it does not provide the capability to join data across tables. This means that you must create a view with all the data elements needed for the Charts you want to build.</li>
<li>Superset supports pretty simple aggregation, the complex and mulit-level groupings and calculations are probably better down as a view in a source system</li>
<li>If you try to return more than a few thousand results to the browser, the performance becomes pretty slow.</li>
<li>Superset does not have the ability to subscribe to a Chart or Dashboard so it would be emailed to a user on a specified interval, an important feature in most of the commercial dashboard solutions.</li>
</ul>
<p><strong>Conclusions</strong></p>
<p><strong></strong>This doesn't have all the bells and whistles of Looker or Tableau. If you have a big enterprise that you want to launch self service reporting for hundreds of non-technical folks, this is probably not the best fit. The limitations above are things that would cause pain when a company scales to many simultaneous users.</p>
<p>However, there are pros I love about it:</p>
<ul>
<li>Superset is free! It can provide a nice start for smaller companies and personal projects.&nbsp;</li>
<li>The pre-canned docker-compose git project makes set up super easy. I was surprised how well it ran on my laptop.</li>
<li>The learning curve was low, I hooked up to my database and built my first dashboard with 5 charts in under 2 hours.</li>
<li>This thing has had different names, but it is over 3 years old and has went through lots of nice iteration.</li>
<li>It's written in Python, which a large audience of data folks enjoy using.&nbsp; Since it is open source, we can fork and extend it as we please.</li>
</ul>