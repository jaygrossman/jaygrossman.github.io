---
layout: post
title:  "My favorite data science interview question"
author: jay
tags: [ data science ] 
image: assets/images/headers/data_science_what_to_buy.png
description: "My favorite data science interview question"
featured: false
hidden: false
comments: false
---

<p>I generally prefer to structure interviews for data candidates with open discussions around topics that are fundamental to the company they are interviewing with, as opposed to generic toy examples or brain teasers. There is a high likelihood that this approach introduces them to types of challenges they may experience and the underlying complexities. I make it very clear that we are not using the interview process to get free solutions to current problems from candidates.</p>

<h2>The Question:</h2>

<p><a href="https://renttherunway.com" target="_blank">Rent the Runway</a> (RtR for short) is an online platform that allows users to rent, subscribe to, or buy designer clothing and accessories. From 2016-2020, I led various data teams there and got to interview + hire lots of smart folks. I would usually ask candidates my favorite question:</p>



<h2><span style="color: #ff0000;"><em><strong>What clothes should Rent the Runway buy and at what price?</strong></em></span></h2>


<h4>Ground rules and assumptions:</h4>

<p>
<ul>
<li>30-45 minute discussion.</li>
<li>I start high level and add context + complexity.</li>
<li>Candidates encouraged to whiteboard ideas.</li>
<li>Candidate could ask questions.</li>
</ul>
</p>

<h4>Why I love this question:</h4>
<p>
<ol>

<li><u>Real World Problem</u> - Inventory acquisition is core to RtR's business. Most candidates walked away learning new things and more interested in the company after the discussion.<br>
<br></li>

<li><u>Dealing with something new</u> - I am looking to see how candidates react to something they probably hadn't thought about, since almost all of them didn't have merchandising backgrounds. I want to understand:
<ul>
  <li>How do they think through the problem?</li>
  <li>How do they structure their response & communicate?</li>
  <li>How well do they understand business, specifically RtR's business?</li>
  <li>What questions do they ask?</li>
</ul></li>

<li><u>Adapting to new info</u> - I added more details about the business as we progressed, introducing nuanced complexity. Often details they wouldn't know unless they were familiar with RtR's operations. I'm interested in observing how they react as their initial assumptions are challenged.</li>
</ol>
</p>


<h4>Some things that make this harder + interesting:</h4>

<p>
<ol>
<li><b>RtR was creating a new category</b> (clothing rental at scale) and it was very different from traditional retail in several ways:

<ul>
<li>The inventory needed to last through many wears and cleanings.</li>
<li>Items may need to have a lifetime of several years to reach profitability. This means that capital is tied up and longer payback timeframes.</li>
<li>The fashion and size needs of RtR's customer base may be different than many retailers, so assortment choices and depths of sizes are more nuanced.</li>
<li>Retailers generally discount excess inventory toward the end of a season (to recoup capital and free space). End of life considerations/strategies for rentals are likely different.</li>
</ul>
</li>

<li><b>RtR was in growth mode</b> after significant VC investment, always looking to aggressively expand their customer base and offerings. Inventory acquisition was the largest capital expense for the company, so maximizing this investment area was always a top priority. This meant we had to consider how to balance the needs of their existing customers (minimizing churn) while cost efficiently attacting new cohorts of customers.<br>
<br>
The finance team did amazing jobs to build out models + forecasts for many potential growth targets, considering many complex assumptions and trade offs. The buying and merchandising teams had to balance multiple forecast scenarios with the realities of the fashion industry and the operational constraints of the business.
<br>
<br></li>

<li><b>RtR has created multiple offerings</b> that could serve different customer profiles: 
<ul>
<li><u>One-time rental</u> - you rent a dress for 4 or 8 days for a specific event.</li>
<li><u>Subscription</u> - you pay a monthly fee to rent a certain number of items at a time, allowing you to swap items whenever you want.</li>
<li><u>Try to Buy</u> - you can buy items instead of returning them.</li>
</ul>
Each of these offerings may have different customer segments, different pricing strategies, and different inventory needs. A large percentage of the inventory was made available for multiple offerings and customers often switched between offerings.
It is especially interesting to consider that the user activity for each offering may directly impact the inventory considerations for the other offerings.<br>
<br>
Please note - RtR has further iterated since 2020 to significantly change their offerings, adding more options and complexity.<br>
<br>
</li>

</ol>
</p>

<hr>
<h2>Things I looked for in the Interview:</h2>
<p>
As with many data science related problems, there are not completely right or wrong answers to the question. I am generally expect candidates to demonstrate understanding of the business and how they may consider the aspects of the problem:

