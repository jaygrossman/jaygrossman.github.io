---
layout: post
title:  "My Auction Framework MVP"
author: jay
categories: [ sports, business, analysis ]
tags: [ eBay, MVP, sportscollectors, auction, baseball cards ]
image: assets/images/headers/auction_mvp.gif
description: "My Auction Framework MVP"
featured: false
hidden: false
comments: false
#rating: 4.5
---


 <p>I have been a huge fan of eBay since the moment I first found it (back in 1997 or so). It truly was the world changing marketplace because:</p>
<ol>
<li >It quickly gained critical mass with worldwide populations of buyers and sellers.</li>
<li >The auction format is easy to understand and allowed the market to determine the prices.&nbsp;</li>
<li >It was an early web 1.0 company that thrived after the dotcom bust of 2001.</li>
</ol>
<p>Over the years, I have seen both start ups and established companies launch would be competitors to eBay. Some try to stand up some cheap package and others build a custom enterprise solution, but just about every one of them dies off with a lack of interest or sustainable business model.</p>
<p>I have to admit, I have always thought it would be a lot of fun to stand up auction functionality (either as part of an existing site or as a stand alone project). I have built out 2 pretty feature rich versions customizing some canned scripts (one in ASP and another in PHP), but I did not feel they offered enough value to bring to production. I wasn&rsquo;t really happy with the quality of the user experience of either solution and didn&rsquo;t really want to commit myself to needing to extend either of those technologies.</p>
<p><span style="margin: 0px; padding: 0px; text-decoration: underline;"><strong style="margin: 0px; padding: 0px;">The Project</strong></span></p>
<p>I am a big fan of quickly launching usable features and then iterating on them based on the learning from users&rsquo; interactions (lean/agile development). Since I run a community full of prospective auction buyers and sellers (SportsCollectors.Net), I thought it would be a good place to test this type of functionality. &nbsp;</p>
<p>The first thing I needed to do was to determine what use cases my&nbsp;<a  href="http://en.wikipedia.org/wiki/Minimum_viable_product" target="_blank">Minimum Viable Product</a>&nbsp;(MVP) would need to accommodate in order to provide value. My vision was to create some of the core user experience of eBay circa 1999 (well before the recommendations and the magento shopping cart integration).&nbsp;</p>
<p>Since SportsCollectors.Net already had a design, an active user base, user/login functions, messaging, and user rating/feedback, I was able to leverage these elements and not even have to spend time on them. So I could focus on these core auction related functions:</p>
<ul >
<li >Buyers need ability to browse/search for active auctions</li>
<li >Buyers need the ability to view the auction details</li>
<li >Buyers need the ability to place bids (with proxy logic)</li>
<li >Bidders need to see a list of active auctions they have bid on.</li>
<li >Winning bidders need to be sent payment instructions once the auction has ended. This included associating the auction with the Trade Manager framework so feedback could be given.</li>
<li >Sellers need the ability to create standard auctions (no support for reverse, dutch, or fixed price formats). This must include support for image uploads, payment methods, and categories.&nbsp;</li>
</ul>
<p>The next thing I did was to create a very basic entity model that defined the key objects in the system, the porperties to describe them, and the actions they can perform. The diagram below shows the corresponding data model (which has since been refactored a bit): &nbsp;</p>
<p><img src="{{ site.baseurl }}/assets/images/scn_auction_erd.gif" alt="erd" /></p>
<p><br style="margin: 0px; padding: 0px;" />I spent about 3 days of my train commute (2+ hours per day) coding the user functionality on the site. Below are screens of the browse/search and auction details pages:</p>

