---
layout: post
title:  "Dagster with Python, Singer, and Meltano"
author: jay
categories: [ ]
tags: [ orchestration, data engineering, dagster, python, singer, meltano, workflow ] 
image: assets/images/headers/dagster_meltano.jpg
description: "Dagster with Python, Singer, and Meltano"
featured: false
hidden: false
comments: false
redirect_from:
  - post/2023/04/04
#rating: 4.5
---


<p>I have been a fan of Dagster for data orchestration for a little while and wanted to share some of the basics. There are a lot of cool things I like about it (compared to airflow and other schedulers):</p>
<ul>
<li>It is Declarative (via their <a href="https://docs.dagster.io/concepts/assets/software-defined-assets" target="_blank">Software-Defined Asset</a> object). I personally like tools and frameworks that allow me to declare a desired end state (Terraform, dbt, Puppet, Ansible, etc.) vs. frameworks that have me build a bunch of imperative tasks that get daisy chained together.&nbsp; &nbsp;<br /><br /></li>
<li>It makes it very easy to define dependencies as function arguments. It reminds a lot of simplicity I see with <em>ref()</em> statements in dbt.<br /><br /></li>
<li>It feels like it is a better fit for iterative engineering. It has easy support for <a href="https://docs.dagster.io/guides/dagster/testing-assets" target="_blank">writing unit tests</a> and running the same code/functionality in different environments.<br /><br /></li>
<li>It pretty easily has integration support with many data related steps I would want to apply as part of an asset building pipeline - including tools like dbt<em>,&nbsp;</em>airbye, meltano, etc.</li>
</ul>
<h3>Goal for this blog post:</h3>
<p>In this post, I am going to document different ways how I can build pretty simple common pipelines that take a csv and upload the contents to postgres via Dagster using:</p>
<ol>
<li>Python Code</li>
<li>Singer</li>
<li>Meltano</li>
</ol>
<h3>Set Up Steps for this demo:</h3>
<ol>
<li>We need to install Dasgter + necessary python packages (I am installing it locally, but we could install it in a docker also):<br /><br />&nbsp; &nbsp; &nbsp; pip3 install dagster dagit pandas psycopg2<br /><br /></li>
<li>We need to install postgres (also adding pgadmin for web based admin) in dockers:<br /><br /><a href="https://towardsdatascience.com/how-to-run-postgresql-and-pgadmin-using-docker-3a6a8ae918b5" target="_blank">https://towardsdatascience.com/how-to-run-postgresql-and-pgadmin-using-docker-3a6a8ae918b5<br /><br /></a>This creates us a local postgres instance with a database "demo_db".<br /><br /></li>
<li>We need to create a table in the demo_db database:<br /><br />
<pre class="brush: sql;">CREATE TABLE IF NOT EXISTS public.sales
(
&nbsp; &nbsp; TransactionID text
&nbsp; &nbsp; , Seller text
&nbsp; &nbsp; , Date text
&nbsp; &nbsp; , Value text
&nbsp; &nbsp; , Title text
&nbsp; &nbsp; , Identifier text
&nbsp; &nbsp; , Condition text
&nbsp; &nbsp; , RetailValue text
&nbsp; &nbsp; , ItemValue text
);</pre>


