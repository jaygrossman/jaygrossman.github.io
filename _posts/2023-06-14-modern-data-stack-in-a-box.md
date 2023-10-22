---
layout: post
title:  "Modern Data Stack in a box (all Open Source)"
author: jay
tags: [ data engineering, data warehosue, dbt, hackathon, duckdb, airbyte, metabase, open source  ] 
image: assets/images/headers/hackathon_modern_data_stack.png
description: "Modern Data Stack in a box (all Open Source)"
featured: false
hidden: false
comments: false
---

<p>This week I participated in a hackathon for fun with a couple of friends. There were 3 rules for each of us:</p>

<ol>
<li>Upgrade something you are unhappy with in a side project</li>
<li>Try some new piece of tech</li>
<li>Document what you did</li>
</ol>

<h3>My project</h3>

<p>Over the past 10 or so years, I have had a lot of fun running&nbsp;<a href="http://www.collectz.com/" target="_blank">CollectZ</a>&nbsp;- Research and Arbitrage Platform for collectibles categories.&nbsp;</p>
<p>TL;DR - The platform informs what undervalued collectibles I should consider buying and when + where + at what price I should sell my inventory.</p>
<p>If you are trying to run arbitrage (exploiting inefficiencies between marketplaces) effectively with physical (non-liquid) assets, it is rather important to be able to have a pretty diverse data set and be able do analysis + reporting + run models across a variety of features.&nbsp;</p>
<h3>My problem</h3>


<p>As I went along building out my platform, I had data sitting in different diverse repositories and in many formats. Some of the exploration and reporting tools I cobbled together over the years suffered because they were:</p>
<ul>
<li>fragile to keep running (things broke unexpectedly)</li>
<li>hard to troubleshoot for data lineage issues</li>
<li>performing slowly with more advanced queries (based off relational databases and file stores)</li>
<li>labor intensive to extend&nbsp;</li>
<li>lacking the general features I wanted&nbsp;</li>
</ul>

<p>The tech debt became more than just noticeable, as it was impacting the effectiveness of the platform and more importantly effectiveness of my trades.&nbsp;</p>
<h3>Requirements for something better</h3>
<p>Before I go about building anything, it is a really good idea to write down some requirements.&nbsp; &nbsp;</p>
<p>When thinking about my reporting and data exploration needs for CollectZ, I had some requirements:</p>
<ul>
<li>I want a fully open source (free) stack that I can configure to run locally on my laptop and could be run in a cloud provider (likely AWS).</li>
<li>I want to use dbt to build my models</li>
<li>I want relatively a fast centralized data store (preferably columnar based)</li>
<li>I want a way to keep the reporting sync'd regularly from production / source systems</li>
<li>I want an off the shelf tool for self service data exploration and BI</li>
</ul>
<h3>The Data</h3>
<p>For this exercise, I am using a subsection of recent transaction and valuation data for Sports Cards category.&nbsp;</p>
<p>I have exported data from 7 tables into .csv files and posted them to S3. Below is a relationship diagram source data model we will be working with:</p>


<p><img src="{{ site.baseurl }}/assets/images/data_stack_1.png" alt="" /></p>

