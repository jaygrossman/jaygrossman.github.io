---
layout: post
title:  "Methodology for Building a Product Valuation Platform"
author: jay
categories: [ analysis, business ]
tags: [ SportsCardDatabase, market value, methodology, analysis ]
image: assets/images/headers/valuation_platform.png
description: "Methodology for Building a Product Valuation Platform"
featured: false
hidden: false
comments: false
redirect_from:
  - /post/2012/08/23
#rating: 4.5
---


 <p>As I have mentioned in previous posts, understanding pricing and market values has been a passion of mine for over 20 years.</p>
<p>With many problems I encounter (especially those related to business or technology), I try to follow these steps borrowed from agile software development methodology:</p>
<ul>
<li>DEFINE - Define the problem/goals, identifying clear and measurable requirements.&nbsp;</li>
<li>ANALYZE - Analyze the each requirement to identify or design potential solutions.&nbsp;</li>
<li>IMPLEMENT - Establish/Implement tested and repeatable processes to accommodate each of the requirements.&nbsp;</li>
<li>AUTOMATE - Implement in a process/method to regularly look for opportunities for optimization and to identify new important requirements.</li>
</ul>
<p>So I tried to follow these steps to find a way to determine market values for products.</p>
<p><span style="text-decoration: underline;">Define the Problem</span></p>
<p>The initial goals (requirements) of the platform were:</p>
<ol>
<li>Ability to determine an accurate market value for any product (card) in my database.</li>
<li>Value determination process must be repeatable/consistent and the platform be able to eventually support millions of products in the targeted product set.</li>
</ol>
<div>Since I was specifically interested in determining the values for sports cards, I focused on that product set.&nbsp;</div>
<p><span style="text-decoration: underline;">Analysis</span></p>
<p>I like to tackle each requirement one at time (when possible), before moving on to the next one. The first one is obviously the most important one, so I needed to come up with a way to determine value for a given sports card record.</p>
<p>Up to that time, I had accumulated a fairly significant amount of transaction data related to sports card sales across many venues. I knew looking at that data was going to be an excellent opportunity for learning. &nbsp;&nbsp;</p>
<p>I wanted to create a standard algorithm to consume listing/transaction data and return a market value. &nbsp;For each product, I quickly was able to calculate averages and medians of recent sale prices and for the asking prices of active listings. I noticed some pretty huge variance depending on the time I was querying for the same product, and that didn't seem like it would be a reliable market value - certainly not usable for making purchasing decisions. &nbsp; So I needed to look deeper</p>
<p>Key Performance Indicators (KPI) is a set of quantifiable measures used to gauge or compare performance in terms of meeting their strategic and operational goals. KPIs vary between companies and industries, depending on their priorities or performance criteria. I decided I would try to identify a collection of such attributes of sales transactions and the product that would effect market value. Some of the initial items I came up with were:</p>
<ul>
<li>selling format of listing - fixed price, auction, reverse auction&nbsp;</li>
<li>marketplace/site where the item is being sold &nbsp;</li>
<li>condition of card&nbsp;</li>
<li>shipping charges</li>
<li>time of day/week/month/year when item is offered and when sale/auction/listing completed</li>
<li>starting price for the listing (for auctions, reverse auctions)</li>
<li>length of time the item is offered before selling</li>
<li>standard deviation of sales and price elasticity</li>
<li>number of listings available for product over time period (week, month, 3 months, 6 months, year)</li>
<li>number of sales for product over time period (week, month, 3 months, 6 months, year)</li>
<li>sales values for product over time (week, month, 3 months, 6 months, year)&nbsp;</li>
<li>the age the item was produced</li>
<li>if the product was autographed</li>
<li>if the product contained game worn memoribilia</li>
<li>if the product &nbsp;had a limited print run</li>
<li>calculated demand score for the item</li>
</ul>
<p>My plan was to come up with a method for weighing the importance of each of the KPI's in relation to one another, in order to establish an optimized algorithm.&nbsp;</p>
<p>In data mining, there are a few different options we could choose for determining relative importance of KPI's. I chose to construct an <a href="http://en.wikipedia.org/wiki/Artificial_neural_network" target="_blank">Artificial Neural Network</a> and loaded several hundred representative random samples to determine the weighting for each KPI.</p>
<p>I also created test cases that could be run after each execution of the valuation algorithm. The test cases validate that the market values were being calculated correctly. I added some exception conditions (such as thresholds for high standard deviations or huge spikes in listed items) that would be recorded and later analyzed.</p>
<p><span style="text-decoration: underline;">Solution Implementation</span></p>
<p>The purpose of this post is to share the methodology of how I went about solving the problem at a high level. I am not going to get into the details of my algorithms, the data I am using, or the code that I wrote. Since there are many different ways to accomplish these items (many programming languages, operating systems, data integration methods, and databases to persist the data), I'll let others have the fun figuring out them like I did.</p>
<p>Once the data structure and factor weightings were determined, I built an application to query for the necessary inputs, calculate the market value, and save the result. I then set up an additional task to run this application for every product in the SportsCardDatabase product catalog.</p>
<p>I also implemented a testing framework that allowed me to validate each calculated market value (Test Driven Development).</p>
<p><span style="text-decoration: underline;">Automation</span></p>
<p>Since there are hundreds of thousands of products in our database, there was no way I could possibly execute my process for each one. I set up automated tasks that would query the list of products, generate calculated values for each, validate the values, and save the valid ones.</p>
<p>I then added functionality to monitor the system's health, compile aggregate statistics for the data processed, &nbsp;and automatically send alerts when there were issues.</p>
<p>In order to understand if there are market changes that would effect the weightings of the KPI's, I've set up a process that randomly picks products with adequate samples and runs them through the artificial neural network. When weightings are returned exceeding certain thresholds, I am alerted so I can further investigate.</p>
<p><strong>Conclusions</strong></p>
<p>So far the results have been really good. I have been able to personally use the information for arbitrage scenarios (to be discussed in a future post). Once I launched&nbsp;<a href="http://www.sportscarddatabase.com/" target="_blank">SportsCardDatabase</a>, I have received mostly positive feedback about what the site offers.</p>
<p>I realize that this is likely a very different approach compared to other companies/groups that offer guidance on the market values for sports cards. The values on&nbsp;<a href="http://www.sportscarddatabase.com/" target="_blank">SportsCardDatabase</a>&nbsp;are lower because they are meant to represent the market values and not retail values (such as Beckett, TuffStuff, etc.). There are some newer sites (much like eBay searches) that simply list sale prices for individual cards (vintagecardprices, cardpricer, valuemycards etc.), and I discussed the limitation of this approach yesterday at <a href="/market_values_from_ebay/">http://www.jaygrossman.com/market_values_from_ebay/</a>.</p>
<p>My algorithm is a continual work in progress. Admittedly, I'd like to dedicate some time looking to better accommodate non commodity products (low print runs, rarities) and products with limited listing/sales data to analyze. For these types, I have seen some unexpected abnormalities that I have to think were outliers being considered too heavily because of small populations.&nbsp;</p>
<p><strong>Future of the Platform</strong></p>
<p>I'm really excited about this. This same type of process could be used to determine market value for other commodity product sets. It would require a well defined catalog of products, well defined KPi's for each product, and access to the sales/listing data. Comic books, stamps, coins, electronics, car parts, who knows what could be next... but it could be fun to try!</p>