</li>
<li>We need some sample data in csv format to upload, so I cerated some sales data for a 1973 Topps Rich Gossage baseball card (below contains a few records):<br /><br /> TransactionID,Seller,Date,Value,Title,Identifier,Condition,RetailValue,ItemValue <br />8094231,comicards990,2023-04-12,14.59,1973 Topps Rich "Goose" Gossage Rookie White Sox HOF #174,134520441986,Ungraded,6.00,8.73 <br />8094232,916lukey31,2023-04-11,10.95,1973 Topps #174 Rich Gossage RC HOF Vg-Ex *Free Shipping*,275699466365,Ungraded,6.00,8.73 <br />8094233,jayjay5119,2023-04-11,6.50,1973 Topps Baseball! Rich Gossage rookie card! Card174! Chicago White Sox!,195695255305,Ungraded,6.00,8.73</li>
</ol>
<h3>Running Dagster with Python</h3>
<p>1) The first thing I did was to run the scaffolding command to create a new dagster project:</p>
<p style="padding-left: 30px;">dagster project scaffold --name dagster-project</p>
<p>It created the following directory and files:</p>
<table class="table" style="border-width: 0px; border-style: solid; border-color: inherit; border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000; text-indent: 0px; border-collapse: collapse; width: 720px; table-layout: auto; margin-top: 2em; margin-bottom: 2em; font-size: 0.875em; line-height: 1.71429; color: #524e48; font-family: 'Neue Montreal', ui-sans-serif, system-ui, -apple-system, 'system-ui', 'Segoe UI', Roboto, 'Helvetica Neue', Arial, 'Noto Sans', sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji', 'Segoe UI Symbol', 'Noto Color Emoji'; letter-spacing: 0.28px; background-color: #faf9f7;">
<thead style="box-sizing: border-box; border-width: 0px 0px 1px; border-style: solid; border-bottom-color: #bdbab7; border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000; color: #231f1b; font-weight: 600;">
<tr style="box-sizing: border-box; border-width: 0px; border-style: solid; border-color: rgba(218,216,214,var(--tw-border-opacity)); border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000;"><th style="box-sizing: border-box; border-width: 0px; border-style: solid; border-color: rgba(218,216,214,var(--tw-border-opacity)); border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000; vertical-align: bottom; padding-right: 0.571429em; padding-bottom: 0.571429em; padding-left: 0px; width: 180px;">File/Directory</th><th style="box-sizing: border-box; border-width: 0px; border-style: solid; border-color: rgba(218,216,214,var(--tw-border-opacity)); border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000; vertical-align: bottom; padding-right: 0px; padding-bottom: 0.571429em; padding-left: 0.571429em;">Description</th></tr>
</thead>
<tbody style="box-sizing: border-box; border-width: 0px; border-style: solid; border-color: rgba(218,216,214,var(--tw-border-opacity)); border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000;">
<tr style="box-sizing: border-box; border-width: 0px 0px 1px; border-style: solid; border-bottom-color: #dad8d6; border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000;">
<td style="box-sizing: border-box; border-width: 0px; border-style: solid; border-color: rgba(218,216,214,var(--tw-border-opacity)); border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000; vertical-align: top; padding: 0.571429em 0.571429em 0.571429em 0px;">dagster_project/</td>
<td style="box-sizing: border-box; border-width: 0px; border-style: solid; border-color: rgba(218,216,214,var(--tw-border-opacity)); border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000; vertical-align: top; padding: 0.571429em 0px 0.571429em 0.571429em;">A Python package that contains your new Dagster code.</td>
</tr>
<tr style="box-sizing: border-box; border-width: 0px 0px 1px; border-style: solid; border-bottom-color: #dad8d6; border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000;">
<td style="box-sizing: border-box; border-width: 0px; border-style: solid; border-color: rgba(218,216,214,var(--tw-border-opacity)); border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000; vertical-align: top; padding: 0.571429em 0.571429em 0.571429em 0px;">dagster_project_tests/</td>
<td style="box-sizing: border-box; border-width: 0px; border-style: solid; border-color: rgba(218,216,214,var(--tw-border-opacity)); border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000; vertical-align: top; padding: 0.571429em 0px 0.571429em 0.571429em;">A Python package that contains tests for<code style="box-sizing: border-box; border-width: 0px; border-style: solid; border-color: rgba(218,216,214,var(--tw-border-opacity)); border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000; font-family: 'Diatype Mono'; font-size: 14px; background: #f5f4f2; color: #231f1b; padding: 4px 6px; border-radius: 4px; overflow-wrap: break-word;">dagster_project</code>.</td>
</tr>
<tr style="box-sizing: border-box; border-width: 0px 0px 1px; border-style: solid; border-bottom-color: #dad8d6; border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000;">
<td style="box-sizing: border-box; border-width: 0px; border-style: solid; border-color: rgba(218,216,214,var(--tw-border-opacity)); border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000; vertical-align: top; padding: 0.571429em 0.571429em 0.571429em 0px;">README.md</td>
<td style="box-sizing: border-box; border-width: 0px; border-style: solid; border-color: rgba(218,216,214,var(--tw-border-opacity)); border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000; vertical-align: top; padding: 0.571429em 0px 0.571429em 0.571429em;">A description and starter guide for your new Dagster project.</td>
</tr>
<tr style="box-sizing: border-box; border-width: 0px 0px 1px; border-style: solid; border-bottom-color: #dad8d6; border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000;">
<td style="box-sizing: border-box; border-width: 0px; border-style: solid; border-color: rgba(218,216,214,var(--tw-border-opacity)); border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000; vertical-align: top; padding: 0.571429em 0.571429em 0.571429em 0px;">pyproject.toml</td>
<td style="box-sizing: border-box; border-width: 0px; border-style: solid; border-color: rgba(218,216,214,var(--tw-border-opacity)); border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000; vertical-align: top; padding: 0.571429em 0px 0.571429em 0.571429em;">A file that specifies package core metadata in a static, tool-agnostic way.<br style="box-sizing: border-box; border-width: 0px; border-style: solid; border-color: rgba(218,216,214,var(--tw-border-opacity)); border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000;" /><br style="box-sizing: border-box; border-width: 0px; border-style: solid; border-color: rgba(218,216,214,var(--tw-border-opacity)); border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000;" />This file includes a&nbsp;<code style="box-sizing: border-box; border-width: 0px; border-style: solid; border-color: rgba(218,216,214,var(--tw-border-opacity)); border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000; font-family: 'Diatype Mono'; font-size: 14px; background: #f5f4f2; color: #231f1b; padding: 4px 6px; border-radius: 4px; overflow-wrap: break-word;">tool.dagster</code>&nbsp;section which references the Python package with your Dagster definitions defined and discoverable at the top level. This allows you to use the<code style="box-sizing: border-box; border-width: 0px; border-style: solid; border-color: rgba(218,216,214,var(--tw-border-opacity)); border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000; font-family: 'Diatype Mono'; font-size: 14px; background: #f5f4f2; color: #231f1b; padding: 4px 6px; border-radius: 4px; overflow-wrap: break-word;">dagster dev</code>&nbsp;command to load your Dagster code without any parameters. Refer to the&nbsp;<a style="box-sizing: border-box; border-width: 0px; border-style: solid; border-color: rgba(218,216,214,var(--tw-border-opacity)); border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000; color: #4f43dd; text-decoration-line: none; transition: all 0.3s ease 0s; overflow-wrap: break-word;" href="https://docs.dagster.io/concepts/code-locations">Code locations documentation</a>&nbsp;to learn more.<br style="box-sizing: border-box; border-width: 0px; border-style: solid; border-color: rgba(218,216,214,var(--tw-border-opacity)); border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000;" /><br style="box-sizing: border-box; border-width: 0px; border-style: solid; border-color: rgba(218,216,214,var(--tw-border-opacity)); border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000;" /><span style="box-sizing: border-box; border-width: 0px; border-style: solid; border-color: rgba(218,216,214,var(--tw-border-opacity)); border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000; color: #231f1b;">Note:</span>&nbsp;<code style="box-sizing: border-box; border-width: 0px; border-style: solid; border-color: rgba(218,216,214,var(--tw-border-opacity)); border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000; font-family: 'Diatype Mono'; font-size: 14px; background: #f5f4f2; color: #231f1b; padding: 4px 6px; border-radius: 4px; overflow-wrap: break-word;">pyproject.toml</code>&nbsp;was introduced in&nbsp;<a style="box-sizing: border-box; border-width: 0px; border-style: solid; border-color: rgba(218,216,214,var(--tw-border-opacity)); border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000; color: #4f43dd; text-decoration-line: none; transition: all 0.3s ease 0s; overflow-wrap: break-word;" href="https://peps.python.org/pep-0518/https://peps.python.org/pep-0518/">PEP-518</a>&nbsp;and meant to replace&nbsp;<code style="box-sizing: border-box; border-width: 0px; border-style: solid; border-color: rgba(218,216,214,var(--tw-border-opacity)); border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000; font-family: 'Diatype Mono'; font-size: 14px; background: #f5f4f2; color: #231f1b; padding: 4px 6px; border-radius: 4px; overflow-wrap: break-word;">setup.py</code>, but we may still include a&nbsp;<code style="box-sizing: border-box; border-width: 0px; border-style: solid; border-color: rgba(218,216,214,var(--tw-border-opacity)); border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000; font-family: 'Diatype Mono'; font-size: 14px; background: #f5f4f2; color: #231f1b; padding: 4px 6px; border-radius: 4px; overflow-wrap: break-word;">setup.py</code>&nbsp;for compatibility with tools that do not use this spec.</td>
</tr>
<tr style="box-sizing: border-box; border-width: 0px 0px 1px; border-style: solid; border-bottom-color: #dad8d6; border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000;">
<td style="box-sizing: border-box; border-width: 0px; border-style: solid; border-color: rgba(218,216,214,var(--tw-border-opacity)); border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000; vertical-align: top; padding: 0.571429em 0.571429em 0.571429em 0px;">setup.py</td>
<td style="box-sizing: border-box; border-width: 0px; border-style: solid; border-color: rgba(218,216,214,var(--tw-border-opacity)); border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000; vertical-align: top; padding: 0.571429em 0px 0.571429em 0.571429em;">A build script with Python package dependencies for your new project as a package.</td>
</tr>
<tr style="box-sizing: border-box; border-width: 0px; border-style: solid; border-bottom-color: #dad8d6; border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000;">
<td style="box-sizing: border-box; border-width: 0px; border-style: solid; border-color: rgba(218,216,214,var(--tw-border-opacity)); border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000; vertical-align: top; padding: 0.571429em 0.571429em 0.571429em 0px;">setup.cfg</td>
<td style="box-sizing: border-box; border-width: 0px; border-style: solid; border-color: rgba(218,216,214,var(--tw-border-opacity)); border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000; vertical-align: top; padding: 0.571429em 0px 0.571429em 0.571429em;">An ini file that contains option defaults for&nbsp;<code style="box-sizing: border-box; border-width: 0px; border-style: solid; border-color: rgba(218,216,214,var(--tw-border-opacity)); border-image: initial; --tw-border-opacity: 1; --tw-shadow: 0 0 #0000; --tw-ring-inset: var(--tw-empty, ); --tw-ring-offset-width: 0px; --tw-ring-offset-color: #fff; --tw-ring-color: rgba(59,130,246,0.5); --tw-ring-offset-shadow: 0 0 #0000; --tw-ring-shadow: 0 0 #0000; font-family: 'Diatype Mono'; font-size: 14px; background: #f5f4f2; color: #231f1b; padding: 4px 6px; border-radius: 4px; overflow-wrap: break-word;">setup.py</code>&nbsp;commands.</td>
</tr>
</tbody>
</table>
<p>2) In that directory we save our sales csv file as:&nbsp;</p>
<p style="padding-left: 30px;">1973_topps_gossage_sales.csv</p>
<p>3) In that directory, create a file called python_assets.py:</p>


    from dagster import asset # import the `dagster` library

    # python libraries we need for these assets
    import numpy as np
    import psycopg2
    import psycopg2.extras as extras
    import pandas as pd

    # get sales from a csv file

    @asset
    def get_sales():
        csv_file_path = './1973_topps_gossage_sales.csv'
        df = pd.read_csv(csv_file_path)
        return df

    # write sales to postgres table, calling the dataframe from get_sales
    @asset
    def write_sales(get_sales):
        conn = psycopg2.connect(
            database="demo_db",
            user='root',
            password='root',
            host='localhost',
            port='5432',
            options="-c search_path=dbo,public"
        )

        table = 'sales'
        tuples = [tuple(x) for x in get_sales.to_numpy()]

        cols = ','.join(list(get_sales.columns))
        query = "INSERT INTO %s(%s) VALUES %%s" % (table, cols)
        cursor = conn.cursor()
        try:
            extras.execute_values(cursor, query, tuples)
            conn.commit()
        except (Exception, psycopg2.DatabaseError) as error:
            print("Error: %s" % error)
            conn.rollback()
            cursor.close()
            return 1
        print("the dataframe is inserted")
        cursor.close() 