<table style="width: 608.8px;" border="1" cellpadding="3">
<tbody>
<tr>
<td style="padding: 3px;"><strong>Table</strong></td>
<td style="padding: 3px;"><strong>Description</strong></td>
<td style="padding: 3px;"><strong>Record Count</strong></td>
</tr>
<tr>
<td style="padding: 3px;">Item</td>
<td style="padding: 3px;">Reference information about a sports card in our catalog</td>
<td style="padding: 3px;">116,459&nbsp;</td>
</tr>
<tr>
<td style="padding: 3px;">ItemValue</td>
<td style="padding: 3px;">Weekly calculated values for each item in different conditions</td>
<td style="padding: 3px;">5,746,066</td>
</tr>
<tr>
<td style="padding: 3px;">CardSet</td>
<td style="padding: 3px;">The sport, year and name for each set associated with the card</td>
<td style="padding: 3px;">691</td>
</tr>
<tr>
<td style="padding: 3px;">Sport</td>
<td style="padding: 3px;">The sport associated with the card set</td>
<td style="padding: 3px;">4</td>
</tr>
<tr>
<td style="padding: 3px;">ItemTransaction</td>
<td style="padding: 3px;">Recent sales for selected items</td>
<td style="padding: 3px;">3,175,362</td>
</tr>
<tr>
<td style="padding: 3px;">ActiveItem</td>
<td style="padding: 3px;">Items available for sale</td>
<td style="padding: 3px;">4,489,085</td>
</tr>
<tr>
<td style="padding: 3px;">Source</td>
<td style="padding: 3px;">Sources of data for ItemTransaction and ActiveItem</td>
<td style="padding: 3px;">12</td>
</tr>
</tbody>
</table>
<br>


<table style="width: 100%; border-color:red;" border="1" cellpadding="5">
<tr>
<td>
   <strong><i>Please Note:</i></strong><br>
    I am not able to share actual Collectz data on my blog and how we get this (let's assume there are lots of fun data engineering pipelines at work here).
    </td>
</tr>
</table>

<br>

<h3>Solution Architecture</h3>
<p>I have run data teams that build out data products for my day job, so I knew I could streamline and modernize on some best practice tooling.&nbsp;</p>
<p>The components of my stack:</p>

<p><img src="{{ site.baseurl }}/assets/images/data_stack_2.png" alt="" /></p>

<ul>
<li><a href="https://duckdb.org/" target="_blank">DuckDB</a>&nbsp;- our local data warehouse</li>
<li><a href="https://airbyte.com/" target="_blank">Airbyte</a>&nbsp;- for populating data into DuckDB</li>
<li><a href="https://www.getdbt.com/" target="_blank">dbt</a>&nbsp;- for building models for analysis</li>
<li><a href="https://www.metabase.com/" target="_blank">Metabase</a>&nbsp;- BI tool</li>
<li><a href="https://jupyter.org/">Jupyter</a>&nbsp;- for ad hoc analysis via Notebooks</li>
</ul>
<h4>DuckDB</h4>
<p>I have a good amount of experience with commercial cloud data warehouses (Snowflake, BigQuery, DataBricks). While they are great products, they are all pretty expensive and I don't have budget for a personal project like CollectZ. I wanted something free + open source that I could run locally for development and later in AWS for production.</p>
<p>My first thought was to go with Postgres, but I wasn't overly excited to manage a relational database (index, keys, etc.) and I like columnar databases for analytics. I also thought about Druid, but that would involve some overhead to set up and surprisingly doesn't yet have dbt integration.&nbsp;</p>
<p>My friend Rob told me about how DuckDB offered much of the functionality I liked in Snowflake, but it was super easy to manage and would run locally on my laptop.</p>
<p>DuckDB is an in-process SQL OLAP database management system. AND I really like the idea of their tagline:</p>
<p><em>All the benefits of a database, none of the hassle.</em></p>
<p>After playing around with it, I found the main points and features of DuckDB?</p>
<ul>
<li>Simple, clean install, with very little overhead.&nbsp;</li>
<li>Feature-rich with SQL, plus CSV and Parquet support.&nbsp;</li>
<li>In-memory option, high-speed, parallel processing.&nbsp;</li>
<li>Open-source.&nbsp;</li>
</ul>
<p>DuckDB isn&rsquo;t meant to replace MySQL, Postgres, and the rest of those relational databases. In fact, they tell you DuckDB is very BAD at &ldquo;High-volume transactional use cases,&rdquo; and &ldquo;Writing to a single database from multiple concurrent processes.&rdquo;</p>
<p>This 10 minute Youtube video from <a href="https://www.linkedin.com/in/ryguyrg/" target="_blank">Ryan Boyd</a> does a good job with more background on DuckDB:<br /> <br /> <iframe title="YouTube video player" src="https://www.youtube.com/embed/5GewuzicW7k" frameborder="0" width="560" height="315"></iframe></p>
<p><strong>Installation Instructions:</strong></p>
<p>Reference from <a href="https://duckdb.org/docs/installation/" target="_blank">https://duckdb.org/docs/installation/</a></p>
<ol>
<li>brew install duckdb</li>
</ol>

<h4>Airbyte</h4>
<p>I have implemented&nbsp;<a href="https://www.fivetran.com/" target="_blank">FiveTran</a>&nbsp;at 2 of my day jobs (ElysiumHealth and Luma), and it is a very easy to configure tool to set up pipelines to sync data from various sources into a data warehouse. Overall it works well if you do not have low latency requirements (it is great for hourly syncs), but it is volume based and can be quite expensive once you go past syncing 500,000 records in a month on their&nbsp;<a href="https://www.fivetran.com/pricing/free-plan" target="_blank">new free tier offering</a>.</p>
<p>For this project, I wanted to try the closest Open Source equivalent I could find - Airbyte.&nbsp;</p>


<table style="width: 100%; border-color:red;" border="1" cellpadding="5">
<tr>
<td>
   <strong><i>Please Note:</i></strong><br>
    The latest version or Airbyte 0.443, only supports DuckDB 0.6.1 (version 39). All the other tools in this post I am configuring are using DuckDB 0.8.0 (which launched within the past month), so I would not be able to use Airbyte in this full stack until its DuckDB version support is aligns with Metabase and dbt.&nbsp;&nbsp;<br /><br />Airbyte is a valuable tool for syncing data (I also tried it syncing to Postgres) and I feel that is worthwhile to share my learnings of how I would/will use it.
    </td>
</tr>
</table>
<br>

<p><strong>Installation Instructions</strong></p>
<p>Reference from&nbsp;<a href="https://docs.airbyte.com/quickstart/deploy-airbyte/" target="_blank">https://docs.airbyte.com/quickstart/deploy-airbyte/</a></p>
<ol>
<li>Install Docker on your workstation (see instructions). Make sure you're on the latest version of docker-compose.<br /><br /></li>
<li>Run the following commands in your terminal:<br /><br />git clone https://github.com/airbytehq/airbyte.git<br />cd airbyte<br />./run-ab-platform.sh<br /><br /></li>
<li>Once you see an Airbyte banner, the UI is ready to go at http://localhost:8000<br />By default, to login you can use: username=airbyte and password=password</li>
</ol>
<p>When I opened up Docker Desktop, you can see airbyte has 12 dockers it has running:</p>

<p><img src="{{ site.baseurl }}/assets/images/data_stack_3.png" alt="" /></p>

<p><strong><br /></strong></p>
<p><strong>Configuring a Pipeline in Airbyte&nbsp;</strong></p>
<p>1) The first step is to set up a Source. In this case I am setting up a "File" source that can read from sport.csv in my S3 bucket. I will need to provide my AWS creds as shown below:</p>

