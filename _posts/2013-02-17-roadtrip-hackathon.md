---
layout: post
title:  "Personal Roadtrip Hackathon"
author: jay
categories: [ sports ]
tags: [ SportsCardDatabase, Market Value, analysis, hackathon ]
image: assets/images/headers/hackathon.png
description: "Personal Roadtrip Hackathon"
featured: false
hidden: false
comments: false
redirect_from:
  - /post/2013/02/17
#rating: 4.5
---

 <p >A hackathon is generally an event a small group collaborates together to build a product or prototype in a very short period. Some of the more popular sponsored examples (such as the ones during the&nbsp;<a  href="http://techcrunch.com/events/disrupt-ny-2013/startup-battlefield/">TechCrunch Disrupt series</a>) usually last for 24 hours.&nbsp;</p>
<p >For a while, I have been intrigued by the concept and have wanted to see what I can accomplish. Unfortunately I have not had the opportunity to enter one with my always busy schedule and family commitments.&nbsp;</p>
<p >This weekend I had a planned&nbsp;<a  href="/2013-nba-all-star-weekend/">trip with a friend to Houston for the NBA All Star weekend</a>. It was a unique opportunity since I would have some time to myself as I would not be traveling with my family or colleagues. Since the flight from JFK Airport in NY to Houston is over 4 hours, I could either rest or be productive.</p>
<p ><strong style="margin: 0px; padding: 0px;">The Plan</strong></p>
<p >I needed to pick a project that I was interested in and spend whatever free time I would have over the weekend to work on it. I decided that my project would be to build a platform for determining market values of products in a new vertical. I knew I could leverage some the work I had invested in the&nbsp;<a  href="/building_valuation_platform/" target="_blank">valuation platform for SportsCardDatabase.com</a>.</p>
<p ><strong style="margin: 0px; padding: 0px;">The Work</strong></p>
<p >When the pilot announced we could start using electronics on the flight, it was time for the fun to begin. The following are the high level items I worked on:</p>
<ol>
<li>Defined the data model and KPI's for the new product types&nbsp;<br style="margin: 0px; padding: 0px;" /><br style="margin: 0px; padding: 0px;" /></li>
<li>Set up product database tables to persist this information<br style="margin: 0px; padding: 0px;" /><br style="margin: 0px; padding: 0px;" /></li>
<li>Populated product tables with sample information<br style="margin: 0px; padding: 0px;" /><br style="margin: 0px; padding: 0px;" /></li>
<li>Wrote code module to use the product information and use it to build data gathering rules.<br style="margin: 0px; padding: 0px;" /><br style="margin: 0px; padding: 0px;" /></li>
<li>Wrote code module to interpret the gathering rules and gather the data.<br style="margin: 0px; padding: 0px;" /><br style="margin: 0px; padding: 0px;" /></li>
<li>Set up database tables to store the gathered data.<br style="margin: 0px; padding: 0px;" /><br style="margin: 0px; padding: 0px;" /></li>
<li>Wrote code module to populate the gathered data into the tables.<br style="margin: 0px; padding: 0px;" /><br style="margin: 0px; padding: 0px;" /></li>
<li>Wrote code module to update gathered data as required by business rules.</li>
</ol>
<p >Since I was able to leverage some pre-existing code, I was able to get all of these code modules with sufficient unit tests by the end of the flight. Since I did not have internet access aboard the flight, I was unable to fully integration test functionality for calling partner APIs.</p>
<p >I spent about 1 hour or so the next day testing and debugging the code. I was able to get the system recording the transaction data required. I spent another 20-30 minutes enhancing a variety of log messages.</p>
<p >The following day I spent another hour adding 2 new KPIs to each of the transaction records, meaning the gathering rules and supporting code modules needed to be upgraded.</p>
<p >I was able to run an end to end test of the system (took another 20 minutes), and it met all of the requirements before my return flight home. I have invested roughly 7 hours and I have the most difficult part of my valuation platform for this new vertical. I wasn&rsquo;t overly confident I would be able to finish this functionality before the end of the trip (and I now have my return flight for other things).</p>
<p ><strong style="margin: 0px; padding: 0px;">Next Steps</strong></p>
<p >When I arrive home, I will deploy the system on a proper server and let it run for the next 3-6 months to gather data. Once there is enough data, I can analyze it and calculate market values for the products.</p>
<p >I will continue to add new items for this vertical to the product tables in the database.&nbsp;</p>
<p >Once I have a strong set of regularly updated market values, I can provide a user interface and allow users access to it.</p>
<p ><strong style="margin: 0px; padding: 0px;">Conclusions</strong></p>
<p >By defining a plan for a focused set of functionality, along with the opportunity for uninterrupted work, I was able to produce more and higher quality work than I usually do. If I needed to collaborate with others, there is no way we could have come to consensus and deliver as much in such tight timeframe &ndash; even if we were to divide the work.&nbsp;</p>
<p >I realized that I was really tired after 4 hours of solid work on the flight and my productivity would have likely decreased had I continued without a break. I am not sure I could focus like this for 24 hours straight like the traditional hackathon.</p>
<p >I don&rsquo;t usually do this kind of development without online resources (google, stack overflow, etc.). I was really shocked the system only needed a relatively short amount of debugging without these resources for reference. Unit tests certainly helped!</p>
<p >I kind of like the hackathon format. In 3-6 months, I can try 2 more hackathon events to finish the project focusing on:</p>
<ul>
<li>Performing analysis on the data.<br style="margin: 0px; padding: 0px;" /><br style="margin: 0px; padding: 0px;" /></li>
<li>Building a user interface and reports.</li>
</ul>