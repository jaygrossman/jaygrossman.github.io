---
layout: post
title:  "Measuring, Understanding, and Predicting Subscriber Value"
author: jay
tags: [ subscripiton, value, analysis, knn, cart, machine learning, prediction ]
image: assets/images/headers/subscribe.webp
description: "Measuring, Understanding, and Predicting Subscriber Value"
featured: false
hidden: false
comments: false
---





<p>The&nbsp;<span style="margin: 0px; padding: 0px; text-decoration: underline;">subscription business model</span>&nbsp;is a business model where a customer must pay a subscription price to have access to the product/service. Below are some examples of some popular commercial subscription offerings:</p>
<ul>
<li>Salesforce.com has built a popular brand offering CRM functions.</li>
<li>Amazon, Microsoft, etc. offer hosting infrastructure.&nbsp;</li>
<li>WeightWatchers and eDiets offer access to their diet information, tools, and community.</li>
<li>Getty offers download of stock photography.</li>
<li>Netflix and Hulu offer access to view streamed movies.</li>
<li>Odeo and Enigma.io offers access to reference data.&nbsp;</li>
</ul>
<p>It is becoming more common that people would rather subscribe to products and services than buy them outright. I am such a big fan of the model for web/software based applications that I implemented it successfully in 2002 on SportsCollectors.Net.</p>
<p>The obvious differentiator for a subscription business vs. selling a sku&rsquo;d product is the potential for recurring revenue. Most subscription businesses are inherently engineered making it easy for subscribers to continually want to pay for access to a valuable product. This eliminates the need for additional costs of acquisition or sales cycles.</p>
<p><strong style="margin: 0px; padding: 0px;">Measuring Subscription Models</strong></p>
<p>As with any business, I am usually interested in understanding its profitability and growth. In the case of subscription models, I look at the profits and growth at both a company/product level and at a user level. I monitor the following metrics to help evaluate performance:</p>
<ol>
<li>Recur Revenue (RR) for time period<br style="margin: 0px; padding: 0px;" />How much subscription based revenue does the business generate.<br style="margin: 0px; padding: 0px;" />&nbsp;</li>
<li>Retention<br style="margin: 0px; padding: 0px;" />Length of time that users are willing to pay for the subscription fees.<br style="margin: 0px; padding: 0px;" />&nbsp;</li>
<li>Total Lifetime Value (TLV)<br style="margin: 0px; padding: 0px;" />All Revenue opportunities and estimated value associated with the user&rsquo;s subscription. I break this down into 4 categories:<br style="margin: 0px; padding: 0px;" />&bull; &nbsp;Subscription Income<br style="margin: 0px; padding: 0px;" />&bull; &nbsp;Product/Affiliate Sales&nbsp;<br style="margin: 0px; padding: 0px;" />&bull; &nbsp;Residual Income (advertising, data/content licensing, etc.)<br style="margin: 0px; padding: 0px;" />&bull; &nbsp;Brand Enhancement (subscribers can become product evangelists)<br style="margin: 0px; padding: 0px;" />&nbsp;</li>
<li>Customer Acquisition Cost (CAC)<br style="margin: 0px; padding: 0px;" />Costs (marketing, sales, etc.) associated with onboarding the user.<br style="margin: 0px; padding: 0px;" />&nbsp;</li>
<li>Operational Costs&nbsp;<br style="margin: 0px; padding: 0px;" />Non-growth spend, the costs of operating the business. This includes hosting infrastructure, developers/designers, license fees, salaries, cost of goods sold, general/administrative, R&amp;D, etc.</li>
</ol>
<p><span style="margin: 0px; padding: 0px; text-decoration: underline;">Measuring a Subscription Company/Product</span></p>
<p>As an example to illustrate, let&rsquo;s say our company offers a subscription product for $10/month. We currently have 500 active subscribers. Each month we average 55 new subscribers and lose 40, with subscription length averaging 5 months long. We calculate an average of $5 of lifetime value for each user related to non-subscription income. The CAC is $8 per user and our operations costs $1000 monthly.</p>
<p>Profitability is the most obvious thing we can look at. Most people would agree with the definition of profit = revenues &ndash; cost.&nbsp;</p>
<p>Month&rsquo;s subscription income = (500 subscribers * $10 subscription price) = $5000<br style="margin: 0px; padding: 0px;" />Month&rsquo;s non -subscription income = ($5 non-subscription income / 5 months average length of subscription * 500 subscribers) = $500<br style="margin: 0px; padding: 0px;" />Month&rsquo;s acquisition costs = ($8 CAC * 55 new subscribers) = $440<br style="margin: 0px; padding: 0px;" />Operations cost = $1000</p>
<p>Monthly Profit Margin = Month&rsquo;s subscription + Month&rsquo;s non -subscription income - Month&rsquo;s acquisition costs - Operations cost<br style="margin: 0px; padding: 0px;" /><span style="margin: 0px; padding: 0px; color: #ff0000;"><em style="margin: 0px; padding: 0px;">Monthly Profit Margin = $5000 + $500 &ndash;$440 - $1000 = $4060</em></span></p>
<p>Then I like to look at growth measures. How many people are we bringing and how many are staying can tell us a lot.&nbsp;</p>
<p>New Subscriber Ratio = New Subscribers / All Subscribers<br style="margin: 0px; padding: 0px;" /><span style="margin: 0px; padding: 0px; color: #ff0000;"><em style="margin: 0px; padding: 0px;">New Subscriber Ratio = 55 / 500 = 11%&nbsp;</em></span></p>
<p>Monthly Recur Revenue Growth = (Current Month&rsquo;s Recur Revenue &ndash; Previous Month&rsquo;s Recur Revenue) / Previous Month&rsquo;s Recur Revenue<br style="margin: 0px; padding: 0px;" /><span style="margin: 0px; padding: 0px; color: #ff0000;"><em style="margin: 0px; padding: 0px;">Monthly Recur Revenue Growth = ($5000 &ndash; $4850) / $4850 = 0.31%</em></span></p>
<p>So adding more active subscribers than you lose is almost always a good thing, but I am interested in whether it is cost effective. Growth Efficiency measures how much it costs you to acquire $1 of Acquired Customer Value:</p>
<p>Growth Efficiency = TLV for average user / (CAC for average user&ndash; Operational Costs for average user)<br style="margin: 0px; padding: 0px;" />Growth Efficiency = ((5 month subscription * $10 subscription fee) + $5 non subscription income) / ($8 CAC + ($1000 operations cost/500 subscribers * 5 month subscription))<br style="margin: 0px; padding: 0px;" /><span style="margin: 0px; padding: 0px; color: #ff0000;"><em style="margin: 0px; padding: 0px;">Growth Efficiency = $55 / ($8 + $10) = 3.06</em></span></p>
<p>In businesses that scale well, we&rsquo;ll see a linear representation showing as the Operations Costs per user decline as they get spread out as more subscribers are added. Hence growth in subscribers will result in greater profitability and growth efficiency.&nbsp;</p>
<p><span style="margin: 0px; padding: 0px; text-decoration: underline;">Measuring a Subscriber</span></p>
<p>If we assume SubscriberX is fairly typical example of the population, and subscribes to the service for 6 months and we invested $7 in CAC to bring him onboard.</p>
<p>The revenues for the SubscriberX are represented by the TLV : (6 month subscription * $10 subscription fee) + $5 non subscription income = $65</p>
<p>The estimated operations cost per user is calculated by taking the overall operations cost, dividing by the number of subscribers, and multiplying it by the number of months the user subscribes to the service. Combining operations and customer acquisition cost give us: &nbsp;$7 CAC + ($1000 operations cost/500 subscribers * 6 month subscription) = $19.&nbsp;</p>
<p><span style="margin: 0px; padding: 0px; color: #ff0000;">SubscriberX represents $46 ($65 TLV - $19 cost) of lifetime profitability.</span></p>
<p>Growth Efficiency = TLV for SubscriberX / (CAC for SubscriberX &ndash; Operational Costs for SubscriberX)<br style="margin: 0px; padding: 0px;" />Growth Efficiency = ((6 month subscription * $10 subscription fee) + $5 non subscription income) / ($7 CAC + ($1000 operations cost/500 subscribers * 6 month subscription))<br style="margin: 0px; padding: 0px;" /><span style="margin: 0px; padding: 0px; color: #ff0000;"><em style="margin: 0px; padding: 0px;">Growth Efficiency $65 / ($7 + $12) = 3.42</em></span></p>
<p><strong style="margin: 0px; padding: 0px;">Understanding User Value</strong></p>
<p>Just because we can measure the LTV of a subscriber, does not necessarily mean we understand it. It&rsquo;s important to understand why some subscribers have higher LTV than others. This will allow us to identify actions we can take to attempt to increase an individual subscriber&rsquo;s LTV.</p>
<p>Although each subscription business is unique and may have different variables describing users&rsquo; options and actions, we can take machine learning approaches to gain insight. We should hopefully be able to define the key variables that go into solving for LTV as the target feature.</p>
<p>I usually start by getting a high level understanding of the distribution of the population&rsquo;s LTV. By getting the summary and generating histograms for LTV and log(LTV), I&rsquo;ll get the structure of distribution (if it is normalized) and size of the deviation. I then do the same for the components that comprise LTV (Subscription Income, Product/Affiliate Sales, Residual Income, Brand Enhancement).</p>
<p>Then I can use the identified variables and create an optimized decision tree solving for LTV. The partitions of the data identify the most important features and the decision points.</p>
<p>I'm not just interested in solving for LTV, but I want a way to estimate the relationships among variables. So I'll use Regression analysis with backwards elimination to find the set of variables that best explain the variance (highest r-squared) and lowest error. &nbsp;</p>
<p>For example, on one site I noticed that members that log into the site 5+ days a week and average 2+ logins per day are most likely to highest LTV. The guidance provided was to take steps to improve user engagement and incent users to return regularly. Enhanced alerting features were added, leading to increases in subscriber visitation and LTV.</p>
<p><strong style="margin: 0px; padding: 0px;">Predicting User Value</strong></p>
<p>Making predictions is another common machine learning task. Since the structure of our data is defined (we know the target feature and the key variables), there are quite a few supervised learning options that can be used to make predictions (linear regression, CART, support vector machine, K-Nearest Neighbors, etc.).</p>
<p><span style="margin: 0px; padding: 0px; text-decoration: underline;">Predicting LTV</span></p>
<p>I have found it to be valuable to predict the LTV at different time intervals. For instance, I want to predict the LTV of a subscriber based on the first month&rsquo;s activity. Then the second month, third month, sixth month, etc.</p>
<p>It can open up interesting possibilities such as:</p>
<ul>
<li>Better understanding behavior as it relates to changes in LTV</li>
<li>Categorizing members to make recommendations/introductions</li>
<li>Identify candidates where early intervention methods may increase LTV or retention</li>
</ul>
<p>I set up templates for a number of predictive algorithms and feed each the subscriber data set. To avoid overfitting, cross validation support is implemented as part of the algorithms. I will most often use the one returning the lowest RSME or a blend of the most accurate ones.</p>
<p><span style="margin: 0px; padding: 0px; text-decoration: underline;">Predicting Retention</span></p>
<p>Subscription sites generally want the highest retention possible, as it translates to greater LTV and profits. Another metric I look to gather is the probability that the member will continue to pay for the service during the upcoming billing period.&nbsp;</p>
<p>This becomes a binary classification problem, predicting whether a subscriber will be retained for the following month. The model considers variables related to each subscriber&rsquo;s lifetime activities and current month&rsquo;s activities. My modeling uses a combination of logistic regression, k-Nearest Neighbors, and CART, also looking for the optimal RMSE.</p>
<p><strong style="margin: 0px; padding: 0px;">Conclusions</strong></p>
<p>The framework described in this post has allowed me to gain better understanding of my subscription business and guided my activities in the following areas:</p>
<ul>
<li>Product Development</li>
<li>Marketing</li>
<li>Customer Service</li>
</ul>
<p>Creating a representation for the Brand Enhancement component of LTV is usually not a straight forward exercise. I have attributed value for:</p>
<ul>
<li>subscribers (or that have web sites) that refer traffic</li>
<li>moderators and admins</li>
<li>subscribers that host activities/events</li>