<p><img src="{{ site.baseurl }}/assets/images/data_stack_4.png" alt="" /></p>

<p>2) Next we will need to set up a destination where we can write the data. This&nbsp;<a href="https://docs.airbyte.com/integrations/destinations/duckdb/" target="_blank">link</a>&nbsp;explains how to update the .env file so that a local directory on my machine will be mounted within the airbyte-server docker image.</p>

<p><img src="{{ site.baseurl }}/assets/images/data_stack_5.png" alt="" /></p>

<p>3) We will need to configure a connection that will schedule running syncs from our source to our destination:</p>

<p><img src="{{ site.baseurl }}/assets/images/data_stack_6.png" alt="" /></p>

<p><br />4) We see the result of the connection syncing data to my destination:&nbsp;</p>

<p><img src="{{ site.baseurl }}/assets/images/data_stack_7.png" alt="" /></p>

<p>5) I checked the DuckDB database and saw the following data synced over:&nbsp;</p>

<p><img src="{{ site.baseurl }}/assets/images/data_stack_8.png" alt="" /></p>

<p>The _airbyte_data column in the screenshot above contains json representing the data in the csv file. This would need to be transformed into a relational format for reporting via a BI tool (such as metabase).</p>

<h4>dbt support for DuckDB</h4>

<p>dbt is a popular tool for allowing developers and analysts to create data models in SQL (and now Python), managing graph dependencies and supporting tests. I have been a user of dbt since 2017 when my team at Rent the Runway first starting use it to standardize large parts of our pipelines.&nbsp;</p>
<p><strong>Installation + Set Up Instructions:</strong></p>
<p>Reference from&nbsp;<a href="https://github.com/jwills/dbt-duckdb" target="_blank">https://github.com/jwills/dbt-duckdb</a>:</p>


