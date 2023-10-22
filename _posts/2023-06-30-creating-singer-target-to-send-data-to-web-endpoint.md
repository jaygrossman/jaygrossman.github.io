---
layout: post
title:  "Creating Singer Target to Send Data to Web Endpoint"
author: jay
categories: [ ]
tags: [  data engineering, singer, python  ] 
image: assets/images/headers/target-web_endpoint.png
description: "Creating Singer Target to Send Data to Web Endpoint"
featured: false
hidden: false
comments: false
#rating: 4.5
---

<table style="width: 100%; border-color:red;" border="1" cellpadding="5">
<tr>
<td>
   <strong><i>Please Note:</i></strong><br>
   If you are new to Singer, you may want to check out my last post <a href="/creating-singer-tap-to-capture-ebay-completed-items/" target="_blank">Creating Singer Tap to Capture Ebay Completed Items</a>. It provides a high level background of the specification and how taps &amp; targets work together.
    </td>
</tr>
</table>
<br>
<h3>The Challenge</h3>
<p>Last week I created a walk through of&nbsp;<a href="http://jaygrossman.com/post/2023/06/19/Creating-Singer-Tap-to-Capture-Ebay-Completed-Items.aspx" target="_blank">Creating Singer Tap to Capture Ebay Completed Items</a>. While it's great to capture data, it's not overly useful without persisting the data to a target destination.</p>
<p>There are some useful targets&nbsp;<a href="https://www.singer.io/#targets" target="_blank">posted on signer.io</a>&nbsp;and&nbsp;<a href="https://hub.meltano.com/loaders/" target="_blank">posted on meltano</a>&nbsp;for writing to a nice variety of standard destinations (databases, cloud data warehouses, S3, csv, json, etc.). However my site has an API and I could not find a target to send to a web endpoint.</p>
<h2><span style="color: #ff0000;"><em><strong>I want to able to pipe data from a Singer Tap to my own API endpoints</strong></em></span></h2>
<h3>Creating target-web_endpoint</h3>
<p>Code for this Singer Target is posted on github (click on image below):</p>

<p><a href="https://github.com/jaygrossman/target-web_endpoint" target="_blank"><img src="{{ site.baseurl }}/assets/images/github_target-web_endpoint.png" alt="" style="border:1px solid blue;" /></a></p>

<h4>Project Scope</h4>
<p>So the goal is to create a Singer target that will allow us to take data piped from a Singer tap and send it to a web endpoint (via a HTTP GET or HTTP POST). Below is a visual illustration:</p>

<p><img src="{{ site.baseurl }}/assets/images/headers/target-web_endpoint.png" alt="" /></p>

<p>This target must be able to support the following requirements:</p>
<ol>
<li>Sending record data (piped from a tap) to a url endpoint via HTTP GET or HTTP Post.<br /><br /></li>
<li>Configuration of basic auth credentials and HTTP Headers for HTTP Post method.&nbsp;<br /><br /></li>
<li>Configuration to map source data field names to target system's data field names.&nbsp;<br /><br /></li>
<li>Configuration to specify additional properties (with static values) to send to endpoint.&nbsp;<br /><br /></li>
<li>Configuration to specify VERY BASIC filter rules based on the record values.</li>
</ol>
<div>&nbsp;</div>
<h4>Helpful links to get background on Developing Singer Targets</h4>
<ul>
<li>Singer provides&nbsp;<a href="https://github.com/singer-io/getting-started/blob/master/docs/RUNNING_AND_DEVELOPING.md#developing-a-tap" target="_blank">getting started docs</a>&nbsp;on creating targets.&nbsp;</li>
<li><a href="https://meltano.slack.com/" target="_blank">Meltano's slack</a>&nbsp;has a dedicated channel&nbsp;<a href="https://meltano.slack.com/?redir=%2Farchives%2FC01RKUVUG4S" target="_blank">#singer-target-development</a>&nbsp;for help developing targets&nbsp;</li>
</ul>

<h4>Setting up development for Singer Target</h4>
<p>In order to develop a tap, we need to install the Singer library:</p>

    pip install singer-python

<p>Next we'll install&nbsp;<a href="https://www.cookiecutter.io/" target="_blank">cookiecutter</a>&nbsp;and download the&nbsp;<a href="https://github.com/singer-io/singer-target-template" target="_blank">target template</a>&nbsp;to give us a starting point:</p>

    pip install cookiecutter
    cookiecutter https://github.com/singer-io/singer-targer-template.git
    project_name [e.g. 'tap-facebook']: target-web_endpoint
    package_name [target_web_endpoint]:target_web_endpoint

<h4>Configuration file for the Target</h4>
<p>There is a template you can use at&nbsp;<em>config.json.example</em>, just copy it to&nbsp;<em>config.json</em>&nbsp;in the repo root and update the following values:</p>

    {
        "method" : "POST",
        "url": "https://api.some_web_site.com/lisitngs/",
        "username": "my_username",
        "password": "my_password",
        "post_headers" : {
            "Content-Type": "application/x-www-form-urlencoded"
        },
        "property_mapping": {
            "field1": { "target_field_name": "target_field_1"},
            "field2": { "target_field_name": "target_field_2"},
            "field3": { "target_field_name": "target_field_3"},
            "field4": { "target_field_name": "field_4"},
            "field5": { "target_field": "field_5"}
        },
        "additional_properties": {
            "system_id": 12,
            "special_key": "0cf18148-1687-11ee-be56-0242ac120002"
        },
        "filter_rules": {
            "field1": { "type": "equals", "value": true },
            "field2": { "type": "not_equals", "value": "123" },
            "field3": { "type": "is_empty", "value": false }
        }
    } 


