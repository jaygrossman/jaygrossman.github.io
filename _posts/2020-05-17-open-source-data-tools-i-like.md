---
layout: post
title:  "Open Source Data Tools I like"
author: jay
tags: [ data, tools, framework, superset, dbt, debezium, great expectations, open source ] 
image: assets/images/headers/data_meet_up.jpg
description: "Open Source Data Tools I like"
featured: false
hidden: false
comments: false
---

<p>A few months ago I participated on a panel talking about something data related at a <a href="https://www.snowflake.com" target="_blank">Snowflake</a> user group.&nbsp;In the picture below, I am in the white button down shirt and blue jeans looking less excited than the guy in plaid shirt on the left.</p>

<p>Toward the end of the Q&amp;A session, the moderator asked the panelists what open source data tools do they use. I thought it would be nice to share my answers here as some of them were new to folks in the audience. These are all tools / frameworks my teams and i have experience with implementing:</p>
<hr />
<p><img src="{{ site.baseurl }}/assets/images/logos/dbt.png" alt="" /></p>

<p><strong>Site:</strong>&nbsp;<a href="https://www.getdbt.com/" target="_blank">https://www.getdbt.com<br /></a><strong>Tagline:</strong> dbt applies the principles of software engineering to analytics code, an approach that dramatically increases your leverage as a data analyst.<br /><strong>Tech:</strong>&nbsp;Written in python</p>
<p><strong>So what problem does it solve?</strong></p>
<p><strong></strong>It is common for data folks to need to take raw data from a myriad of sources, load into a data warehouse, and then need to:<br />- build new tables with aggregated data<br />- take snapshots of data for time series analysis.&nbsp;</p>
<p>As your data needs grow and you have more folks working on these types of tasks, you can start to see jobs with many steps daisy chaining one new aggregated table called by another. Understanding, testing, and debugging can get incredibly challenging (my teams faced this on a daily basis supporting hundreds of legacy aggregated tables/views). And it gets exponentially worse as subject matter experts leave your team/company.</p>
<p>This was such a pain point that my team was in the process of creating our framework to tackle these types of problems.</p>
<p><strong>What is it?</strong></p>
<p><strong></strong>dbt (data build tool) is a command line tool that enables data analysts and engineers to transform data in their warehouses more effectively.&nbsp;&nbsp;</p>
<p>Below is a picture of a modern data stack+pipeline - doing Extract, Load, Transform (known as ELT).&nbsp; dbt is handles the Transform step as shown below:</p>

<p><img src="{{ site.baseurl }}/assets/images/open_source_tools_1.png" alt="" /></p>

<p>The dbt framework essentially takes sql templates (jinja files) where define tables as {{[ref("table_name")]}}. So your code may&nbsp;</p>

<p><img src="{{ site.baseurl }}/assets/images/open_source_tools_2.gif" alt="" /></p>

<p>Once you run "dbt run" from the command line,&nbsp; the framework can figure out the order to build the aggregated tables (meaning build the tables that other downstream tables depend on). It even generates out a nice visual graphic of that dependency tree as part of the run sequence (shown below). This alone was a huge thing for us.&nbsp;</p>

<p><img src="{{ site.baseurl }}/assets/images/open_source_tools_3.png" alt="" /></p>

<p><br />dbt also allows us to define "tests" - SQL statements defined to validate some cases that you care about in the data of tables in the data warehouse.&nbsp; This is great to run on both source data (coming from upstream systems) as well as the dbt created aggregate data. This allowed us to find+monitor for all sorts of problems/inconsistencies in data from source systems that cost us so many hours of complex troubleshooting.</p>
<p>I found this this&nbsp;<a href="https://blog.getdbt.com/what--exactly--is-dbt-/%20" target="_blank">excellent blog post</a>&nbsp;in late 2017 and we a working proof concept within a week. It proved out many of our risk areas and it was quickly adopted by a variety of teams (you really need to know SQL to use it).</p>
<p>Fast forward 2+ years, dbt has added all sorts of excellent features (including their own IDE and cloud offering) and has grown significantly in adoption.</p>
<hr />

<p><img src="{{ site.baseurl }}/assets/images/logos/great_expectations.png" alt="" /></p>

<p><strong>Site:</strong>&nbsp;<a href="https://greatexpectations.io/" target="_blank">https://greatexpectations.io<br /></a><strong>Tagline:</strong> Great Expectations (GE) helps data teams eliminate pipeline debt, through data testing, documentation, and profiling.<br /><strong>Tech:</strong>&nbsp;Written in python</p>
<p>While tests in dbt are really convenient+helpful, GE takes it to a different level. Here are some places I have used it:</p>
<ul>
<li>You can point GE at your existing database and it will generate out some nice documentation for you.<br /><br />