<p>1) Install dbt and dbt-duckdb:</p>

    pip3 install dbt-core
    pip3 install dbt-duckdb

<p>2) Configure the ~/.dbt/profile.yml file with the following:</p>

    collectz:
    target: dev
    outputs:
        dev:
        type: duckdb
        path: /path_to_file/collectz.db
        threads: 4

<p>3) Create a new directory&nbsp; and create new dbt project:</p>

    mkdir dbt
    cd dbt
    dbt init

<p>4) I entered the name for your project&nbsp;<em>collectz</em>&nbsp;when prompted</p>



<p><strong>Building models in dbt</strong>&nbsp;</p>
<p>This post assumes you have working knowledge of how dbt works. If you do not, their&nbsp;<a href="https://docs.getdbt.com/quickstarts/manual-install?step=1" target="_blank">quick start</a>&nbsp;is helpful.</p>
<p>Since I could not use Airbyte due to version incompatibilities mentioned above, I made simple models that created VIEWS on top of local copies of our 7.csv files.</p>
<p>Example is _raw_active_item.sql (it uses the read_csv_auto function to read in a csv) as shown below:</p>

    FROM read_csv_auto('/path_to _data_file/Active_Item.csv')


<table style="width: 100%; border-color:red;" border="1" cellpadding="5">
<tr>
<td>
   <strong><i>Please Note:</i></strong><br>
    I tried to use&nbsp;<em>dbt seed</em>&nbsp;which would make the larger datasets persisted as tables, but it was pretty inefficient to copy over millions of rows when DuckDB is designed to efficiently process data in files at request time. I found the read_csv_auto() function performed well on data sets with 8M+ rows.
    </td>
</tr>
</table>
<br>

<p>Now that I have "raw" tables, I created SQL files that would be my BI reporting schema containing the appropriate transformations and aggregations.</p>

