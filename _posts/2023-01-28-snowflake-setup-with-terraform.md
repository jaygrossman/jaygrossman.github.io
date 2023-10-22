---
layout: post
title:  "My Snowflake Set up with Terraform"
author: jay
categories: [ ]
tags: [ snowflake, terraform, data engineering ] 
image: assets/images/headers/snowflake_terraform.png
description: "My Snowflake Set up with Terraform"
featured: false
hidden: false
comments: false
redirect_from:
  - post/2023/01/28
#rating: 4.5
---



<p>I have been working with <a href="https://www.snowflake.com/en/" target="_blank">Snowflake</a> since 2016 when I proposed and chose to bring it into Rent the Runway to replace our very painful on premise Vertica implementation (yes we had it in a data center in NJ). Since then, Snowflake has grown considerably and is now one of the leading Data Warehouse offerings.<br /><br />After implementing data warehouses at several companies and having lots of conversations with some very smart folks, I've learned some things along the way. I've found it is a really good idea to think about up front how I want to segment responsibilities and permissions for databases. I want to decide what types of data to store in different places and to build roles + permission grants to enforce those decisions.&nbsp;</p>
<h3>My Best Practices Assumptions for segmenting data:</h3>
<p>Best practice for running a cloud data warehouse is to build separate repositories for:</p>
<ol>
<li>raw data from source systems and providers</li>
<li>schemas where data is regularly transformed/generated and documented to be consumed for reporting and analysis</li>
<li>area to data exploration, modeling, and experimentation</li>
</ol>
<p>To ensure that these logical areas are used for their defined purposes, we can create specific roles and permissions for each.&nbsp;</p>
<h3>Logical set up of Databases with Role Permissions</h3>

<p><img src="{{ site.baseurl }}/assets/images/sf_terrafrom_1.png" alt="" /></p>
<p>&nbsp;</p>
<table style="width: 600px;" border="1" cellpadding="5">
<tbody>
<tr>
<td style="padding: 5px; vertical-align: top;"><strong>database</strong></td>
<td style="padding: 5px; vertical-align: top;"><strong>functional description</strong></td>
<td style="padding: 5px; vertical-align: top;"><strong>privileges</strong></td>
</tr>
<tr>
<td style="padding: 5px; vertical-align: top;">Raw</td>
<td style="padding: 5px; vertical-align: top;">
<ul>
<li>Data is loaded from source systems and providers in its original (non transformed) format</li>
<li>No users can directly query this data</li>
</ul>
</td>
<td style="padding: 5px; vertical-align: top;">
<ul>
<li>load_role - full access&nbsp;</li>
<li>transform_role - read</li>
</ul>
</td>
</tr>
<tr>
<td style="padding: 5px; vertical-align: top;">Analytics</td>
<td style="padding: 5px; vertical-align: top;">
<ul>
<li>Data is transformed or generated (on a regular cadence) and documented to support reporting and analysis needs.</li>
<li>Users can directly query this data, but can not write/update</li>
<li>Metabase (and other reporting tools) would read from this database</li>
</ul>
</td>
<td style="padding: 5px; vertical-align: top;">
<ul>
<li>transform_role - full access</li>
<li>report_role - read</li>
</ul>
</td>
</tr>
<tr>
<td style="padding: 5px; vertical-align: top;">Work</td>
<td style="padding: 5px; vertical-align: top;">
<ul>
<li>Serves as an area to data exploration, modeling, and experimentation</li>
<li>Users can access, create/load, and change data in this area</li>
</ul>
</td>
<td style="padding: 5px; vertical-align: top;">
<ul>
<li>analyst_role - full access&nbsp;</li>
</ul>
</td>
</tr>
</tbody>
</table>
<p>&nbsp;</p>
<p>Other key assumptions with this set up:</p>
<ol>
<li>Each Snowflake Role has its own dedicated compute (Snowflake Warehouse). <br />For instance the LOAD_ROLE can only run on a warehouse named LOAD_WH.<br /><br /></li>
<li>The Raw database should contain a schema for each source system.&nbsp;<br />For example, if we are syncing data from our ecommerce site with a Mongo backend, we would name the schema as MONGO_ECOMMERCE.&nbsp;<br /><br /></li>
<li>In the Analytics database, we define 5 schemas:<br />- <strong>src</strong>:&nbsp; holding dbt models of data that has been lightly transformed from the original source data. Here we may flatten nested variant data into a relational model.<br />- <strong>trans</strong>:&nbsp;schema for intermediate dbt models <br />- <strong>rpt</strong>: holding dbt dimensional models that drive our BI reporting<br />- <strong>export</strong>: holding tables/views that are for data to be exported to systems outside of Snowflake <br />-&nbsp;<strong>db_stats</strong>: metadata about our dbt models like total runtime, last runtime, number of rows added incrementally, etc</li>
<li>The specific tools in the diagram above are meant to be examples for certain roles:<br />- Fivetran to sync raw data from source systems into RAW (could be meltano, airbyte)<br />- dbt to create models in ANALYTICS (could be ML scripts via dagster/airflow)<br />- Metabase to read from ANALYTICS for BI (could be Looker, Tableau, Superset)<br /><br /></li>
<li>You need to consider the processes for Development and Testing of Pipelines + Models. This diagram does not describe how I would approach these topics.</li>
</ol>
<h3>Snowflake configuration management with Terraform</h3>
<p><a href="https://www.terraform.io/" target="_blank">Terraform</a> (created by Hashicorp) is an open-source infrastructure as code software tool that enables you to safely and predictably create, change, and improve infrastructure.</p>
<h3>Chan Zuckerberg terraform provider for Snowflake</h3>
<p>This is a terraform provider plugin for managing Snowflake accounts: <br />GitHub -&nbsp;<a href="https://github.com/Snowflake-Labs/terraform-provider-snowflake" target="_blank">https://github.com/Snowflake-Labs/terraform-provider-snowflake</a></p>
<p>Documentation available here: <br /><a href="https://registry.terraform.io/providers/chanzuckerberg/snowflake/latest/docs" target="_blank">Terraform Registry</a></p>
<p>We can use this framework to manage objects in snowflake such as:</p>
<ul>
<li>databases</li>
<li>roles</li>
<li>schemas</li>
<li>user accounts</li>
<li>permission grants</li>
</ul>
<h3>Github repo with my terraform configuration for this set up</h3>
<p><a href="https://github.com/jaygrossman/snowflake_terraform_setup" target="_blank">https://github.com/jaygrossman/snowflake_terraform_setup</a>&nbsp;&nbsp;</p>
<p>PLEASE NOTE: This scope for this repo does not include the terrafom configuration to set up snowflake stages, functions, and file formatters. These objects often have more dependencies and require more advanced configuration, so I may plan to dedicate future blog posts to explaining the details.&nbsp;</p>
<h3>Folks who this would not have been possible without:</h3>
<table>
<tbody>
<tr>
<td><img src="{{ site.baseurl }}/assets/images/collin.jpg" alt="" width=200 /></td>
<td><img src="{{ site.baseurl }}/assets/images/rob.jpg" alt="" width=200 /></td>
<td><img src="{{ site.baseurl }}/assets/images/tim.jpg" alt="" width=200 /></td>
</tr>
<tr>
<td><a href="https://www.linkedin.com/in/collinmeyers/" target="_blank">Collin Meyers</a></td>
<td><a href="https://www.linkedin.com/in/rob-sokolowski-35b25318/" target="_blank">Rob Sokolowski</a></td>
<td><a href="https://www.linkedin.com/in/timricablanca/" target="_blank">Tim Ricablanca</a></td>
</tr>
</tbody>
</table>