<p><img src="{{ site.baseurl }}/assets/images/open_source_tools_4.jpg" alt="" /></p>

<br /><br /></li>
<li>We can define validation rules (called "expectations") that can be run against flat files or database tables. Below are some basic examples of these rules:<br /><br />

<p><img src="{{ site.baseurl }}/assets/images/open_source_tools_5.png" alt="" /></p>

<br /><br /></li>
<li>&nbsp;I have run these validation process for these expectations from jupyter, before file uploads, as part of ETL/ELT pipelines. You can even have the GE process generate out docs like this:<br /><br />

<p><img src="{{ site.baseurl }}/assets/images/open_source_tools_6.png" alt="" /></p>

</li>
</ul>
<hr />

<p><img src="{{ site.baseurl }}/assets/images/logos/superset.png" alt="" width=200 /></p>

<p><strong>Site:</strong>&nbsp;<a href="https://superset.incubator.apache.org/" target="_blank">https://superset.incubator.apache.org<br /></a><strong>Tagline:</strong> Apache Superset (incubating) is a modern, enterprise-ready business intelligence web application.<br /><strong>Tech:</strong>&nbsp;Written in python</p>
<p><strong>So what problem does it solve?</strong></p>
<p><strong></strong>You have lots of data, GREAT! But you will probably want a way to build reports and visualizations so you can understand and share out what that data means to your business.</p>
<p>There are great commercial offerings out there (like Tableau and Looker), but they are usually pretty expensive and have more overhead to administer.</p>
<p><strong>What is it?</strong></p>
<p>I am not going to go into detail of this here, since I wrote a blog post on my experience test driving SuperSet last year:</p>
<p><a href="/apache-superset-test-drive/">http://jaygrossman.com/apache-superset-test-drive/<br /></a></p>
<hr />

<p><img src="{{ site.baseurl }}/assets/images/logos/debezium.png" alt="" /></p>

<p><strong>Site:</strong>&nbsp;<a href="https://debezium.io/" target="_blank">https://debezium.io<br /></a><strong>Tagline:</strong> Debezium is an open source distributed platform for change data capture. Start it up, point it at your databases, and your apps can start responding to all of the inserts, updates, and deletes that other apps commit to your databases.<br /><strong>Tech:</strong>&nbsp;Written in Java</p>
<p>I saved the most ambitious one for last.&nbsp;</p>
<p><strong>So what problem does it solve?</strong></p>
<p>It is common need for companies to want to be able to:<br />- Sync all data changes between source systems (like production databases) and their reporting systems (data warehouse/data lake).<br />- Build a real time series (without taking snapshots of tables) of all changes on key tables / data sets&nbsp;</p>
<p><strong>What is it?</strong></p>
<p>Debezium is a set of distributed services that capture row-level changes in your databases so that your applications can see and respond to those changes. Debezium records in a transaction log all row-level changes committed to each database table. Each application simply reads the transaction logs they&rsquo;re interested in, and they see all of the events in the same order in which they occurred.&nbsp;</p>
<p>Change Data Capture, or CDC, is an older term for a system that monitors and captures the changes in data so that other software can respond to those changes. Data warehouses often had built-in CDC support, since data warehouses need to stay up-to-date as the data changed in the upstream OLTP databases. Debezium is essentially a modern, distributed open source change data capture platform that will eventually support monitoring a variety of database systems.</p>
<p>The diagram below illustrates how debezium can be used as part of a CDC solution:</p>

<p><img src="{{ site.baseurl }}/assets/images/open_source_tools_7.png" alt="" /></p>

<div>&nbsp;</div>
<p>Adrian Kreuziger has written an <a href="https://medium.com/convoy-tech/logs-offsets-near-real-time-elt-with-apache-kafka-snowflake-473da1e4d776" target="_blank">excellent blog post</a> that provides a high level walk you through how he set this up for <a href="https://convoy.com/" target="_blank">Convoy</a>. It walks through considerations for PostGres (we used it with mySQL), Kafka, Kafka Connect, and Snowflake.</p>
<p>In 2015, my team wrote something custom in Java to do some of what debezium does (capture change records from mySQL and write them to Kafka) to support requirements for real time time series. It was my first experience setting up Kafka and running it in production, so I learned a ton (both good and bad). Debezium (and KafkaConnect in general) were attractive because the creators seemed to have good options, it had configuration options out of the box, and we could get some community support.</p>
<p><strong>Please Note:</strong></p>
<p>There are some great commercial offerings in this space (FiveTran, Stitch Data, etc.) that make it super easy to set up regular data sync'ing data between data stores. You should definitely consider those offerings before building your own version of this - managing kafka yourself is non trivial. We found that most vendor SLA's may not meet the needs of organizations with real time and near real time requirements (such as running a logistics operation with hundreds of workers on a warehouse floor).</p>