<ul>
    <li><a href="#Data">Discussions about our Data</a></li>
    <li><a href="#Demand">Discussions around Demand</a></li>
    <li><a href="#UnitEconomics">Discussions around Unit Economics</a></li>
    <li><a href="#OperationalConcerns">Discussions around Operational Concerns</a></li>
    <li><a href="#General">General Interview Aspects</a></li>
</ul>
</p>
<source id="Data"/>
<h4>Discussions about our Data:</h4>

<p>It's likely going to be pretty hard to do analysis and potentially build models if you don't have relevant data available! Hopefully there is some brainstorming around some the types of data we can hope to have for this exercise. Some common areas include:</p>
<ul>
<li>Listings of items bought from previous seasons, along with the inventory costs and rental history.</li>
<li>Customer feedback for items (RtR has millions of reviews, requiring subscribers to rate each item rented).</li>
<li>Customer shopping experience details.</li>
<li>Operational details (costs, delivery times, cleaning/repair history, customer service interactions, etc).</li>
<li>Current and upcoming trends that should be considered.</li>
</ul>

<source id="Demand"/>
<h4>Discussions around Demand:</h4>

<p>Since part of the question is <i>"What to buy?"</i>, pretty much every candidate spends some time talking about how they think about demand for the items we buy.</p>

<p>Better candidates talk about how understanding demand is crucial when deciding what inventory to buy for a clothing rental business for several reasons:</p>

<p><u>Maximizing Profitability</u><br/>
Startups need efficient use of their capital. Understanding demand helps ensure that we invest in items that will generate profitable revenue. </p>

<p><u>Meeting Customer Expectations</u><br/>
Demand-driven inventory decisions allow for RtR to have items that customers want to rent (that fit their lifestyles and rental use cases), better availability of popular items during peak seasons and selection diversity that caters to various customer preferences.</p>

<p><u>Seasonal and Trend Considerations</u><br/>
Fashion is highly dynamic, with trends and demand fluctuating based on seasons and events.</p>

<p><u>Inventory Management Efficiency</u><br/>
Effectively matching demand allows for less overstocking/ understocking, lower storage costs for slow-moving items and streamlined your inventory management process.</p>

<p>Some candidates would go on to discuss how they could model demand. Some would talk about the relationship between demand and personalized recommendations to customers. Some even talked about segmenting customers into target groups (based on size, geography, fashion persona, etc.) and discussed how RtR needed to have enough desirable inventory to attract + keep users.</p>

<source id="UnitEconomics"/>
<h4>Discussions around Unit Economics:</h4>

<p>Since the second part of the question is <i>"At what price?"</i>, usually there were some discussions about item level profitability and about how to spend our inventory budget.</p>

<p>Having multiple offerings required RtR to consider the profitability of each item in their inventory for each use case. The way we would calculate the revenue associated with an item from a one-time rental for 4 days would be different than one of the items that was rented as part of a subscription for the same 4 days.</p>

<p>
<u>One-time rental (simple model):</u><br/>
I would generally start with asking candidates to talk through a methodology to calculate profitability for items rented exclusively via the one-time rental business. <br>
<br>
Successful candidates would arrive at a formula:<br>
<ul>
<li>
unit revenue = (number of rentals * average revenue rental) + final sale revenue</li>
<li>
unit costs = initial cost of the item + (number of rentals * average incremental logistics costs of each rental)</li>
<li>unit profitability = unit revenue - unit costs</li>
</ul>
</p>
RtR offered customers a "free" backup size with each rental to minimize issues when the primary size choice did not fit customers. When we added this context, candidates would need to consider how to attribute across multiple item orders. 

<p>
<u>Subscription:</u><br/>
If the candidate did well with the simple model, I would introduce the subscription model. This would require candidates to think about how to attribute the revenue contribution for items rented via the subscription model. <br>
<br>
Let's illustrate a scenario for subscription:<br>
- Monthly subscription cost: $149<br>
- Number of slots per month: 4<br>
- Total days in the month: 31<br>
<br>
Let's say a Ralph Lauren dress (item sku 123) was rented for 8 days in this 31-day subscription period, how would you attribute the revenue for this item?<br>
<br>
For our example, we track that the number of days the user had items in the 31 day period:<br>
- slot 1 = 12 days with items<br>
- slot 2 = 14 days with items<br>
- slot 3 = 22 days with items<br>
- slot 4 = 31 days with items<br>
<br>
At the end of the month, can count up the total number of days items were rented across all 4 slots for the month (12 + 14 + 22 + 31 = 79).  Then we can divide the Monthly subscription cost by number of days with items (average daily item revenue attribution = $149/79=$1.89)<br>
<br>
To get the revenue for this item from this subscription, we can multiply the number of days the item was rented (8) by the average daily item revenue attribution ($1.89) to get the attributed revenue for the item ($15.12).<br>
<br>
We then subtract the blended average unit costs ($5.02 for prorated shipping, repairs, etc.) of a subscription item to get the profitability for the item as part of this subscription ($15.12 - $5.02 = $10.10).<br>
<br>
We can calculate the lifetime item profitability of subscriptions by adding up all of the item's profitability from each subscription rental.
</p>