<p>In the above code, we have functions preceded by the <strong>@asset</strong> decorator. This tells Dagster to identify them as software-defined assets that can be materialized.&nbsp;</p>
<p>The <strong>get_sales</strong> asset will read the csv file and populate the dataframe. The&nbsp;<strong>write_sales</strong> asset writes the contents of dataframe to our Postgres table. Notice the&nbsp;<strong>write_sales(<span style="color: #ff0000;">get_sales</span>)</strong>&nbsp;signature, that is how Dagster can recognize that&nbsp;<strong>get_sales</strong>&nbsp;is a dependency for&nbsp;<strong>write_sales</strong>.</p>
<p>PLEASE NOTE: We would never want to hard code database credentials in our python code, below is how you can use environment variables to make it more secure:&nbsp;&nbsp;<br /><span style="color: #0000ee; text-decoration-line: underline;">https://docs.dagster.io/guides/dagster/using-environment-variables-and-secrets</span>&nbsp;</p>
<p>4) We can now run the asset pipeline in the Dagster dashboard. You can type the following on the command line to launch the dagit dashboard:</p>
<p style="padding-left: 30px;">dagster dev -f python_assets.py</p>
<p>The you can visit the following address in your browser to view the dashboard:</p>
<p>http://127.0.0.1:3000/assets</p>

