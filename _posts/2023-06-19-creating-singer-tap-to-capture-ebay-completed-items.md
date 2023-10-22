---
layout: post
title:  "Creating Singer Tap to Capture Ebay Completed Items"
author: jay
tags: [ data engineering, singer, ebay, python, open source ] 
image: assets/images/headers/tap-ebaycompleted.png
description: "Creating Singer Tap to Capture Ebay Completed Items"
featured: false
hidden: false
comments: false
---

 <h3>What is Singer</h3>
<p>The <a href="https://www.singer.io/" target="_blank">Singer specification</a> bills itself as <em>"the open-source standard for writing scripts that move data"</em>. <a href="https://transferwise.github.io/pipelinewise/" target="_blank">PipelineWise</a> and <a href="https://meltano.com/" target="_blank">Meltano</a> are popular open source platforms that use the Singer specification to accommodate ingest and replication of data from various sources to various destinations.</p>
<p>Singer describes how data extraction scripts&mdash;called <strong>"taps"</strong>&nbsp;&mdash;and data loading scripts&mdash;called <strong>"targets"</strong>&mdash; should communicate, allowing them to be used in any combination to move data from any source to any destination. Send data between databases, web APIs, files, queues, and just about anything else you can think of.</p>


<p><img src="{{ site.baseurl }}/assets/images/ebaytap_1.png" alt="" /><br>
<small>ETL Pipeline in Singer, image credit: <a href="https://blog.panoply.io/etl-with-singer-a-tutorial" target="_blank">panoply</a></small></p>

<h3>Completed Items on eBay</h3>
<p>For over 20 years, eBay has allowed users to search by keyword for auctions and Buy It Now listings that have recently ended. This is very helpful to provide buyer and sellers with a directional idea of how much comparable items sell for.</p>
<p>On the left side of any search page (near the bottom of the search results), users have the ability to filter by Completed Items as shown below:</p>

<p><img src="{{ site.baseurl }}/assets/images/ebaytap_2.png" alt="" /></p>

<p>The screen below shows a search for the search term "iphone 14":</p>

<p><img src="{{ site.baseurl }}/assets/images/ebaytap_3.png" alt="" /><br>
<small>URL:&nbsp;<a href="https://www.ebay.com/sch/i.html?LH_Complete=1&amp;_nkw=iphone+14" target="_blank">https://www.ebay.com/sch/i.html?LH_Complete=1&amp;_nkw=iphone+14</a></small></p>

<h3>The Challenge</h3>
<p>For a long time I've wanted a standard method to be able to capture data about items that have sold on eBay and have an easy + repeatable way to save the data (to files, a database, etc.). I have pieced together various scripts in different languages (python, powershell, C#, php) at different times to accomplish this, but I wanted it to be based off a more standard and extensible framework.</p>
<p>eBay offers&nbsp;<a href="https://developer.ebay.com/develop/apis" target="_blank">developer APIs</a>&nbsp;to query their data which I have used. While I could use the API's&nbsp;<a href="https://developer.ebay.com/devzone/finding/callref/findCompletedItems.html" target="_blank">findCompletedItems</a>&nbsp;end point, it is a far more straight forward implementation and faster learning opportunity for me to use some common python libraries to get the data from eBay's public web site.</p>
<div>&nbsp;</div>
<h3>tap-ebaycompleted</h3>
<p>Code for this Singer Tap is posted on github (click on image below):</p>

<p><a href="https://github.com/jaygrossman/tap-ebaycompleted" target="_blank"><img src="{{ site.baseurl }}/assets/images/github_tap-completed.png" alt="" style="border:1px solid blue;" /></a></p>


<h4>Project Scope</h4>
<p>So the goal is to create a Singer tap that will allow us to generate out properly formatted JSON data with the details of the listing, that can be consumed by a Singer target (such as writing to a .csv file, an API or PostGres database). Below is a visual illustration:</p>

<p><img src="{{ site.baseurl }}/assets/images/ebaytap_5.png" alt="" /></p>

<h4>Helpful links to get background on Developing Singer Taps</h4>
<ul>
<li>Singer provides&nbsp;<a href="https://github.com/singer-io/getting-started/blob/master/docs/RUNNING_AND_DEVELOPING.md#developing-a-tap" target="_blank">getting started docs</a>&nbsp;on creating taps.&nbsp;</li>
<li>Meltano provides <a href="https://sdk.meltano.com/en/latest/code_samples.html" target="_blank">code samples</a> for developing taps&nbsp;</li>
</ul>
<div>There are 2 functions in the <a href="https://github.com/singer-io/singer-python" target="_blank">singer python library</a>&nbsp;for my simple tap that we must care about:</div>
<div><ol>
<li>singer.write_schema - responsible for defining the JSON schema that data will be outputted</li>
<li>singer.write_records - responsible for outputting each record (data row)</li>
</ol></div>
<h4>&nbsp;</h4>
<h4>Setting up development for Singer Tap</h4>
<p>In order to develop a tap, we need to install the Singer library:</p>

    pip install singer-python