<source id="OperationalConcerns"/>
<h4>Discussions around Operational Concerns:</h4>

<p>Very few candidates brought up operational aspects that may influence decisions around inventory, but here are some very important considerations by the RtR's various teams:</p>

<p><u>Durability</u><br>
These items need to last and be available for many rentals (sometimes >30). Items with delicate materials or that wore down quickly were often eliminated during buying cycles. The cost of repairs and replacements can be significant.</p>

<p><u>Shipping & Delivery</u><br>
Were the items cost effective for shipping to users? Some larger items, footwear and accessories did not fit well in the standard Rent the Runway shipping bags.</p>

<p><u>Cleaning</u><br>
RtR has the biggest dry cleaner facility in North America. While the cleaning expertise is likely unparalleled, it is not always efficient to have multiple rounds for cleaning certain types of materials. Also some items tend to have more frequent challenges when trying to treat stains.</p>

<p><u>Logistics</u><br>
RtR implemented automation around their warehouses for moving/tracking inventory (via scanning bar codes and later RFID), and not all types of inventory are well suited for the automation requirements. In addition, larger and non-standard items can cause delays in both inbound and outbound warehouse process flows.</p>

<source id="General"/>
<h4>General Interview Aspects:</h4>

<p>In addition to the mechanics of trying to answer the question, I am also looking for the following behavior I have seen in effective data scientists:</p>

<p><u>Framing the problem</u><br>
It is interesting how candidates begin their attempt to come up with their responses. Some specific things I look for:
<ul>
<li>Do they try to define what would constitute success?</li>
<li>Do they ask for context and/or look to validate any assumptions, or do they immediately dive into providing a solution?</li>
<li>Do they spend time explaining their thought process and/or how they can associate this problem with others they have seen?</li>
<li>Do they try to break the hard problem into digestible parts?</li>
</ul>
</p>
<p>It's worth noting that there were quite a few candidates that immediately tried to identify the machine learning models they would try without really understanding the scope of the problem. Once I provided feedback about their solution, some folks plowed through with an incorrect path while others adjusted their approach.</p>

<p><u>Communication Style</u><br>
Assuming I am their stakeholder for this problem, how do they interact with me? I am looking for candidates to be able to explain their thoughts clearly and concisely. How do they react when the parameters of the problem change or their explanations are challenged? I am looking for cultural red flags in their responses, as I am inclined to hire gritty folks over brilliant/condescending jerks. How do they use visual cues effectively to tell a stroy and/or express their points (like drawing things on a whiteboard)?</p>

<p><u>Questions I get asked</u><br>
I generally try to leave the last 10-15 minutes of the interview for the candidate to ask me questions. I am looking for candidates to ask questions that show they are interested in the company, the teams, the problem space and most importantly learning.</p>

<p>The most memorable candidates asked detailed questions that surprise me or lead me to learn new things.  Some have led to interesting discussions about hobbies, career aspirations, side projects, team dynamics, management styles and learnings from being a parent.</p>


<hr>
<h2>Other parts of the interview:</h2>
<p>While I like to think that the session with my question was always a highlight, the RtR data interview process generally also included:</p>
<p>
<ul>
  <li><u>Phone screen:</u> discuss the role + company + team + opportunities and the candidate's interest + background.</li>
  <li><u>Technical exercise:</u> either a take home project or 45-60 minute in person pair programming session. Example exercises were analysis of a customer data set, building data pipelines + data models or coding a recommendation engine.</li>
  <li><u>Cultural + team fit</u>: candidates meet with 1-2 stakeholders (often product managers) to talk about how they would interact within project team scenarios.</li>
  <li><u>Project deep dive:</u> more senior level candidates would present a previous data project to (roughly 10-20) data team members and stakeholders  - sharing the business problem + implementation approach + results.</li>
</ul>
</p>