<h4>Metabase</h4>
<p>I have used a bunch of different BI / Dashboard tools over the years. I feel like Looker is probably the best of the commercial tools, but set up is time consuming and it has become really expensive. Metabase is an open source alternative that I have found really easy to set up, easy to use, and has some of the advanced features I would want.&nbsp;</p>
<p>I found Maz's 6 minute&nbsp;<a href="https://www.metabase.com/demo" target="_blank">Demo video</a>&nbsp;posted below walks you through the basics from an analyst perspective doing some data exploration and then incorporating it into dashboards:&nbsp;&nbsp;</p>
<iframe src="https://www.loom.com/embed/97107a8c8b054cd8bfa8dacb297d2c04" frameborder="0" width="500" height="300"></iframe>
<p>&nbsp;</p>
<p><strong>Installation Instructions:</strong></p>
<ol>
<li>install java - I used this link to install Eclispe Temurin:<br /><a href="https://www.metabase.com/docs/latest/installation-and-operation/running-the-metabase-jar-file#:~:text=of%20JRE%20from-,Eclipse%20Temurin,-with%20HotSpot%20JVM">https://www.metabase.com/docs/latest/installation-and-operation/running-the-metabase-jar-file#:~:text=of%20JRE%20from-,Eclipse%20Temurin,-with%20HotSpot%20JVM<br /><br /></a>
<p class="p1" style="margin: 0px; font-variant-numeric: normal; font-variant-east-asian: normal; font-variant-alternates: normal; font-kerning: auto; font-optical-sizing: auto; font-feature-settings: normal; font-variation-settings: normal; font-stretch: normal; font-size: 11px; line-height: normal; font-family: Menlo; color: #000000;"><span class="s1" style="font-variant-ligatures: no-common-ligatures;">% java --version</span></p>
<p class="p1" style="margin: 0px; font-variant-numeric: normal; font-variant-east-asian: normal; font-variant-alternates: normal; font-kerning: auto; font-optical-sizing: auto; font-feature-settings: normal; font-variation-settings: normal; font-stretch: normal; font-size: 11px; line-height: normal; font-family: Menlo; color: #000000;"><span class="s1" style="font-variant-ligatures: no-common-ligatures;">openjdk 17.0.7 2023-04-18</span></p>
<p class="p1" style="margin: 0px; font-variant-numeric: normal; font-variant-east-asian: normal; font-variant-alternates: normal; font-kerning: auto; font-optical-sizing: auto; font-feature-settings: normal; font-variation-settings: normal; font-stretch: normal; font-size: 11px; line-height: normal; font-family: Menlo; color: #000000;"><span class="s1" style="font-variant-ligatures: no-common-ligatures;">OpenJDK Runtime Environment Temurin-17.0.7+7 (build 17.0.7+7)</span></p>
<p class="p1" style="margin: 0px; font-variant-numeric: normal; font-variant-east-asian: normal; font-variant-alternates: normal; font-kerning: auto; font-optical-sizing: auto; font-feature-settings: normal; font-variation-settings: normal; font-stretch: normal; font-size: 11px; line-height: normal; font-family: Menlo; color: #000000;"><span class="s1" style="font-variant-ligatures: no-common-ligatures;">OpenJDK 64-Bit Server VM Temurin-17.0.7+7 (build 17.0.7+7, mixed mode)<br /><br /></span></p>
</li>
<li>Download the&nbsp;<a href="https://www.metabase.com/docs/latest/installation-and-operation/running-the-metabase-jar-file#:~:text=2.%20Download%20Metabase-,Download%20the%20JAR%20file%20for%20Metabase%20OSS,-." target="_blank">JAR file for Metabase OSS</a><br /><br /></li>
<li>Download the&nbsp;<a href="https://github.com/AlexR2D2/metabase_duckdb_driver/releases/tag/0.1.6#:~:text=duckdb.metabase%2Ddriver.jar" target="_blank">DuckDB driver for metabase</a>&nbsp;<br /><br /></li>
<li>run the following commands from command line to create the metabase directory and move the jar files:<br /><br />mkdir ~/metabase<br />mv ~/Downloads/metabase.jar ~/metabase<br />cd ~/metabase<br />mv ~/Downloads/duckdb.metabase-driver.jar ~/metabase/plugins&nbsp;<br /><br />
</li>
<li>Run the metabase jar file with the following commands:<br /><br />cd ~/metabase<br />java -jar metabase.jar<br /><br /></li>
<li>got to http://localhost:3000 in your browser</li>
</ol>

<table style="width: 100%; border-color:red;" border="1" cellpadding="5">
<tr>
<td>
   <strong><i>Please Note:</i></strong><br>
    To productionize metabase, we will want to review&nbsp;<a href="https://www.metabase.com/docs/latest/installation-and-operation/running-the-metabase-jar-file" target="_blank">this page</a>&nbsp;with instructions how to:<br>
&nbsp; &nbsp; 1. run metabase with a postgres as the data store instead of using the default h2 file.<br>
&nbsp; &nbsp; 2.run the java process as a service using systemd<
    </td>
</tr>
</table>
<br>

<p><strong>Data Configuration in Metabase:</strong></p>


<p>Once you log into Metabase's Admin section, you set up a connection to register your DuckDB database as shown below:</p>

<p><img src="{{ site.baseurl }}/assets/images/data_stack_9.png" alt="" /></p>

