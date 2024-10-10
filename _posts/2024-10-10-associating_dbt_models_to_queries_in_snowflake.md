---
layout: post
title:  "Associating dbt models to queries in Snowflake"
author: jay
tags: [ data engineering, dbt, snowflake ] 
image: assets/images/headers/snowflake_dbt.png
description: "Associating dbt models to queries in Snowflake"
featured: false
hidden: false
comments: false
---

<p>Since 2017, I have been userof  <a href="https://www.getdbt.com/" target="_blank">dbt</a> - the most popular open source framework for transforming data in data warehouses. I have brought the framework into my last 4 companies, so needless to say I am a big fan boy as it has solved a bunch of common problems.</p>

<p>This week I got to attend dbt labs's biggest conference in Las Vegas - <a href="https://coalesce.getdbt.com/" target="_blank">Coalesce conference</a>. Overall there have been some excellent talks and I have gotten to meet a lot of people at all different stages in their careers who are solving interesting data related problems. It has been awesome to catch up with some folks who I haven't seen in person in many years.</p>

<h2>I want to find the dbt code for slow queries</h2>

<p>Yesterday I attended a session titled "Supercharge your data pipelines with AI & ML using dbt Labs and Snowflake". During the session I asked a question to the moderator:</p>

> When a query runs slow and I find it running slowly in Snowflake's Profiler, how can I can make it easier to associate the query to the dbt model where the code lives that I will need to refactor?

<h5>Some background on Snowflake's Query Profiler</h5>

<p>Snowflake's Query History shows queries run, who ran it and how long they took. I generally like to spend time looking at the longest running (most expensive) queries to look for opportunities to refactor them and save money.</p>

<p style="text-align: center;"><img src="{{ site.baseurl }}/assets/images/snowflake_query_history.png" alt="snowflake_query_history"  style="border:1px solid #000000;" /><br/>
<small>Snowflake's Query History view.</small></p>

<p>You can see the details of the query by clicking on the query link, including the SQL that was run and Query Profiler. The Profiler shows valuable diagnostic information about the query run and which logical parts of the query are the most expensive.</p>

<p style="text-align: center;"><img src="{{ site.baseurl }}/assets/images/snowflake_query_profiler.png" alt="snowflake_query_profiler"  style="border:1px solid #000000;" /><br/>
<small>Snowflake's Query Profile view.</small></p>

<h2>The solution:</h2>

<p>While the moderator of the session did not have much interest providing a solution, one of the audience members had a great suggestion - put a comment with the model's name in the SQL that dbt generates, so I will be able to see it in the query in the Profiler.</p>

<h5>Adding a comment with the model name using a macro</h5>

<p>We can a dbt macro that will add some model details as shown below:</p>

{% raw %}
```sql
{% macro model_comment() %}
/* Model: {{ model.name }}
   Schema: {{ model.schema }}
   Database: {{ target.database }}
   Compiled at: {{ modules.datetime.datetime.now() }} */
{% endmacro %}
```
{% endraw %}

<p>Then we can add the comment to the top of the SQL file for the model:</p>

{% raw %}
```
{{ model_comment() }}
SELECT 
  field1
  , field2
FROM my_table
```
{% endraw %}

<h5>Automatically adding this info to all dbt models</h5>

<p>While the macro is great, I am lazy and don't want to go through the exercise of adding t0 the 100's of models in my projects. I'd like to automagically add it to all of the models!</p>

<p>In dbt, the <a href="https://docs.getdbt.com/reference/project-configs/query-comment" target="_blank">query-comment</a> configuration in dbt_project.yml allows you to inject custom comments into the SQL queries that dbt runs against your database. This feature serves several important purposes:</p>
<ul>
<li>Query Attribution: It helps attribute SQL statements to specific dbt resources like models and tests.</li>
<li>Debugging and Tracing: Custom comments can aid in debugging by providing additional context about the query's origin and purpose.</li>
<li>Customization: You can customize the comment to include relevant information such as the user running the query, the dbt version, or any other metadata you find useful1.</li>
</ul>
<p>So we can add this into our dbt_project.yml file:</p>

{% raw %}
```yaml
query-comment: "{{ model_comment() }}"
```
{% endraw %}