<p><img src="{{ site.baseurl }}/assets/images/dagster_fun_1.png" alt="" /></p>

<p><br />4) Select the checkboxes in front of both assets and click the "Materialize selected" button. This will execute the assets in the correct order.&nbsp;</p>
<p>The screen below show the successful materialization of the assets:&nbsp;</p>

<p><img src="{{ site.baseurl }}/assets/images/dagster_fun_2.png" alt="" /></p>

<p><br />5) We can log into Postgres (via pgadmin) and see our records written into the <strong>public.sales</strong> table:</p>

<p><img src="{{ site.baseurl }}/assets/images/dagster_fun_3.png" alt="" /></p>

<h3><br />Running Dagster with Singer</h3>
<p><a href="https://www.singer.io/" target="_blank">Singer</a> is an open-source ETL tool from Stitch that lets you write scripts to move data from your sources to their destinations. Singer has two types of scripts&mdash;taps and targets.</p>
<ul>
<li>A tap is a script, or a piece of code, that connects to your data sources and outputs the data in JSON format.<br /><br /></li>
<li>A target script pipes these data streams from input sources and store them in your data destinations.&nbsp;</li>
</ul>
<p>1) In order to migrate our dagster flow to use singer, we will need a tap to read our csv and a target to write to postgres. We can install them into python virtual environments with the below commands:</p>

    python -m venv tap-csv-venv
    source tap-csv-venv/bin/activate
    pip3 install git+https://github.com/MeltanoLabs/tap-csv.git
    alias tap-csv="tap-csv-venv/bin/tap-csv"
    deactivate

    python -m venv target-postgres-venv
    source target-postgres-venv/bin/activate
    pip3 install git+https://github.com/datamill-co/target-postgres.git
    alias target-postgres="target-postgres-venv/bin/target-postgres"
    deactivate