<p>&nbsp;Next we'll install <a href="https://www.cookiecutter.io/" target="_blank">cookiecutter</a> and download the <a href="https://github.com/singer-io/singer-tap-template" target="_blank">tap template</a> to give us a starting point:</p>

    pip install cookiecutter
    cookiecutter https://github.com/singer-io/singer-tap-template.git
    project_name [e.g. 'tap-facebook']: tap-ebaycompleted
    package_name [tap_ebaycompleted]:


<h4>Defining the schema</h4>
<p>The JSON schema defines the structure that the data will be outputted by the tap. Below I am writing a small inline python function to do so:&nbsp;</p>


    def get_schema():
    schema = {
        "properties": {
            "search_term": {"type": "string"},
            "title": {"type": "string"},
            "price": {"type": "string"},
            "bids": {"type": "string"},
            "buy_it_now": {"type": "boolean"},
            "condition": {"type": "string"},
            "image": {"type": "string"},
            "link": {"type": "string"},
            "end_date": {"type": "string"}
            "has_sold": {"type": "boolean"}
            "id": {"type": "string"}
            }
        }
    return schema

<p>We later call this function to create the schema object:</p>

    schema = get_schema()
    singer.write_schema("completed_item_schema", schema, "id")

<h4>Getting data from eBay's Completed Items Search Results</h4>
<p>In python, there are a few different libraries to parse the contents of a web page. In this tap, I use:</p>
<ul>
<li><a href="https://requests.readthedocs.io/en/latest/" target="_blank">requests</a>&nbsp;for getting the HTML of the page&nbsp;</li>
<li><a href="https://beautiful-soup-4.readthedocs.io/en/latest/" target="_blank">BeautifulSoup 4</a>&nbsp;for parsing the elements on the page</li>
</ul>
<p>While I added some extra logic in the actual tap, below will provide the basic idea of how we can parse the page if you are new to these libraries:</p>


    search_term = 'iphone 13'
    url = "https://www.ebay.com/sch/i.html?LH_Complete=1&_sop=13&_nkw={search_term}"

    response = requests.get(url)
    html_content = str(response.content)
    soup = BeautifulSoup(html_content, "html.parser")

    # Each completed item is within a <li> tag with class=s-item s-item__pl-on-bottom
    listings = soup.find_all("li", class_="s-item s-item__pl-on-bottom")

    # Iterate over completed listings
    for listing in listings:
        title = listing.find("div", class_="s-item__title").text
        price = listing.find("span", class_="s-item__price").text
        condition = listing.find("span", class_="SECONDARY_INFO").text
        image= listing.find("img")['src']
        link = listing.find("a", class_="s-item__link")['href']
        id = link[0:link.index("?")].replace("https://www.ebay.com/itm/", "")
        bids = ""
        try:
            bids = listing.find("span", class_="s-item__bids s-item__bidCount").text
        except:
            bids = ""
        buy_it_now = False
        try:
            if listing.find("span", class_="s-item__dynamic s-item__buyItNowOption").text == "Buy It Now":
                buy_it_now = True
        except:
            buy_it_now = False
        has_sold = False
        try:
            if listing.find("div", class_="s-item__title--tag").find("span", class_="clipped").text == "Sold Item":
                has_sold = True
                end_date=listing.find("div", class_="s-item__title--tag").find("span", class_="POSITIVE").text
            else:
                end_date = listing.find("div", class_="s-item__title--tag").find("span", class_="NEGATIVE").text
        except:
            end_date=""

        record = {
            "search_term": search_term,
            "title": title,
            "price": price,
            "condition": condition,
            "image": image,
            "link": link,
            "id": id,
            "bids": bids,
            "buy_it_now": buy_it_now,
            "end_date": end_date,
            "has_sold": has_sold
        } 



<p>We can then pass the schema ("completed_item_schema") that we defined earlier and the record to singer.write_records:</p>

    singer.write_records("completed_item_schema", [record])

<h4>Building and Configuring the Tap</h4>
<p>The template created a directory named tap-ebaycompleted. Since this is a very simple tap, I removed the contents and created a single file called <a href="https://github.com/jaygrossman/tap-ebaycompleted/blob/main/tap_ebaycompleted/__init__.py" target="_blank">__init__.py</a>. In the main function, I provided the logic specified in the sections above with a little bit of additional logic.</p>
<p><span style="text-decoration: underline;">Create a configuration file</span></p>
<p>There is a template you can use at <em>config.json.example</em>, just copy it to <em>config.json</em> in the repo root and update the following values:</p>

    {
        "search_terms": ["iphone 13", "iphone 14"],
        "page_size": 240,
        "min_wait": 2.1,
        "max_wait": 4.4
    } 