<p><img src="{{ site.baseurl }}/assets/images/scn_auction_listings.gif" alt="auction detail" /></p>
<p><img src="{{ site.baseurl }}/assets/images/headers/auction_mvp.gif" alt="auction detail" /></p>
<p>The proxy logic for bidding took the most time of any function. There are a few involved scenarios to accommodate, like when the bid is not high enough or when high bidder raises the maximum bid amount. &nbsp;It was certainly helpful to have built out robust unit tests (validating all the fringe scenarios) before writing any implementation code.</p>
<p>I was really happy to not have to worry about creating a look and feel or generic user management functionality. The look and feel can especially be a huge time suck.</p>
<p><strong style="margin: 0px; padding: 0px;"><span style="margin: 0px; padding: 0px; text-decoration: underline;">The Test</span></strong></p>
<p>I decided I would run a test of 50 auctions of vintage sports cards of star players (mostly Hall of Fame caliber). All auctions would run for 14 days and have a starting bid of $0.25 (the median retail value of the lots was around $8). I figured the best time to have them end was on a Sunday night between 8-10PM EST.</p>
<p>I spent another 2 more hours scanning the cards and entering the information for the auctions. To expedite the process, I wrote a small console application to trigger a scan and then divide a scanned image into individual images based on coordinates &ndash; much like a photoshop macro.</p>
<p>To let folks know about the new functionality, I posted a simple announcement the site&rsquo;s&nbsp;<a  href="http://www.sportscollectors.net/MessageBoardThread.aspx?t=157710" target="_blank">message board</a>&nbsp;and the site&rsquo;s&nbsp;<a  href="https://www.facebook.com/groups/8878802885/" target="_blank">Facebook page</a>:</p>

<p><img src="{{ site.baseurl }}/assets/images/scn_auction_announcement.gif" alt="auction detail" /></p>
<p>&nbsp;</p>
<p>Other than these general announcements, I provided no additional information or instructions to how everything would work. My hypothesis was that this member base was familiar enough using auctions to figure out how it would work. In the event there would be questions, they could reply to the message board post.&nbsp;</p>
<p><span style="text-decoration: underline;"><strong style="margin: 0px; padding: 0px;">The Results</strong></span></p>
<p>I&rsquo;d like to think the auction was pretty easy to use, as people used it to get some good deals. It had 43 different bidders made a total of 163 bids. &nbsp;47 of the 50 auctions resulted in sales (85%), for a total of $65.19 ($1.38 average).&nbsp;</p>
<p>On the final day, the auctions saw 24 bids, 10 of which were leading bidders raising their proxy bid amounts. I had figured there would be some items were people would bid in the last few minutes, but it did not happen. Of course I didn&rsquo;t take into account that the Oscars and a new Walking Dead episode may have distracted people during that window.</p>
<p>There was a theme on the message board (which saw 30 replies to the announcement) and via personal messages/emails, that members liked the auction feature. Many had expressed interest in using it to offer their items for sale.</p>
<p>I am interested to see if everyone pays for their winnings, and how timely the payments are. We should know over the next few weeks. I am also interested to see the preferred payment methods for buyers (Paypal, check, money order, or cash).</p>
<p><span style="text-decoration: underline;"><strong style="margin: 0px; padding: 0px;">Conclusions</strong></span></p>
<p>This was fun and I am happy I decided to 1) build this MVP during my commute and 2) run a set of auctions. I think the feature set that was part of this first test offered the right functionality.&nbsp;</p>
<p>I intentionally made starting bid very attractive to promote interest, as well as picking items (vintage star cards) that I knew would garner interest at these low price points. I likely spent more on the cards than what they sold for, but it was well worth it to get some tangible feedback on the idea.</p>
<p>I have not yet decided if an auction service is viable to run &ndash; either to be added permanently to the site (only open to members) or living as a different site.&nbsp;</p>
<p>I have also considered adding the following enhancements and offering the framework to others (maybe open source):</p>
<ul >
<li >Buy it now and fixed price listings</li>
<li >Allowing other sellers can list</li>
<li >Options to charge listing and/or final value fees</li>
<li >Watchlists for bidders</li>
<li >Notification when outbid</li>
<li >Notification when your auction is sold (for seller)</li>
</ul>