<p>2) Configure the tap-csv (docs at <a href="https://github.com/MeltanoLabs/tap-csv" target="_blank">https://github.com/MeltanoLabs/tap-csv</a>):</p>
<p>- Create a file singer/config.json with this content:</p>

    {
        "csv_files_definition": "./singer/files_def.json"
    }

<p>- Create a file singer/files_def.json with this content:</p>

    [
        {   "entity" : "sales",
            "path" : "1973_topps_gossage_sales.csv",
            "keys" : ["TransactionID"]
        }
    ]

<p>You can test that the tap will read in your file and convert it to json:</p>
<p style="padding-left: 30px;"><strong>tap-csv --config singer/config.json</strong></p>
<p style="padding-left: 30px;">2023-04-03 05:23:13,706 | INFO&nbsp; &nbsp; &nbsp;| tap-csv&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; | Beginning full_table sync of 'sales'...<br />2023-04-03 05:23:13,706 | INFO&nbsp; &nbsp; &nbsp;| tap-csv&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; | Tap has custom mapper. Using 1 provided map(s).<br />{"type": "SCHEMA", "stream": "sales", "schema": {"properties": {"TransactionID": {"type": ["string", "null"]}, "Seller": {"type": ["string", "null"]}, "Date": {"type": ["string", "null"]}, "Value": {"type": ["string", "null"]}, "Title": {"type": ["string", "null"]}, "Identifier": {"type": ["string", "null"]}, "Condition": {"type": ["string", "null"]}, "RetailValue": {"type": ["string", "null"]}, "ItemValue": {"type": ["string", "null"]}}, "type": "object"}, "key_properties": ["TransactionID"]}<br />{"type": "RECORD", "stream": "sales", "record": {"TransactionID": "8094231", "Seller": "comicards990", "Date": "2023-04-12", "Value": "14.59", "Title": "1973 Topps Rich \"Goose\" Gossage Rookie White Sox HOF #174", "Identifier": "134520441986", "Condition": "Ungraded", "RetailValue": "6.00", "ItemValue": "8.73"}, "time_extracted": "2023-04-03T09:23:13.706647+00:00"}<br />{"type": "STATE", "value": {"bookmarks": {"sales": {"starting_replication_value": null}}}}<br />{"type": "RECORD", "stream": "sales", "record": {"TransactionID": "8094232", "Seller": "916lukey31", "Date": "2023-04-11", "Value": "10.95", "Title": "1973 Topps #174 Rich Gossage RC HOF Vg-Ex *Free Shipping*", "Identifier": "275699466365", "Condition": "Ungraded", "RetailValue": "6.00", "ItemValue": "8.73"}, "time_extracted": "2023-04-03T09:23:13.706848+00:00"}<br />{"type": "RECORD", "stream": "sales", "record": {"TransactionID": "8094233", "Seller": "jayjay5119", "Date": "2023-04-11", "Value": "6.50", "Title": "1973 Topps Baseball! Rich Gossage rookie card! Card174! Chicago White Sox!", "Identifier": "195695255305", "Condition": "Ungraded", "RetailValue": "6.00", "ItemValue": "8.73"}, "time_extracted": "2023-04-03T09:23:13.707571+00:00"}<br />2023-04-03 05:23:13,707 | INFO&nbsp; &nbsp; &nbsp;| singer_sdk.metrics&nbsp; &nbsp;| INFO METRIC: {"metric_type": "timer", "metric": "sync_duration", "value": 0.0012621879577636719, "tags": {"stream": "sales", "context": {}, "status": "succeeded"}}<br />2023-04-03 05:23:13,708 | INFO&nbsp; &nbsp; &nbsp;| singer_sdk.metrics&nbsp; &nbsp;| INFO METRIC: {"metric_type": "counter", "metric": "record_count", "value": 3, "tags": {"stream": "sales", "context": {}}}<br />{"type": "STATE", "value": {"bookmarks": {"sales": {}}}}<br />{"type": "STATE", "value": {"bookmarks": {"sales": {}}}}</p>
<p>2) Configure the taraget-postgres (docs at&nbsp;<a href="https://github.com/datamill-co/target-postgres" target="_blank">https://github.com/datamill-co/target-postgres</a>):</p>

<p>- Create a file singer/target_postgres_config.json with this content:</p>

    {
        "postgres_host": "localhost",
        "postgres_port": 5432,
        "postgres_database": "demo_db",
        "postgres_username": "root",
        "postgres_password": "root",
        "postgres_schema": "public"
    }

<p>You can test that the tap and target will read in your file and upload it to postgres:</p>
<p style="padding-left: 30px;"><strong>tap-csv --config singer/config.json | target-postgres --config singer/target_postgres_config.json&nbsp;<br /></strong></p>
<p style="padding-left: 30px;">INFO PostgresTarget created with established connection: `user=root password=xxx dbname=demo_db host=localhost port=5432 application_name=target-postgres`, PostgreSQL schema: `public`</p>
<p style="padding-left: 30px;">INFO Sending version information to singer.io. To disable sending anonymous usage data, set the config parameter "disable_collection" to true</p>
<p style="padding-left: 30px;">2023-04-03 13:09:44,466 | INFO&nbsp; &nbsp; &nbsp;| tap-csv&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; | Beginning full_table sync of 'sales'...<br />2023-04-03 13:09:44,467 | INFO&nbsp; &nbsp; &nbsp;| tap-csv&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; | Tap has custom mapper. Using 1 provided map(s).<br />2023-04-03 13:09:44,467 | INFO&nbsp; &nbsp; &nbsp;| singer_sdk.metrics&nbsp; &nbsp;| INFO METRIC: {"metric_type": "timer", "metric": "sync_duration", "value": 0.000537872314453125, "tags": {"stream": "sales", "context": {}, "status": "succeeded"}}<br />2023-04-03 13:09:44,467 | INFO&nbsp; &nbsp; &nbsp;| singer_sdk.metrics&nbsp; &nbsp;| INFO METRIC: {"metric_type": "counter", "metric": "record_count", "value": 3, "tags": {"stream": "sales", "context": {}}}<br />INFO Mapping: test to None<br />INFO Mapping: sales to ['sales']<br />INFO Mapping: tp_sales_transactionid__sdc_sequence_idx to None<br />INFO Stream sales (sales) with max_version None targetting None<br />INFO Root table name sales<br />INFO Writing batch with 3 records for `sales` with `key_properties`: `['TransactionID']`<br />INFO Writing table batch schema for `('sales',)`...<br />INFO METRIC: {"type": "timer", "metric": "job_duration", "value": 0.048573970794677734, "tags": {"job_type": "upsert_table_schema", "path": ["sales"], "database": "demo_db", "schema": "public", "table": "sales", "status": "succeeded"}}<br />INFO Writing table batch with 3 rows for `('sales',)`...<br />INFO METRIC: {"type": "counter", "metric": "record_count", "value": 3, "tags": {"count_type": "table_rows_persisted", "path": ["sales"], "database": "demo_db", "schema": "public", "table": "sales"}}<br />INFO METRIC: {"type": "timer", "metric": "job_duration", "value": 0.10874700546264648, "tags": {"job_type": "table", "path": ["sales"], "database": "demo_db", "schema": "public", "table": "sales", "status": "succeeded"}}<br />INFO METRIC: {"type": "counter", "metric": "record_count", "value": 3, "tags": {"count_type": "batch_rows_persisted", "path": ["sales"], "database": "demo_db", "schema": "public"}}<br />INFO METRIC: {"type": "timer", "metric": "job_duration", "value": 0.10944700241088867, "tags": {"job_type": "batch", "path": ["sales"], "database": "demo_db", "schema": "public", "status": "succeeded"}}</p>
<p><strong><span style="text-decoration: underline;">Some things to take note of:</span><br /></strong></p>
<p>- We defined the entity = "sales" in the tap.&nbsp; So that dictates the table_name for the target.</p>
<p>- We see in the output <strong>"job_type": "upsert_table_schema"</strong>, this means that write to postgres will do an upsert of the record based on the keys we defined in tap ("keys" : ["TransactionID"]).</p>
<p>3)&nbsp;In that directory, create a file called python_assets.py:</p>

    # import the `dagster` library
    from dagster import asset

    # python libraries we need for these assets
    import subprocess

    # get sales from a csv file and write to postgres via singer tap and target
    @asset
    def get_and_write_sales_with_singer():
        ps = subprocess.Popen(['~/dagster/dagster-project/tap-csv-venv/bin/tap-csv', '--config', 'singer/config.json'],
        stdout=subprocess.PIPE) output = subprocess.run(['~/dagster/dagster-project/target-postgres-venv/bin/target-postgres', '--config', 'singer/target_postgres_config.json'], stdin=ps.stdout)
        ps.wait() print(output.stdout)