<table border="1" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td style="padding: 5px;"><strong>Variable</strong></td>
<td style="padding: 5px;"><strong>Description</strong></td>
</tr>
<tr>
<td style="padding: 5px;">search_terms</td>
<td style="padding: 5px;">list of terms that the tap will search for <strong>REQUIRED</strong></td>
</tr>
<tr>
<td style="padding: 5px;">page_size</td>
<td style="padding: 5px;">number of records to return (values can be 240,120,60), default is 120</td>
</tr>
<tr>
<td style="padding: 5px;">min_wait</td>
<td style="padding: 5px;">minimum amount of time between searches, default is 2 seconds</td>
</tr>
<tr>
<td style="padding: 5px;">max_wait</td>
<td style="padding: 5px;">maximum amount of time between searches, default is 5 seconds</td>
</tr>
</tbody>
</table>
<p>&nbsp;</p>
<h4>Running the Tap</h4>
<p>Let's create a virtual environment to run our tap within:</p>

    cd tap-ebaycompleted
    python3 -m venv ~/.virtualenvs/tap-ebaycompleted
    source ~/.virtualenvs/tap-ebaycompleted/bin/activate
    git clone git@github.com:jaygrossman/tap-ebaycompleted.git
    cd tap-ebaycompleted
    pip3 install requests
    pip3 install BeautifulSoup4
    pip3 install .
    deactivate 

<p>We can run our tap with the following command:</p>
    ~/.virtualenvs/tap-ebaycompleted/bin/tap-ebaycompleted -c config.json
    
<p>Below is a sample record representing a completed item:</p>

    {
        "type": "RECORD", 
    "stream": 
    "completed_item_schema", 
    "record": {
        "search_term": "iphone 13", 
        "title": "Apple iPhone 13 - 128GB - Midnight", 
        "price": "$411.00", 
        "condition": "Pre-Owned", 
        "image": "https://shorturl.at/bqrsK", 
        "link": "https://www.ebay.com/itm/314645218752", 
        "id": "314645218752", 
        "bids": "", 
        "buy_it_now": false
        "end_date": "Jun 21, 2023"
        "has_sold": True
        }
    }

<h4>Running the Tap with a Target to Export the Data to a .csv File</h4>
<p>Install target-csv:</p>

    python3 -m venv ~/.virtualenvs/target-csv
    source ~/.virtualenvs/target-csv/bin/activate
    pip3 install target-csv
    deactivate

<p>We can run our tap piped to target-csv with the following command:</p>

    ~/.virtualenvs/tap-ebaycompleted/bin/tap-ebaycompleted -c config.json |
    ~/.virtualenvs/target-csv/bin/target-csv 


<p>Below is the first couple of lines of the .csv file the command generated:</p>
<p style="padding-left: 30px;">search_term,title,price,condition,image,link,id,bids,buy_it_now,end_date,has_sold</p>
<p style="padding-left: 30px;">iphone 13,Apple iPhone 13 - 128GB - Midnight (Verizon),$170.00,Parts Only,https://i.ebayimg.com/thumbs/images/g/AKkAAOSw55lkhLVt/s-l300.jpg,https://www.ebay.com/itm/225623521919,225623521919,0 bids,False,"Jun 21, 2023",True</p>

<h3>Conclusions and What's Next</h3>
<ul>
<li>You should not consider this a production quality tap. eBay often changes the format of the HTML on their search results page and this Tap will need to be updated when this occurs.&nbsp;&nbsp;<br /><br />The&nbsp;<a href="https://github.com/singer-io/tap-ebay" target="_blank">tap-ebay</a>&nbsp;project (developed by <a href="https://www.linkedin.com/in/drewbanin/" target="_blank">Drew Banin</a> of Fishtown/dbt) is well done. It uses the fulfillment API to allow a seller to query for their orders received via eBay's marketplace.&nbsp; I may refactor this tap using some of Drew's concepts.<br /><br /></li>
<li>Singer is very robust and powerful. It has pretty slick support for Authentication to data source and State management that I did not need in this very simple idempotent example.<br /><br /></li>
<li>This python code only returns the first page of results for the provided search terms. It would be beneficial to add in support to iterate through multiple pages of results.<br /><br /><span style="color: #ff0000;"><strong>Update 2023-06-27:</strong></span> Added support to configure the maximum search result pages to capture records from.<br /><br /></li>
<li>I'd like to extend the tap with the option of retrieving the list of search terms getting pulled from web url or an API call.<br /><br /><span style="color: #ff0000;"><strong>Update 2023-06-28:</strong></span>&nbsp;Added support to configure a public url where search terms and corresponding SKUs can be fed in from.<br /><br /></li>
<li>I will probably build a new Singer target to persist the data from this tap&nbsp;to my private API . And then I can orchestrate the pipeline on a cron with either <a href="https://dagster.io/" target="_blank">Dagster</a> or try out Pipelinewise.<br /><br /><span style="color: #ff0000;"><strong>Update 2023-06-30:</strong></span>&nbsp;Created a configurable singer target with blog post at&nbsp;<a href="/creating-singer-target-to-send-data-to-web-endpoint/">Creating Singer Target to Send Data to Web Endpoint</a></li>
</ul>
<p>&nbsp;&nbsp;</p>