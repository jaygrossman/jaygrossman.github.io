---
layout: post
title:  "Predicting eBay Auction Sales with Machine Learning"
author: jay
tags: [ analysis, knn, cart, machine learning, prediction, ebay, auction, sportscollectors ]
image: assets/images/headers/carnac.png
description: "Predicting eBay Auction Sales with Machine Learning"
featured: true
hidden: false
comments: false
---

<p><span style="margin: 0px; padding: 0px; font-size: large;"><strong>Abstract:&nbsp;</strong></span></p>
<p>Online auctions are one the most popular methods to buy and sell items on the internet. &nbsp;With more than 100 million active users globally (as of Q4 2011),&nbsp;<a  href="http://www.ebayinc.com/who_we_are/one_company">eBay</a>&nbsp;is the world's largest online marketplace, where practically anyone can buy and sell practically anything. The total value of goods sold on eBay was $68.6 billion, more than $2,100 every second. This kind of volume produces huge amounts of data that can be utilized to provide services to the buyers and sellers, market research, and product development.&nbsp;</p>
<p>In this analysis, I collect historical auction data from eBay and use machine learning algorithms to predict sales results of auction items. I describe the features used and formulations used for making predictions. Using the sports autograph category on eBay, the algorithms used can be relatively accurate and can result in a useful set of services for buyers and sellers.</p>
<p><span style="margin: 0px; padding: 0px; font-size: large;"><strong>1)&nbsp;Introduction</strong>&nbsp;&nbsp;</span></p>
<p>I run a subscription based community with 30,000+ collectors of sports autographs and memorabilia &ndash;&nbsp;<a  href="http://www.sportscollectors.net/">http://www.sportscollectors.net</a>. &nbsp;</p>
<table>
<tbody>
<tr>
<td><img src="{{ site.baseurl }}/assets/images/scn.jpg" alt="" /></td>
</tr>
</tbody>
</table>
<p>Figure 1. Homepage of SportsCollectors.net</p>
<p>Since eBay is the world&rsquo;s largest marketplace for sports autographs, the vast majority of the site&rsquo;s membership uses it to buy and/or sell items via auction format. The ability to provide a method to estimate auction sale prices is desirable to this community.</p>
<p>Members of most communities related to collectibles have reported they most often try to predict how much an auction would sell for by performing a search for item and manually calculating the average sales price shown in the completed listings page (shown in Figure 2 below).</p>
<p><img src="{{ site.baseurl }}/assets/images/ebay_lisitngs.jpg" alt="" /></p>
<p>Figure 2. Example of eBay&rsquo;s completed listings page for autographed baseballs by Jim Rice. In this example, there have been 43 sales via auction format with an average sale price of $32.05 ending in the past 60 days.</p>
<p>To best serve this audience, I am interested in 2 things:</p>
<ol>
<li><span><strong>Determine whether an auction listing will result in a sale.</strong></span></li>
<li><span><strong>Predict final sale prices for auctions.</strong></span></li>
</ol>
<p>&nbsp;</p>
<p><span style="margin: 0px; padding: 0px; font-size: large;"><strong>2) Data Collection</strong>&nbsp;&nbsp;</span></p>
<p>I run an automated process that collects fixed price and auction listings information available on eBay. The process queries for listings at product sku level, defined by the combination of:</p>
<ul>
<li >Player&rsquo;s reference data from SportsCollectors.Net - every player to have played pro baseball, football, basketball, and hockey since 1948</li>
<li >eBay autograph category by sport (shown in Table 1):</li>
</ul>
<table style="margin: 0px; padding: 0px; color: #333333; font-size: 13px; letter-spacing: normal; line-height: 17.549999237060547px; width: 668px;" border="1" cellspacing="0" cellpadding="3">
<tbody>
<tr>
<td><strong>Sport</strong></td>
<td>Baseball</td>
<td>Basketball</td>
<td>Football</td>
<td>Hockey</td>
</tr>
<tr>
<td valign="top"><strong>Categories</strong></td>
<td valign="top">-Balls<br />-Bats<br />-Hats<br />-Helmets<br />-Index Cards<br />-Jerseys<br />-Lithographs, Posters, &amp; Prints<br />-Magazines<br />-Other Autographed Items<br />-Photos<br />-Plaques<br />-Plates<br />-Postcards<br />-Programs<br />-Ticket Stubs<br />-Trading Cards</td>
<td valign="top">-Balls<br />-Floor, Floorboard<br />-Index Cards<br />-Jerseys<br />-Lithographs, Posters, &amp; Prints<br />-Magazines<br />-Other Autographed Items<br />-Photos<br />-Trading Cards</td>
<td valign="top">-Balls<br />-Hats<br />-Helmets<br />-Index Cards<br />-Jerseys<br />-Lithographs, Posters, &amp; Prints<br />-Magazines<br />-Other Autographed Items<br />-Photos<br />-Plaques<br />-Programs<br />-Ticket Stubs<br />-Trading Cards</td>
<td valign="top">-Index Cards<br />-Jerseys<br />-Magazines<br />-Other Autographed Items<br />-Photos<br />-Pucks<br />-Sticks<br />-Trading Cards</td>
</tr>
</tbody>
</table>
<p>Table 1. Breakdown of categories of autographed products by sport</p>
<p><strong><span style="margin: 0px; padding: 0px; font-size: large;">3) Features</span></strong></p>
<p><span><strong>3.1 Auction Features</strong></span>&nbsp;&nbsp;</p>
<p>Table 2. Features extracted from the auction&rsquo;s meta data:&nbsp;</p>
<table border="1" cellspacing="0" cellpadding="3">
<tbody>
<tr>
<td><strong>Feature</strong></td>
<td><strong>Description</strong></td>
</tr>
<tr>
<td>Price</td>
<td>Final price the auction.<br /><br />If the listing does not result in a sale, the Price will be equal to the StartingBid.</td>
</tr>
<tr>
<td>StartingBid</td>
<td>Minimum bid for the auction</td>
</tr>
<tr>
<td>BidCount</td>
<td>Number of bids made for the auction</td>
</tr>
<tr>
<td>Title</td>
<td>Auction title</td>
</tr>
<tr>
<td>QuantitySold</td>
<td>The number of items sold in the listing. Represented by a 0 or 1.</td>
</tr>
<tr>
<td>SellerRating</td>
<td>Seller&rsquo;s eBay rating</td>
</tr>
<tr>
<td>SellerAboutMePage</td>
<td>Whether the seller has an eBay About Me page</td>
</tr>
<tr>
<td>StartDate</td>
<td>The beginning date and time of the auction</td>
</tr>
<tr>
<td>EndDate</td>
<td>The ending date and time of the auction</td>
</tr>
<tr>
<td>PositiveFeedbackPercent</td>
<td>The percent of positive feedback (of all the feedback) received by the seller</td>
</tr>
<tr>
<td>HasPicture</td>
<td>Indicates the seller included a picture with the listing<br /><br />Represented by a 0 or 1.</td>
</tr>
<tr>
<td>MemberSince</td>
<td>The date the seller created their online marketplace user account</td>
</tr>
<tr>
<td>HasStore</td>
<td>Indicates the seller has an eBay Store<br /><br />Represented by a 0 or 1.</td>
</tr>
<tr>
<td>SellerCountry</td>
<td>The country of the seller</td>
</tr>
<tr>
<td>BuyItNowPrice</td>
<td>The optional price to buy the item instantly</td>
</tr>
<tr>
<td>HighBidderFeedbackRating</td>
<td>Highest bidder&rsquo;s eBay rating</td>
</tr>
<tr>
<td>ReturnsAccepted</td>
<td>Whether the seller accepts returns.<br /><br />Represented by a 0 or 1.</td>
</tr>
<tr>
<td>HasFreeShipping</td>
<td>Whether the seller provides free shipping.<br /><br />Represented by a 0 or 1.</td>
</tr>
</tbody>
</table>
<p>&nbsp;</p>
<p><strong><span>3.2 Derived Features</span></strong>&nbsp;&nbsp;</p>
<p>Table 3. Features derived from the auction&rsquo;s meta data:</p>
<table border="1" cellspacing="0" cellpadding="3">
<tbody>
<tr>
<td><strong>Feature</strong></td>
<td><strong>Description</strong></td>
</tr>
<tr>
<td>IsHOF</td>
<td>Whether the player in their sport&rsquo;s Hall of Fame.&nbsp;<br /><br />Represented by a 0 or 1.</td>
</tr>
<tr>
<td>IsAuthenticated</td>
<td>Whether the received third party authentication.&nbsp;<br /><br />Represented by a 0 or 1.&nbsp;<br /><br />Determined by inspecting the auction&rsquo;s title and description details for a whitelisted set of keywords and ruling out a blacklisted set of keywords.</td>
</tr>
<tr>
<td>HasInscription</td>
<td>Whether the item has an inscription.&nbsp;<br /><br />Represented by a 0 or 1.&nbsp;<br /><br />Determined by inspecting the auction&rsquo;s title and description details for a whitelisted set of keywords and ruling out a blacklisted set of keywords.</td>
</tr>
<tr>
<td>AvgPrice</td>
<td>The average sale price by sku</td>
</tr>
<tr>
<td>MedianPrice</td>
<td>The median sale price by sku</td>
</tr>
<tr>
<td>AuctionCount</td>
<td>The number of auctions listed by sku</td>
</tr>
<tr>
<td>SellerSaleToAveragePriceRatio</td>
<td>The ratio of the sale price realized by a specific seller divided by the average price of the same skus</td>
</tr>
<tr>
<td>SellerAuctionSaleCount</td>
<td>The number of sales the seller has made</td>
</tr>
<tr>
<td>SellerItemSellPercent</td>
<td>The ratio of the number of sales divided by number of auctions listed by seller</td>
</tr>
<tr>
<td>StartDayOfWeek</td>
<td>The day of the week (number) that the auction Started</td>
</tr>
<tr>
<td>EndDayOfWeek</td>
<td>The day of the week (number) that the auction Ended</td>
</tr>
<tr>
<td>AuctionDuration</td>
<td>The number of days the auction lasted</td>
</tr>
<tr>
<td>StartingBidPercent</td>
<td>The ratio of the StartingBid divided by sku&rsquo;s AvgPrice</td>
</tr>
<tr>
<td>SellerClosePercent</td>
<td>The ratio of the number of auctions resulting in sale for a seller divided by total number of auctions the seller listed</td>
</tr>
<tr>
<td>ItemAuctionSellPercent</td>
<td>The ratio of the number of auctions resulting in sale for a sku divided by total number of auctions the listed for the sku</td>
</tr>
</tbody>
</table>
<p>&nbsp;</p>
<p><strong><span style="margin: 0px; padding: 0px; font-size: large;">4) Training and Test Data</span></strong></p>
<p>Data to determine whether an auction listing will result in a sale:</p>
<table border="1" cellspacing="0" cellpadding="3">
<tbody>
<tr>
<td>&nbsp;</td>
<td>Query Criteria</td>
<td>Records</td>
<td>Mean Sale Price</td>
<td>Median Sale</td>
<td>PriceRange Sale Price</td>
</tr>
<tr>
<td>Training Set</td>
<td>All Auctions ending in April 2013</td>
<td>258,588</td>
<td>$28.96</td>
<td>$9.99</td>
<td>$0.01-$300.00</td>
</tr>
<tr>
<td>Test Set</td>
<td>All Auctions ending in first week of May 2013</td>
<td>37,460</td>
<td>$24.65</td>
<td>$9.99</td>
<td>$0.01-$300.00</td>
</tr>
</tbody>
</table>
<p>&nbsp;</p>
<p>Data to predict final sale prices for auctions:&nbsp;</p>
<table border="1" cellspacing="0" cellpadding="3">
<tbody>
<tr>
<td>&nbsp;</td>
<td>Query Criteria</td>
<td>Records</td>
<td>Mean Sale Price</td>
<td>Median Sale</td>
<td>PriceRange Sale Price</td>
</tr>
<tr>
<td>Training Subset</td>
<td>Auctions ending with a sale in April 2013</td>
<td>79,732</td>
<td>$33.04</td>
<td>$14.99</td>
<td>$0.01-$300.00</td>
</tr>
<tr>
<td>Test Subset</td>
<td>Auctions ending with a sale in first week of May 2013</td>
<td>9,392</td>
<td>$29.17</td>
<td>$12.55</td>
<td>$0.01-$297.50</td>
</tr>
</tbody>
</table>
<p><span style="margin: 0px; padding: 0px; text-decoration: underline;">Filter Criteria</span></p>
<ul>
<li >Only Standard auction format.</li>
<li >Only items signed by a single player.</li>
</ul>
<p><span style="margin: 0px; padding: 0px; font-size: large;"><strong>5) Analysis and Prediction</strong></span></p>
<p>Since this analysis is trying to answer two questions, this section details the methodologies for solving each problem individually.</p>
<p><span><strong>5.1 Determine whether an auction listing will result in a sale.</strong></span></p>
<p>This is a binary classification problem, as the goal is to optimally predict QuantitySold (containing values of 0 or 1) as the target feature.&nbsp;</p>
<p>The model chosen to create the classification predictions is Logistic regression. Logistic regression uses a set of covariates to predict probabilities of class membership. I achieved an optimized prediction model based on a set of 5 derived features with standardized values.</p>
<table border="1" cellspacing="0" cellpadding="3">
<tbody>
<tr>
<td><strong>Prediction via Logistic Regression</strong></td>
<td><strong>Baseline: Prediction of sale when AvgPrice is less than SalePrice</strong></td>
</tr>
<tr>
<td>85.97%</td>
<td>42.74%</td>
</tr>
</tbody>
</table>
<p>&nbsp;</p>
<p><span><strong>5.2 Predict final sale prices for auctions.</strong></span></p>
<p>&nbsp;</p>
<table>
<tbody>
<tr>
<td><img src="{{ site.baseurl }}/assets/images/histogram_price.gif" alt="" /></td>
</tr>
</tbody>
</table>
<p>Figure 3. Histogram of Price feature in Training Subset.</p>
<p>Figure 3 shows a high concentration of sales under $20.00. Since sports autographs on eBay are not a commoditized item (as compared to consumer electronics or books), I have seen some pretty interesting ranges in sale price for the same items. This leads to one of the challenges for this analysis, that my Training Subset was not Gaussian. Figure 4 below represents the Price feature from training data after a log transform. We can see that the graph is skewed with a very high Min (first quartile). The test also set has a very similar distribution.</p>
<table>
<tbody>
<tr>
<td><img src="{{ site.baseurl }}/assets/images/histogram_log_price.gif" alt="" /></td>
</tr>
</tbody>
</table>
<p>Figure 4. Histogram of log(Price) feature in Training Subset after log transformation</p>
<p>There are 2 different approaches used to solving price prediction as a machine learning problem:</p>
<p><span><strong>5.2.1 Price Prediction by Regression</strong></span></p>
<p><span style="margin: 0px; padding: 0px; text-decoration: underline;">Classification and Regression Trees (CART)&nbsp;</span></p>
<p><a  href="http://www.stat.wisc.edu/~loh/treeprogs/guide/wires11.pdf" target="_blank">Classiﬁcation and regression trees</a>&nbsp;are machine-learning methods for constructing prediction models from data. The models are obtained by recursively partitioning the data space and ﬁtting a simple prediction model within each partition. As a result, the partitioning can be represented graphically as a decision tree. Classiﬁcation trees are designed for dependent variables that take a ﬁnite number of unordered values, with prediction error measured in terms of misclassiﬁcation cost. Regression trees are for dependent variables that take continuous or ordered discrete values, with prediction error typically measured by the squared difference between the observed and predicted values.</p>
<p>I created an optimized decision tree using the Training Subset, and then used it to create predictions against the Test Subset. The Root Mean Squared Error (RMSE) of the CART predictions is 4.30.</p>
<p>&nbsp;&nbsp;</p>
<table>
<tbody>
<tr>
<td><img src="{{ site.baseurl }}/assets/images/prediction_results.gif" alt="" /></td>
</tr>
</tbody>
</table>
<p>Figure 5. Plot of CART Predictions vs. Observed Sale Prices from the Test Subset. The green line represents Prediction=SalePrice and the blue line is the smoothed actual.</p>
<p>Figure 5 shows that the model does a pretty good job for the majority of the listings and then becomes significantly less accurate.&nbsp;</p>
<p>I created subsets of the Test Subset with different maximum predictions. I saw that $50 was the optimal balance in terms of RMSE and keeping a high portion of records of the original data set.&nbsp;</p>
<p><span style="margin: 0px; padding: 0px; text-decoration: underline;">Baseline</span></p>
<p>I created a analysis baseline using the CART model. I created a decision tree based the log(Price) and AvgPrice features in the Training Subset. The tree is used along with the Test Subset to create predictions. Using this method, the predictions had an RMSE of 5.30.</p>
<p><span><strong>5.2.2 Multi-Class Classification</strong></span></p>
<p>Using the Training Subset, I divided the Sale Price (target variable) into $5 intervals and created discrete categories. Each auction is assigned to one category. This allows for a multiclass classification problem in which case the output is a $5 range instead of the specific price.</p>
<p>Prediction using K-Nearest Neighbors (KNN)&nbsp;</p>
<p><a  href="https://en.wikipedia.org/wiki/K-nearest_neighbors_algorithm" target="_blank">Wikipedia defines KNN</a>&nbsp;as a non-parametric method for classifying objects based on closest training examples in the feature space.&nbsp;</p>
<p>I was able to use the KNN to create a model based on the Training Subset, using the Price Interval as the factor and k=3. This model was combined with the Test Subset to generate predictions for what $5 interval each item would fall into. Below are the results:</p>
<ul>
<li >accurately predicted: 50.92%&nbsp;</li>
<li >predictions within one group: 76.68%</li>
<li >predictions within two groups: 86.15%</li>
</ul>
<p><strong><span>5.2.3 Filtered Prediction</span></strong></p>
<p>I combined the two methods to get optimal results:</p>
<ul>
<li >Use predictions from CART with only out predictions under $50.00.</li>
<li >Use KNN classification predictions to limit outliers. Filter out auctions where predicted $5 interval is greater than 2 from the predicted price.</li>
</ul>
<table border="1" cellspacing="0" cellpadding="3">
<tbody>
<tr>
<td>Method</td>
<td>RMSE</td>
<td>Sale-Predition</td>
<td>Standard Deviation</td>
<td>% of Subset</td>
</tr>
<tr>
<td>Baseline CART (using AvgPrice)</td>
<td>5.30</td>
<td>+21%</td>
<td>40</td>
<td>100%</td>
</tr>
<tr>
<td>CART</td>
<td>4.30</td>
<td>-15%</td>
<td>28</td>
<td>100%</td>
</tr>
<tr>
<td>CART for predictions under $50.00</td>
<td>3.52</td>
<td>-14%</td>
<td>15</td>
<td>88%</td>
</tr>
<tr>
<td>CART for predictions under $50.00<br />and within 2 price intervals</td>
<td>0.84</td>
<td>-7%</td>
<td>12</td>
<td>65%</td>
</tr>
</tbody>
</table>
<p>&nbsp;</p>
<p><span style="margin: 0px; padding: 0px; font-size: large;"><strong>6) Conclusions</strong></span>&nbsp;</p>
<p><strong><span style="margin: 0px; padding: 0px; text-decoration: underline;">How reliable is using the average price for predicting sales?</span></strong></p>
<p>I would not be comfortable using the average price to predict auction sales. It has a higher RMSE, higher difference between actualized prices, and a very high standard deviation.</p>
<p><strong><span style="margin: 0px; padding: 0px; text-decoration: underline;">Can we determine whether an auction listing will result in a sale?</span></strong></p>
<p>Since logistic regression provided an almost 86% success rate for predicting if the auction would result in a sale, I would be comfortable considering the model&rsquo;s prediction.</p>
<p>This would be quite useful from a seller&rsquo;s perspective, to help minimize:</p>
<ul>
<li >The listing fees a seller would accumulate due to unsold listings.</li>
<li >The time invested in listing unsuccessful items.</li>
</ul>
<p><span style="margin: 0px; padding: 0px; text-decoration: underline;"><strong>Can we predict final sale prices for auctions?</strong></span></p>
<p>The combination of the CART prediction using KNN for eliminating obvious outliers does a good job predicting final sale price when predictions are under $50 (about 65% of the observed cases). &nbsp;</p>
<p>Since I have yet to find a service or commercial product related to predicting eBay auction results, this analysis could be valuable in offering services for the following applications:</p>
<ul>
<li >Buying recommendations/arbitrage</li>
<li >Listing Optimization</li>
<li >Product Sourcing and Logistics&nbsp;</li>
<li >Price Estimation for Complimentary Services (Shipping, Insurance providers)</li>
</ul>
<p>&nbsp;</p>
<p><strong><span style="margin: 0px; padding: 0px; font-size: large;">7) Future Exploration</span></strong></p>
<p>There are some additional features I can consider to potentially enhance these models:&nbsp;</p>
<ul>
<li >Time of year, month, major events (Super Bowl, Spring Training)</li>
<li >Bid Timing, clusters around bids near end of auctions.&nbsp;<br />Chou et al 2007 have done analysis on predicting price based on bidding patterns A Simulation-Based Model for Final Price Prediction in Online Auctions (http://www.jem.org.tw/content/pdf/Vol.3No.1/01.pdf)</li>
<li >Measure of Player&rsquo;s Popularity/Demand/Interest (on eBay, sportcollectors.net, twitter, espn, etc.)</li>
<li >Semantic parsing auction description for most relevant keywords or phrases</li>
</ul>
<p>Other areas to explore with this data:</p>
<ul>
<li >Sku representations for items signed by multiple players</li>
<li >Guidance around 3rd party authentication services</li>
<li >Product recommendations and predicting arbitrage scenarios.</li>
<li >More Verticals:<br />Other Collectibles (sports cards, coins, stamps, toys)<br />Car parts&nbsp;<br />Consumer Electronics</li>
</ul>
<div style="margin: 0px; padding: 0px; font-size: 13px; letter-spacing: normal; line-height: 17.549999237060547px;">
<div><strong><span style="margin: 0px; padding: 0px; text-decoration: underline;">R code used for this analysis:</span></strong></div>
<div>&nbsp;</div>
<p style="margin: 0px 0px 1.5em; padding: 0px;"><a  href="https://github.com/jaygrossman/eBaySalesPrediction" target="_blank">https://github.com/jaygrossman/eBaySalesPrediction</a></p>
<p style="margin: 0px 0px 1.5em; padding: 0px;">&nbsp;</p>