<p>4) We can now run the asset pipeline in the Dagster dashboard. You can type the following on the command line to launch the dagit dashboard:</p>

    dagster dev -f singer_assets.py

<p>The you can visit the following address in your browser to view the dashboard:</p>
<p>http://127.0.0.1:3000/assets</p>


<p><img src="{{ site.baseurl }}/assets/images/dagster_fun_4.png" alt="" /></p>


<p><br />5) Select the checkboxes in front of both assets and click the "Materialize selected" button. This will execute the assets in the correct order.&nbsp;</p>
<p>The screen below show the successful materialization of the assets:&nbsp;</p>

<p><img src="{{ site.baseurl }}/assets/images/dagster_fun_5.png" alt="" /></p>

<p>&nbsp;<br />6) We can log into Postgres (via pgadmin) and see our records written into the&nbsp;<strong>public.sales</strong>&nbsp;table:</p>

<p><img src="{{ site.baseurl }}/assets/images/dagster_fun_6.png" alt="" /></p>

<h3><br />Running Dagster with Meltano</h3>

<p><a href="https://meltano.com/" target="_blank">Meltano</a> is an open source tool which can be used to extract from data sources and load it to destinations like your data warehouse. It uses extractors and loaders written in the Singer open source standard.</p>

<p>1) In order to migrate our dagster flow to use meltano, we will need to install meltano:</p>

    pip3 install meltano