<p>Under the Data Model area, you can define elements of your data model from any registered databases shown below. Here you can set which tables are visible and how tables join together (similar to primary-foreign keys).&nbsp; &nbsp;&nbsp;</p>

<p><img src="{{ site.baseurl }}/assets/images/data_stack_10.png" alt="" /></p>

<h3>The Deliverable</h3>
<p>So I made some widgets and dashboards that make it easy enough for me to explore my data, quickly see some overviews of products with dashboards, and troubleshoot issues.&nbsp;&nbsp;</p>
<p><strong>Data Exploration Example - Finding undervalued items:</strong></p>
<p>Since DuckDb doesn't have a nice web based SQLRunner like Snowflake or BigQuery, Metabase made it really easy for me to write a SQL query joining across tables with minimal aggregation + filtering to visualize results.</p>
<p>It took me about 2 minutes to write the query below that returns underpriced items:</p>


<p><img src="{{ site.baseurl }}/assets/images/data_stack_11.png" alt="" /></p>

<p><strong><br />Item Search and Dashboard:</strong></p>
<p>I was able to make a search screen where I can search by the year, sport and wildcard on the card's Description. Below I am searching for cards of Nolan Ryan from 1981 (the links take me to card details report):</p>



<p><img src="{{ site.baseurl }}/assets/images/data_stack_12.png" alt="" /></p>

<p>The card details page below shows me:</p>
<ul>
<li>Details about the card</li>
<li>If I have it in my inventory, where I am selling it, the condition and the price</li>
<li>Time Series of&nbsp;Values (for graded and ungraded categories)</li>
<li>Time Series of Volumes of sales with pricing</li>
<li>Time Series of Volume of the item available (buy it now format)</li>
<li>Where the item is available and for how much</li>
</ul>



<p><img src="{{ site.baseurl }}/assets/images/data_stack_13.png" alt="" /></p>

<h3>Thoughts</h3>
<h4>For me, this hackathon was a great success.<strong><br /><br /></strong></h4>
<ul>
<li>Metabase had made it easy to more effectively operationalize parts of this business with enhanced reporting and alerting.</li>
<li>I now have a much easier way to answer my ad hoc and research questions quickly.</li>
<li>I had the opportunity to play with a great new technology - DuckDB.&nbsp; And I got to integrate it with other products I like (dbt, metabase, airbyte).</li>
<li>This blog post does a decent job of documenting the problem, solution approach and some implementation details + learnings.</li>
</ul>

<h4>Other Learnings / Observations:<br /><strong><br /></strong></h4>

<ul>
<li>It is so much easier to manage running BI reporting off a columnar database vs. a relational database. DuckDB is pretty amazing for side projects if you can deal with the constraints.&nbsp;</li>
<li>I would consider adding in an orchestration layer with <a href="https://dagster.io/" target="_blank">Dagster</a>. I like the idea of having ingestion, transformation and stopping/starting services controlled by a consistent and testable process.</li>
<li>For ingestion, I also considered using the Singer/Meltano&nbsp;<a href="https://github.com/jwills/target-duckdb" target="_blank">target-duckdb</a>&nbsp;project.</li>
<li>I also played with <a href="https://www.rilldata.com/" target="_blank">Rill Data</a>. It is very cool for analyzing the structure of data files (csv, parquet, etc), but it can not read from DuckDB.</li>
</ul>
<h4>Inspiration from:</h4>

<ul>
<li><a href="https://medium.com/datamindedbe/use-dbt-and-duckdb-instead-of-spark-in-data-pipelines-9063a31ea2b5" target="_blank">Use dbt and Duckdb instead of Spark in data pipelines</a></li>
<li><a href="https://duckdb.org/2022/10/12/modern-data-stack-in-a-box.html" target="_blank">Modern Data Stack in a Box with DuckDB</a></li>
<li><a href="https://dagster.io/blog/duckdb-data-lake" target="_blank">Build a poor man&rsquo;s data lake from scratch with DuckDB</a></li>
</ul>