<table border="1" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td style="padding: 5px;"><strong>Variable</strong></td>
<td style="padding: 5px;"><strong>Description</strong></td>
</tr>
<tr>
<td style="padding: 5px;">method</td>
<td style="padding: 5px;">method for calling url (GET or POST), default is GET</td>
</tr>
<tr>
<td style="padding: 5px;">url</td>
<td style="padding: 5px;">endpoint url&nbsp;<strong>REQUIRED</strong></td>
</tr>
<tr>
<td style="padding: 5px;">username</td>
<td style="padding: 5px;">user name for basic auth (only for POST)</td>
</tr>
<tr>
<td style="padding: 5px;">post_headers</td>
<td style="padding: 5px;">dict of headers to pass (only for POST)</td>
</tr>
<tr>
<td style="padding: 5px;">property_mapping</td>
<td style="padding: 5px;">define the properties received from tap to be sent to the endpoint. You can update the target property names)</td>
</tr>
<tr>
<td style="padding: 5px;">additional_properties</td>
<td style="padding: 5px;">define additional properties with hard coded values that will be sent to the endpoint</td>
</tr>
<tr>
<td style="padding: 5px;">filter_rules</td>
<td style="padding: 5px;">configure rules to only include records when matching all criteria.</td>
</tr>
</tbody>
</table>
<p>&nbsp;</p>
<p><span style="text-decoration-line: underline;">Notes about filter_rules:</span></p>
<p>1. Records will be sent to the endpoint only when they are valid for all the configured rules.<br />2. You can only identify one rule for each field.<br />3. There are 5 supported types of rules:</p>
<table border="1" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td style="padding: 5px;"><strong>Rule Type</strong></td>
<td style="padding: 5px;"><strong>Description</strong></td>
</tr>
<tr>
<td style="padding: 5px;">equals</td>
<td style="padding: 5px;">the field's value must equal the configured value</td>
</tr>
<tr>
<td style="padding: 5px;">not_equals</td>
<td style="padding: 5px;">the field's value must not equal the configured value</td>
</tr>
<tr>
<td style="padding: 5px;">contains</td>
<td style="padding: 5px;">the field's value must contain the configured value</td>
</tr>
<tr>
<td style="padding: 5px;">not_contains</td>
<td style="padding: 5px;">the field's value must not contain the configured value</td>
</tr>
<tr>
<td style="padding: 5px;">is_empty</td>
<td style="padding: 5px;">if true, the field's value must not be empty. if false, the field's value must be empty</td>
</tr>
</tbody>
</table>

<h4>Setting up to run the Target</h4>
<p>Let's create a virtual environment to run our tap within:</p>

    cd target-web_post
    python3 -m venv ~/.virtualenvs/target-web_endpoint
    source ~/.virtualenvs/target-web_endpoint/bin/activate
    git clone git@github.com:jaygrossman/target-web_endpoint.git
    cd target-web_endpoint
    pip install requests
    pip install -e .
    deactivate 

<p>We can pipe the output of a tap to our target with the following command (after the | symbol):</p>
    run_your_tap | ~/.virtualenvs/target-web_endpoint/bin/target-web_endpoint

<h4>EXAMPLE: Running Tap-Csv + Target-web_endpoint&nbsp;</h4>
<p>I created a sample_data folder in the project's github repo that includes:</p>
<ol>
<li>sample_data.csv file contains a github keyword search</li>
<li>tap-csv.config.json file contains config for the tap-csv</li>
<li>target-web_endpoint.config.json file contains config for the target-web_endpoint</li>
</ol>
<p>Calling this thread will try to search github (https://github.com/search) via a HTTP GET request with the keywords supplied in sample_data.csv.</p>
<p>Install tap-csv:</p>

    python3 -m venv ~/.virtualenvs/tap-csv
    source ~/.virtualenvs/tap-csv/bin/activate
    pip install git+https://github.com/MeltanoLabs/tap-csv.git
    deactivate

<p>We can run tap-csv piped to our target-web_endpoint with the following command:</p>
    ~/.virtualenvs/tap-csv/bin/tap-csv --config sample_data/tap-csv.config.json | ~/.virtualenvs/target-web_endpoint/bin/target-web_endpoint --config sample_data/target-web_endpoint.config.json

<p>The command outputs the following:</p>
<p style="padding-left: 30px;">2023-06-29 23:17:10,957 | INFO&nbsp; &nbsp; &nbsp;| tap-csv&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; | Beginning full_table sync of 'seaches'...<br />2023-06-29 23:17:10,957 | INFO&nbsp; &nbsp; &nbsp;| tap-csv&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; | Tap has custom mapper. Using 1 provided map(s).<br />2023-06-29 23:17:10,957 | INFO&nbsp; &nbsp; &nbsp;| singer_sdk.metrics&nbsp; &nbsp;| METRIC: {"type": "timer", "metric": "sync_duration", "value": 0.000225067138671875, "tags": {"stream": "searches", "context": {}, "status": "succeeded"}}<br />2023-06-29 23:17:10,957 | INFO&nbsp; &nbsp; &nbsp;| singer_sdk.metrics&nbsp; &nbsp;| METRIC: {"type": "counter", "metric": "record_count", "value": 1, "tags": {"stream": "searches", "context": {}}}<br />url: https://github.com/search?q=tap-ebaycompleted, response: &lt;Response [200]&gt;<br />{"bookmarks": {"searches": {}}}</p>
<p>&nbsp;</p>