<p>2) Configure a new meltano project and switch into the directory:&nbsp;</p>

    meltano init meltano
    cd meltano

<p>3) We need to install the tap and target:</p>

    meltano add extractor tap-csv
    meltano add loader target-postgres --variant&nbsp;datamill-co

<p>4) Adding a job that include the tap and target:</p>

    meltano job add demo_job --tasks "tap-csv target-postgres"

<p>4) Configure the following in meltano.yml (it should look very similar to the singer configuration from the Singer section):</p>

    version: 1
    default_environment: dev
    project_id: fb9afb3c-5560-406e-b977-d86eef949779
    environments:
    - name: dev
    - name: staging
    - name: prod
    plugins:
    extractors:
    - name: tap-csv
        variant: meltanolabs
        pip_url: git+https://github.com/MeltanoLabs/tap-csv.git
        config:
        files:
            - entity: sales
            file: ../1973_topps_gossage_sales.csv
            keys:
                - TransactionID
    loaders:
    - name: target-postgres
        variant: datamill-co
        pip_url: git+https://github.com/datamill-co/target-postgres.git
        config:
        host: localhost
        port: 5432
        user: root
        password: root
        dbname: demo_db
        default_target_schema: public
    jobs:
    - name: demo_job
    tasks:
    - tap-csv target-postgres

<p>5) Run meltano on the command line:</p>

    meltano run demo_job

<p style="padding-left: 30px;">2023-04-03T18:06:17.336263Z [info&nbsp; &nbsp; &nbsp;] Environment 'dev' is active<br />2023-04-03T18:06:19.212684Z [info&nbsp; &nbsp; &nbsp;] INFO Starting sync&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;cmd_type=elb consumer=False name=tap-csv producer=True stdio=stderr string_id=tap-csv<br />2023-04-03T18:06:19.212939Z [info&nbsp; &nbsp; &nbsp;] INFO Syncing entity 'sales' from file: '../1973_topps_gossage_sales.csv' cmd_type=elb consumer=False name=tap-csv producer=True stdio=stderr string_id=tap-csv<br />2023-04-03T18:06:19.213097Z [info&nbsp; &nbsp; &nbsp;] INFO Sync completed&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; cmd_type=elb consumer=False name=tap-csv producer=True stdio=stderr string_id=tap-csv<br />2023-04-03T18:06:19.577999Z [info&nbsp; &nbsp; &nbsp;] time=2023-04-03 14:06:19 name=target_postgres level=INFO message=Table '"sales"' exists cmd_type=elb consumer=True name=target-postgres producer=False stdio=stderr string_id=target-postgres<br />2023-04-03T18:06:19.711365Z [info&nbsp; &nbsp; &nbsp;] time=2023-04-03 14:06:19 name=target_postgres level=INFO message=Loading 3 rows into 'public."sales"' cmd_type=elb consumer=True name=target-postgres producer=False stdio=stderr string_id=target-postgres<br />2023-04-03T18:06:19.840375Z [info&nbsp; &nbsp; &nbsp;] time=2023-04-03 14:06:19 name=target_postgres level=INFO message=Loading into public."sales": {"inserts": 0, "updates": 3, "size_bytes": 452} cmd_type=elb consumer=True name=target-postgres producer=False stdio=stderr string_id=target-postgres<br />2023-04-03T18:06:19.862895Z [info&nbsp; &nbsp; &nbsp;] Incremental state has been updated at 2023-04-03 18:06:19.862840.<br />2023-04-03T18:06:19.871775Z [info&nbsp; &nbsp; &nbsp;] Block run completed.&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;block_type=ExtractLoadBlocks err=None set_number=0 success=True</p>


<p>6) Install the Dagster-Meltano library</p>

    cd ../
    pip3 install dagster-meltano

<p>7) In that directory, create a file called meltano_assets.py:</p>

    from dagster import Definitions, job
    from dagster_meltano import meltano_resource, meltano_run_op 

    @job(resource_defs={"meltano": meltano_resource})
    def run_job():
    tap_done = meltano_run_op("demo_job")() 

    # alternatively we could run this
    # tap_done = meltano_run_op("tap-csv target-postgres")()

    defs = Definitions(jobs=[run_job])

<p>8) We can now run the asset pipeline in the Dagster dashboard. You can type the following on the command line to launch the dagit dashboard:</p>

    dagster dev -f meltano_assets.py