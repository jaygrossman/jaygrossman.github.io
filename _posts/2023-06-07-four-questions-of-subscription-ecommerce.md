---
layout: post
title:  "The 4 Analytics Questions of Subscription Ecommerce"
author: jay
categories: [ ]
tags: [  favorites, analysis, data engineering, subscription, marketing, attribution ] 
image: assets/images/headers/4_questions.gif
description: "The 4 Analytics Questions of Subscription Ecommerce"
featured: true
hidden: false
comments: false
#rating: 4.5
---

<p>I have spent over 20 years building my own subscription based service (<a href="http://www.SportsCollectors.Net" target="_blank">SportsCollectors.Net</a>) and working for companies with subscription offerings (<a href="http://www.dell.com" target="_blank">Dell</a>, <a href="https://www.gettyimages.com/search/photographer?family=creative&amp;photographer=jupiterimages&amp;sort=best" target="_blank">Jupiterimages</a>, <a href="http://www.ww.com" target="_blank">Weight Watchers</a>, <a href="http://www.renttherunway.com" target="_blank">Rent the Runway</a>,&nbsp;<a href="https://www.elysiumhealth.com/" target="_blank">ElysiumHelath</a>). While the business models, value propositions and customer segments of these companies may be very different, there are similarities I recognized as these companies (all with product market fit) looked to accelerate their growth. From this, the 4 questions above are what analytics teams should spend resources trying to answer.</p>


<style>
table.table_11 td  {
    font-size: 11pt;
    vertical-align: top;
    padding: 2px;
}
</style>

<p><strong>Please Note:</strong><br>The order of these questions is in the context that you have an existing subscription based offering (with existing data available to analyze) you want to optimize+grow. If you are starting a new subscription offering/company, the order would likely be the opposite.</p>


<p>So let's dig into each one of these topics:</p>
<h3>1) What do you know about your valuable customers?</h3>
<p>You are likely in business to serve your customers' needs and/or deliver some value that they are comfortable (or even happy) to pay you for. It helps to understand who are those customers that are most happy and making you money. These folks tend to be your biggest ambassadors (promoters) and support your growth the most.&nbsp;</p>

<h4>Margin as a measure of value</h4>
<p>I've seen different definitions of "Lifetime Value", many revolving around top line revenue. I personally like to start with looking at the amount and the details that make up the total margin a specific user represents to the business. So I start by building a ledger for each user showing the credits (increases to equity account) and debits (decreases to equity account).<br /><br />Let's look at a very simple ledger for a subscription for a content site:</p>


<table class="table_11">
<tbody>
<tr>
<td><strong>Date</strong></td>
<td colspan="2"><strong>Debit (money out)</strong></td>
<td>&nbsp;&nbsp;</td>
<td colspan="2"><strong>Credit (money in)</strong></td>
<td>&nbsp;</td>
<td><strong>Margin</strong></td>
</tr>
<tr>
<td>12/03/2018</td>
<td>Google AdWords CAC (campaign 892)</td>
<td>$2.00</td>
<td>&nbsp;&nbsp;</td>
<td>&nbsp;</td>
<td>&nbsp;</td>
<td>&nbsp;</td>
<td>-$2.00</td>
</tr>
<tr>
<td>01/01/2019&nbsp;</td>
<td>&nbsp;</td>
<td>&nbsp;</td>
<td>&nbsp;&nbsp;</td>
<td>Subscription (order 1987)</td>
<td>$9.99</td>
<td>&nbsp;</td>
<td>&nbsp;$7.99</td>
</tr>
<tr>
<td>01/01/2019&nbsp;</td>
<td>Subscription promo (order 1987)</td>
<td>$9.00</td>
<td>&nbsp;&nbsp;</td>
<td>&nbsp;</td>
<td>&nbsp;</td>
<td>&nbsp;</td>
<td>-$1.01</td>
</tr>
<tr>
<td>01/03/2019&nbsp;</td>
<td>Monthly operations cost</td>
<td>$0.14</td>
<td>&nbsp;&nbsp;</td>
<td>&nbsp;</td>
<td>&nbsp;</td>
<td>&nbsp;</td>
<td>-$1.15</td>
</tr>
<tr>
<td>01/03/2019&nbsp;</td>
<td>&nbsp;</td>
<td>&nbsp;</td>
<td>&nbsp;&nbsp;</td>
<td>Monthly affiliate revenue</td>
<td>$0.37</td>
<td>&nbsp;</td>
<td>-$0.78</td>
</tr>
<tr>
<td>02/01/2019&nbsp;</td>
<td>&nbsp;</td>
<td>&nbsp;</td>
<td>&nbsp;&nbsp;</td>
<td>Subscription Renewal - Term 2</td>
<td>$9.99</td>
<td>&nbsp;</td>
<td>&nbsp;$9.21</td>
</tr>
<tr>
<td>02/03/2019&nbsp;</td>
<td>Monthly operations cost</td>
<td>$0.14</td>
<td>&nbsp;&nbsp;</td>
<td>&nbsp;</td>
<td>&nbsp;</td>
<td>&nbsp;</td>
<td>&nbsp;$9.07</td>
</tr>
<tr>
<td>02/03/2019&nbsp;</td>
<td>&nbsp;</td>
<td>&nbsp;</td>
<td>&nbsp;&nbsp;</td>
<td>Monthly affiliate revenue</td>
<td>$1.28</td>
<td>&nbsp;</td>
<td>&nbsp;$10.35</td>
</tr>
<tr>
<td>03/01/2019&nbsp;</td>
<td>&nbsp;</td>
<td>&nbsp;</td>
<td>&nbsp;&nbsp;</td>
<td>Subscription Renewal - Term 3</td>
<td>$9.99</td>
<td>&nbsp;</td>
<td>&nbsp;$20.34</td>
</tr>
<tr>
<td>03/02/2019&nbsp;</td>
<td>&nbsp;</td>
<td>&nbsp;</td>
<td>&nbsp;&nbsp;</td>
<td>Pay Per View Event (order 2508)</td>
<td>$3.99</td>
<td>&nbsp;</td>
<td>&nbsp;$24.33</td>
</tr>
<tr>
<td>03/02/2019&nbsp;</td>
<td>Royalty for Pay Per View Event</td>
<td>$1.00</td>
<td>&nbsp;&nbsp;</td>
<td>&nbsp;</td>
<td>&nbsp;</td>
<td>&nbsp;</td>
<td>&nbsp;$23.33</td>
</tr>
<tr>
<td>03/03/2019&nbsp;</td>
<td>Monthly operations cost</td>
<td>$0.15</td>
<td>&nbsp;&nbsp;</td>
<td>&nbsp;</td>
<td>&nbsp;</td>
<td>&nbsp;</td>
<td>&nbsp;$23.18</td>
</tr>
<tr>
<td>03/03/2019&nbsp;</td>
<td>&nbsp;</td>
<td>&nbsp;</td>
<td>&nbsp;&nbsp;</td>
<td>Monthly affiliate revenue</td>
<td>$2.31</td>
<td>&nbsp;</td>
<td>&nbsp;$25.49</td>
</tr>
<tr>
<td>03/15/2019&nbsp;</td>
<td>Mid-term cancel - partial refund</td>
<td>$5.00</td>
<td>&nbsp;&nbsp;</td>
<td>&nbsp;</td>
<td>&nbsp;</td>
<td>&nbsp;</td>
<td>&nbsp;$20.49</td>
</tr>
<tr>
<td><strong>Totals</strong></td>
<td>&nbsp;</td>
<td><strong>$17.43</strong></td>
<td>&nbsp;&nbsp;</td>
<td>&nbsp;</td>
<td><strong>$37.92</strong></td>
<td>&nbsp;</td>
<td>&nbsp;<strong style="color: #ff0000;">$20.49</strong></td>
</tr>
</tbody>
</table>
<p><br />There's some interesting questions looking at this kind of data can unearth:</p>
<ul>
<li>How does this user's journal entries compare to others?&nbsp;</li>
<li>Was their desirable ROI on the $2.00 AdWords attribution?</li>
<li>Was offering a $9.00 signup promo worthwhile (we won't see margin on user until second month)?&nbsp;</li>
<li>Since this user's affiliate revenue is increasing and they bought a Pay Per View Event, should we incentivize renewal?</li>
</ul>
<h4>What do your users tell you?</h4>

<div>Many companies may ask their users to provide details about themselves with the hope of using this info to provide a better user experience. Here are some examples of the types of industry specific profile information:</div>
<div>&nbsp;</div>
<div>
<ul>
<li>WeightWatchers has a goal of helping members get healthier - so it may ask about current weight, weight loss goals, lifestyle or dietary preferences, age, location.</li>
<li>Rent the Runway has a goal of helping members enjoy fashion options - so it may ask about body type, height, birthdate, location, style preferences.</li>
<li>EHarmony has the goal of matching users for relationships - so it has users spend 45 minutes to fill a&nbsp;<a href="https://www.eharmony.com/tour/what-is-the-compatibility-quiz/" target="_blank">lengthy questionnaire</a>&nbsp;about their lifestyles and personal preferences.&nbsp;&nbsp;</li>
<li>SportsCollectors.net has the goal of helping members enjoy collecting sports autographs - so it may ask about want lists, favorite players/teams, what items they have for trade/sale, their ebay username, year of birth, location.&nbsp;&nbsp;</li>
</ul>
</div>
<h4 id="user_journey">Do your users' actions tell you things?</h4>
<p>In addition to the margin profile, I have also invested time in building a user journey (now being marketed as the&nbsp;<a href="https://www.activityschema.com/" target="_blank">Activity Schema</a>) -&nbsp; which is a time series of all the actions and communications with this subscriber. A user journey can be represented with the following data elements:</p>
<table class="table_11">
<tbody>
<tr>
<td><strong>UserID</strong>&nbsp;</td>
<td><strong>Timestamp</strong>&nbsp;</td>
<td><strong>User_Event</strong>&nbsp;</td>
<td><strong>Payload</strong>&nbsp;</td>
</tr>
<tr>
<td valign="top">12345&nbsp;</td>
<td valign="top">01/01/2019 00:00:00&nbsp;</td>
<td valign="top">PAGE_LOAD&nbsp;</td>
<td>{"device_type": "desktop", "marketing_channel": "google_sem", "url": "http://www.hello.com/about-us/?utm=sem_plan1"}</td>
</tr>
<tr>
<td valign="top">12345&nbsp;</td>
<td valign="top">01/01/2019 00:01:00&nbsp;</td>
<td valign="top">PAGE_LOAD&nbsp;</td>
<td>{"device_type": "desktop", "url": "http://www.hello.com/plans"}</td>
</tr>
<tr>
<td valign="top">12345&nbsp;</td>
<td valign="top">01/01/2019 00:02:00&nbsp;</td>
<td valign="top">PAGE_LOAD&nbsp;</td>
<td>{"device_type": "desktop", "url": "http://www.hello.com/register"}</td>
</tr>
<tr>
<td valign="top">12345&nbsp;</td>
<td valign="top">01/01/2019 00:03:00&nbsp;</td>
<td valign="top">PAGE_LOAD&nbsp;</td>
<td>{"device_type": "desktop", "url": "http://www.hello.com/login"}</td>
</tr>
<tr>
<td valign="top">12345&nbsp;</td>
<td valign="top">01/01/2019 00:04:00&nbsp;</td>
<td valign="top">PAGE_LOAD&nbsp;</td>
<td>{"device_type": "desktop", "url": "http://www.hello.com/subscribe"}</td>
</tr>
<tr>
<td valign="top">12345&nbsp;</td>
<td valign="top">01/01/2019 00:04:00&nbsp;</td>
<td valign="top">PLACE_ORDER&nbsp;</td>
<td>{"order_id": 1987, "order_status": "payment_received", "order_type": "SUBSCRIPTION", "amount": "0.99"}</td>
</tr>
<tr>
<td valign="top">12345&nbsp;</td>
<td valign="top">01/01/2019 00:07:00&nbsp;</td>
<td valign="top">RECEIVE_EMAIL&nbsp;</td>
<td>{"template": "201900101_new_years_cohort", "list_name": "hello subscribers"}</td>
</tr>
<tr>
<td valign="top">12345&nbsp;</td>
<td valign="top">01/01/2019 00:015:00&nbsp;</td>
<td valign="top">OPEN_EMAIL&nbsp;</td>
<td>{"template": "201900101_new_years_cohort", "list_name": "hello subscribers"}</td>
</tr>
<tr>
<td valign="top">12345&nbsp;</td>
<td valign="top">01/01/2019 00:16:00&nbsp;</td>
<td valign="top">PAGE_LOAD&nbsp;</td>
<td>{"device_type": "mobile", "url": "http://www.hello.com/article/hot-investments"}</td>
</tr>
<tr>
<td valign="top">12345&nbsp;</td>
<td valign="top">01/01/2019 00:21:00&nbsp;</td>
<td valign="top">PAGE_LOAD&nbsp;</td>
<td>{"device_type": "mobile", "url": "http://www.hello.com/article/trending-for-2019"}</td>
</tr>
</tbody>
</table>
<p><br />This structure allows us to define multiple types of events across our system and the relevant details about each as&nbsp;<a href="https://en.wikipedia.org/wiki/JSON" target="_blank">JSON</a>&nbsp;in the "Payload" field.</p>
<p>In order to achieve this, we need to have the ability to track what different users are doing in our system. We have a service that we call to log our users' actions to get the PAGE_LOAD events (there are services like&nbsp;<a href="https://snowplowanalytics.com/" target="_blank">snowplow</a>,&nbsp;<a href="https://segment.com/" target="_blank">segment</a>&nbsp;or&nbsp;<a href="https://analytics.google.com/analytics/web/">google analytics</a>&nbsp;for this), we get the PLACE_ORDER events from the order table in our database, and we get EMAIL related information from our email vendor (<a href="https://mailchimp.com/" target="_blank">mailchimp</a>,&nbsp;<a href="https://www.sailthru.com" target="_blank">sailthru</a>,&nbsp;<a href="https://www.cheetahdigital.com" target="_blank">cheetahmail</a>&nbsp;do this kind of thing).</p>
<p>This very simple example shows that User 12345 has 10 events associated with them:</p>
<ul>
<li>He arrives to the site from a google paid search. We can use this to attribute our marketing acquisition cost.</li>
<li>He views our plans page, then registers for an account, then logs in, and then places an order all on a desktop browser. Seeing the steps taken before an order is placed allows us understand our conversion funnel.</li>
<li>Our system sends email (through an email vendor) and can track if it is opened or clicked on.</li>
<li>He reads 2 article pages on their mobile device.</li>
</ul>
<div>A real user journey may have many thousands of actions spanning across many visits/sessions from different devices/channels. This can allow us to see how often they interact with our system and how their behavior changes over time.<br /><br /></div>
<h3>Margin + Profile Info + Actions == Potential for Analysis</h3>
<p>When we combine the margin of a user, their profile information, and the actions in their user journey, we can:</p>
<ul>
<li>Find who are the high margin users and what makes them so. Develop understanding what actions and features drive higher margins.&nbsp;</li>
<li>Find out what our members like and want. Develop understanding of their interests across segments of our membership. Understand how should we communicate with them - (effectiveness of email, chat, customer service, retail channels).</li>
<li>Develop features/content (and possibly create experiments) to further satisfy the user. This can help support goals for longer retention or converting on up-sell opportunities.</li>
<li>Find out how to describe and categorize our users. Does their profile and actions allow us to classify them into naturally forming groups?</li>
<li>Try to make our offerings more attractive to future users. What are common paths for conversion and what types of folks convert quickly, slowly, not at all. Where do users get stuck and what actions have worked to de-risk these scenarios.</li>
<li>Find out how our users at different margin levels find us. This can help optimize our marketing/awareness efforts.</li>
<li>Understand what happens when we make changes to the offering - such as changing features, content, messaging, pricing, support options, etc.?&nbsp;</li>
</ul>
<h3>2) How can you keep customers coming back?</h3>
<div>&nbsp;</div>
<div>
<div>For subscription based business models, how much users continue to pay for the subscription (also known as retention) is a key concern because:</div>
<div>&nbsp;</div>
<div>
<ul>
<li>Assuming that your offering can be profitable (subscription fees are higher than your costs), your revenues and margins scale linearly to the number of subscription terms that users pay for. So figuring out how to maximize retention will increase your earnings.&nbsp;</li>
<li>It is almost always cheaper to retain current customers than to acquire new ones.&nbsp;<br />

<p><img src="{{ site.baseurl }}/assets/images/4_questions_1.png" alt="" /></p>

</li>
</ul>
</div>
<h4>Measuring retention?</h4>
<div>Retention rate (as defined by&nbsp;<a href="https://en.wikipedia.org/wiki/Retention_rate" target="_blank">Wikipedia</a>)&nbsp;is "the ratio of the number of retained customers to the number at risk". As an example: if you have 1,000 subscribers in term 1 and 950 of those same users are still active in term 2, then your retention rate for that period is 950/1000=0.95 or 95%.</div>
<div>&nbsp;</div>
<div>Churn rate (another popular metric) is the inverse of retention, is "the percentage of service subscribers who discontinue their subscriptions within a given time period". Back to our example: if you have 1,000 subscribers in term 1 and 950 of those same users are still active in term 2, then your churn rate for that period is (1000-950)/1000=0.05 or 5%.</div>
<div>&nbsp;</div>
<div>Retention rate for a subscription offering is a proxy metric many folks use to understand its business health and investors use it to compare companies.<br /><br /></div>
</div>
<h4>Visualizing retention rates by cohorts</h4>
<p>The most common way people seems to look at retention rate is to segment into related groups - known as cohorts. Each person in a cohort must share a related yet distinguishable trait that separates them from the other cohorts. In these examples, our cohorts will be based on the month that users starts their subscriptions.</p>
<p>Aaron Chantiles has done a nice job creating these&nbsp;&nbsp;<a href="https://blog.usejournal.com/how-to-perform-cohort-analysis-calculate-customer-ltv-in-excel-80bfed785ec4" target="_blank">3 cohort model reports</a>:</p>
<p>1) Survival Analysis - for each month term we can see the percentage of the original users in that cohort that are still active.</p>

<p><img src="{{ site.baseurl }}/assets/images/4_questions_2.png" alt="" /></p>

<p><br />2) Average Revenue per User - for each month term we can see the average revenue of the active users in that cohort. (this could be margin instead)<br /><em>Note: Big variances from month to month likely results from introducing major changes to your subscription plan, adding some great new features, breaking something, or big impact from seasonality.</em></p>


<p><img src="{{ site.baseurl }}/assets/images/4_questions_3.png" alt="" /></p>

<p><br />3) Total Revenue by Cohort - for each month term we can see the total cumulative revenue of the active users in that cohort. (this could be margin instead)</p>

<p><img src="{{ site.baseurl }}/assets/images/4_questions_4.png" alt="" /></p>

<h4>Understanding and Predicting user retention</h4>
<p>KEY QUESTION - If we can somehow experience or 'learn' how users have behaved in the past (those who churned and those who stayed), then can we can predict how your current users will behave in the future?</p>
<p>We can try to leverage our user profile information, ledger, and user journey data sets to discover things about our users and how they behaved. We'll start by defining some variables (features) that we think may contribute to retention for a subscription content site:</p>
<table border="1" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td style="padding: 3px;" valign="top"><strong>metric</strong></td>
<td style="padding: 3px;" valign="top"><strong>type</strong></td>
<td style="padding: 3px;" valign="top"><strong>source</strong></td>
<td style="padding: 3px;" valign="top"><strong>description</strong></td>
</tr>
<tr>
<td style="padding: 3px;" valign="top">median_income</td>
<td style="padding: 3px;" valign="top">int</td>
<td style="padding: 3px;" valign="top">profile</td>
<td style="padding: 3px;" valign="top">we can join user's location on census data</td>
</tr>
<tr>
<td style="padding: 3px;" valign="top">population_density</td>
<td style="padding: 3px;" valign="top">int</td>
<td style="padding: 3px;" valign="top">profile</td>
<td style="padding: 3px;" valign="top">we can join location on census data</td>
</tr>
<tr>
<td style="padding: 3px;" valign="top">user_age</td>
<td style="padding: 3px;" valign="top">int</td>
<td style="padding: 3px;" valign="top">profile</td>
<td style="padding: 3px;" valign="top">age in full years</td>
</tr>
<tr>
<td style="padding: 3px;" valign="top">first_month_price</td>
<td style="padding: 3px;" valign="top">int</td>
<td style="padding: 3px;" valign="top">ledger</td>
<td style="padding: 3px;" valign="top">amount of first month cost (to determine impact of promos)</td>
</tr>
<tr>
<td style="padding: 3px;" valign="top">customer_acquisition_cost</td>
<td style="padding: 3px;" valign="top">int</td>
<td style="padding: 3px;" valign="top">ledger</td>
<td style="padding: 3px;" valign="top">amount of acquisition cost (to determine impact of paid marketing sources vs. organic)</td>
</tr>
<tr>
<td style="padding: 3px;" valign="top">total_margin</td>
<td style="padding: 3px;" valign="top">int</td>
<td style="padding: 3px;" valign="top">ledger</td>
<td style="padding: 3px;" valign="top">total margin the user has contributed to the company</td>
</tr>
<tr>
<td style="padding: 3px;" valign="top">searches_count</td>
<td style="padding: 3px;" valign="top">int</td>
<td style="padding: 3px;" valign="top">journey</td>
<td style="padding: 3px;" valign="top">the number of searches they performed</td>
</tr>
<tr>
<td style="padding: 3px;" valign="top">visit_count</td>
<td style="padding: 3px;" valign="top">int</td>
<td style="padding: 3px;" valign="top">journey</td>
<td style="padding: 3px;" valign="top">the number of times they visit</td>
</tr>
<tr>
<td style="padding: 3px;" valign="top">articles_per_visit</td>
<td style="padding: 3px;" valign="top">decimal</td>
<td style="padding: 3px;" valign="top">journey</td>
<td style="padding: 3px;" valign="top">the number of articles they view per visit</td>
</tr>
<tr>
<td style="padding: 3px;" valign="top">views_per_article</td>
<td style="padding: 3px;" valign="top">decimal</td>
<td style="padding: 3px;" valign="top">journey</td>
<td style="padding: 3px;" valign="top">average number of times they visit the same article</td>
</tr>
<tr>
<td style="padding: 3px;" valign="top">favorite_count</td>
<td style="padding: 3px;" valign="top">int</td>
<td style="padding: 3px;" valign="top">journey</td>
<td style="padding: 3px;" valign="top">number of times they favorite content</td>
</tr>
<tr>
<td style="padding: 3px;" valign="top">recommendation_count</td>
<td style="padding: 3px;" valign="top">int</td>
<td style="padding: 3px;" valign="top">journey</td>
<td style="padding: 3px;" valign="top">number of times they recommend/share content</td>
</tr>
<tr>
<td style="padding: 3px;" valign="top">non_subscription_revenue</td>
<td style="padding: 3px;" valign="top">decimal</td>
<td style="padding: 3px;" valign="top">ledger</td>
<td style="padding: 3px;" valign="top">amount of non-subscription revenue</td>
</tr>
<tr>
<td style="padding: 3px;" valign="top">term_number</td>
<td style="padding: 3px;" valign="top">int</td>
<td style="padding: 3px;" valign="top">journey</td>
<td style="padding: 3px;" valign="top">number of terms</td>
</tr>
<tr>
<td style="padding: 3px;" valign="top">is_churn_next_term</td>
<td style="padding: 3px;" valign="top">boolean</td>
<td style="padding: 3px;" valign="top">journey</td>
<td style="padding: 3px;" valign="top">did the user churn in the next term</td>
</tr>
</tbody>
</table>
<p>&nbsp;<br />Once we build a nice data set, we can do some data exploration.</p>
<ul>
<li>We can run queries on the top 50-100 highest margin users and see visually view if there obvious patterns that can guide deeper exploration.&nbsp;</li>
<li>If we see that these folks read lots of content, come in with low customer_acquisition_cost,&nbsp;and/or produce more non-subscription revenue - then we can dig on specifics of the behaviors related users achieving those actions/milestones.&nbsp;</li>
</ul>
<p>We can plot each of the metrics against number of terms and see which ones correlate well.&nbsp;</p>
<p>In the past I have built machine learning models to try to identify what variables drive retention and show us the probability that a user is going to churn from their membership in upcoming terms (a.k.a . Churn model). As you can probably imagine, there is a substantial amount of data engineering work (data capture, feature definition, cleanup/transformation/standardization/regularization/labeling, validation) needed and lots of data exploration before we consider running models.</p>
<p>I may devote a full future blog post to this, but for now I'll share some outside blog posts with helpful approaches along with python examples how to do this.&nbsp;</p>
<p>My preference is try to calculate a Subscriber Fragility Score that indicates the likeliness that a subscriber with churn in the upcoming billing term. Many folks use&nbsp; regression and classification models to do this:</p>
<ul>
<li><a href="https://365datascience.com/tutorials/python-tutorials/how-to-build-a-customer-churn-prediction-model-in-python/" target="_blank">https://towardsdatascience.com/predict-customer-churn-in-python-e8cd6d3aaa7<br /></a></li>
<li><a href="https://365datascience.com/tutorials/python-tutorials/how-to-build-a-customer-churn-prediction-model-in-python/" target="_blank">https://365datascience.com/tutorials/python-tutorials/how-to-build-a-customer-churn-prediction-model-in-python/<br /></a></li>
<li><a href="https://neptune.ai/blog/how-to-implement-customer-churn-prediction" target="_blank">https://neptune.ai/blog/how-to-implement-customer-churn-prediction</a></li>
</ul>
<div>&nbsp;Another approach is to try to predict how long the subscriber will remain with the subscription via Survivor Analysis:</div>
<div>&nbsp;</div>
<ul>
<li><a href="https://square.github.io/pysurvival/tutorials/churn.html" target="_blank">https://square.github.io/pysurvival/tutorials/churn.html<br /></a></li>
<li><a href="https://thedatascientist.com/customer-churn-machine-learning-data-science-survival-analysis/" target="_blank">https://thedatascientist.com/customer-churn-machine-learning-data-science-survival-analysis/</a></li>
</ul>
<div>I also enjoyed this blog post "Top Ten Mistakes Data Scientists Make While Building Churn Models":</div>
<div>&nbsp;</div>
<div>
<ul>
<li><a href="https://medium.com/@swansburg.justin/top-ten-mistakes-data-scientists-make-while-building-churn-models-d773bb7deaa5" target="_blank">https://medium.com/@swansburg.justin/top-ten-mistakes-data-scientists-make-while-building-churn-models-d773bb7deaa5</a></li>
</ul>
</div>
<h3>3) How can you get new visitors to buy things and become customers?</h3>
<p>This area is a huge focus in many companies and I know quite a few Product Managers who have over a decade each dedicated to working on incrementally improving buyer experiences and getting better conversion. In all those cases, they heavily rely on data (and analysts) to help guide their activites.</p>
<h4>Let's start with some questions&nbsp;</h4>
<p><em><strong>1. What is the value proposition of the subscription product? What is the problem that we think it solves and for what groups of people?</strong></em></p>
<p>There are many approaches and tools analysts can use to define a product's value proposition to users.</p>
<p>Peter Thompson introduces a diagram view that&nbsp;<a href="https://www.peterjthomson.com/2013/11/value-proposition-canvas/" target="_blank">he calls the Value Proposition Canvas</a>. He defines as:</p>
<p style="padding-left: 30px;">A value proposition is the place where your company&rsquo;s product intersects with your customer&rsquo;s desires. It&rsquo;s the magic fit between&nbsp;<strong>what</strong>&nbsp;you make and&nbsp;<strong>why</strong>&nbsp;people buy it. Your value proposition is the crunch point between business strategy and brand strategy.</p>

<p><img src="{{ site.baseurl }}/assets/images/4_questions_5.png" alt="" /></p>

<p><br />Below is an example illustration for an angel investing syndicate with a co-working space for the member investors and their portfolio of startups.</p>

<p><img src="{{ site.baseurl }}/assets/images/4_questions_6.png" alt="" /></p>

<p>I'd want to try to quantify the key elements of the value proposition, specifically from the buyer's perspective (wants, needs, fears and substitutes). We can try to collect factual information, statistics, or research results from credible external sources that support the value proposition. This could include industry expert opinions, awards, or mentions, as well as documented improvements or reductions in attributes like revenue or costs.</p>
<p><em><strong>2. How can a user purchase a subscription? More specifically what is the process users generally take to make purchases and what is the messaging about the product?</strong></em></p>
<p>A subscription funnel is an analytical method used to analyze the sequence of events&nbsp;in a subscription-based offering.&nbsp;&nbsp;Funnel analysis tracks user behavior throughout their user journey and calculates how many people make it through each step,&nbsp;allowing marketers / product managers to optimize their strategies and improve user experiences, hopefully leading to subscription growth.</p>
<p>Typical steps in a subscription funnel for a website can be broken down into the following stages:</p>
<ol>
<li><span style="text-decoration: underline;">Awareness</span>: The user becomes aware of the website or service. This can include visiting the website, receiving an email, receiving a referral or seeing an advertisement.</li>
<li><span style="text-decoration: underline;">Interest</span>: The user shows interest in the service by signing up for a newsletter, registering for a free trial, or subscribing to receive updates.</li>
<li style="box-sizing: border-box;"><span style="text-decoration: underline;">Evaluation</span>: The user evaluates the service by exploring features, reading reviews, or comparing it with competitors.</li>
<li style="box-sizing: border-box;"><span style="text-decoration: underline;">Conversion</span>: The user decides to subscribe or purchase the service.</li>
<li style="box-sizing: border-box;"><span style="text-decoration: underline;">Checkout</span>: The process and/or steps a user takes to subscribe to the service and make payment.&nbsp;</li>
</ol>
<p>Being able to leverage each action and signal in the&nbsp;<a href="#user_journey">user journey</a>&nbsp;(time series of events) discussed earlier provides an opportunity to understand the funnel for each user and across user groups.&nbsp; Below is a basic example of funnel visualization on a companies's checkout process that shows the drop off from each step:&nbsp;&nbsp;</p>

<p><img src="{{ site.baseurl }}/assets/images/4_questions_7.png" alt="" /></p>

<p>We could next take it further in a BI tool or App (streamlit, Dash, Shiny, etc)&nbsp; we add support for filtering based on user attributes such as demographics, referral method (ads, organic), user behaviors, timing, etc.</p>
<p><em><strong>3. What do you we know about the people who become subscribers? Are there consistent differences from the visitors to your site that do not become subscribers?&nbsp;</strong></em></p>
<p>When analyzing users who convert and users who don't for a subscription product, there are several data elements that can be useful for an analyst / data scientist:</p>
<ul>
<li><span style="text-decoration: underline;">User demographics</span>: Age, gender, location, and other demographic information can provide insights into the types of users who are more likely to convert. Analyzing these factors can help identify target segments for marketing and user acquisition efforts.&nbsp;</li>
<li><span style="text-decoration: underline;">User behavior</span>: Tracking user interactions with the product (via our&nbsp;<a href="#user_journey">user journey</a>), such as features used, frequency of use, and duration of sessions, can help identify patterns and trends among converting users. This information can be used to optimize the user experience and improve conversion rates.&nbsp;</li>
<li><span style="text-decoration: underline;">Conversion funnel analysis</span>: Analyzing the steps users take before converting can reveal potential bottlenecks or barriers to conversion. Identifying these issues can help optimize the conversion process and increase overall conversion rates.</li>
<li><span style="text-decoration: underline;">User feedback</span>: Collecting and analyzing user feedback, such as ratings, reviews, and survey responses, can provide valuable insights into user preferences, pain points, and areas for improvement. This information can be used to refine the product and marketing strategies to better cater to user needs.</li>
<li><span style="text-decoration: underline;">Marketing and promotional efforts</span>: Analyzing the effectiveness of marketing campaigns and promotional efforts can help identify which channels, messages, and targeting strategies are most successful in driving conversions. This information can be used to optimize marketing budgets and improve overall conversion rates.</li>
<li><span style="text-decoration: underline;">Customer lifetime value (CLV)</span>: Assessing the long-term value of customers can help determine which user segments are most profitable and inform decisions regarding customer acquisition, retention, and marketing strategies.</li>
</ul>
<p>You can likely do some quick exploration to see how much each of these metrics/areas correlate to subscription over different timeframes or cohorts.&nbsp;</p>
<h4>What can you try to learn more?</h4>
<p><strong>Ask users what they want and why they aren't buying</strong></p>
<p>You can ask them (maybe even offer some kind of bribe) to understand specifically what their needs/wants are and why do not think the value proposition of your offering will satisfy them at an acceptable cost.&nbsp;</p>
<p>I would want to find out if they think:</p>
<ul>
<li>Do they actually think they need your product?</li>
<li>Are your prices too high?</li>
<li>Is there a competitor with a better suited offering or better distribution?</li>
<li>Are their friends using your product and giving positive feedback about it?</li>
</ul>
<div><strong>Record their sessions and see what they see + do</strong></div>
<div>&nbsp;</div>
<div>There are a number of&nbsp;<a href="https://siterecording.com/best-website-session-recording-software" target="_blank">SaaS services</a>&nbsp;that offer the ability integration full session recording directly into your web site. I have used&nbsp;<a href="https://www.fullstory.com/session-replay/" target="_blank">Full Story</a>&nbsp;in the past and it was helpful in some scenarios.</div>
<div>&nbsp;</div>
<p><strong>Look at the Churn Model.&nbsp;</strong></p>
<p>I have found that it is often the case that the reasons why subscribers churn is also the reasons why users are hesitant to subscribe to your product. You may find overlap from an asset you already have.</p>
<p><strong>Test Things and Run Experiments</strong></p>
<p>You can conduct experiments to test different versions of your website or app to identify the most effective design, content, messaging, or features that drive customer engagement and conversion. You can use statistical hypothesis testing to determine the impact of each variation on customer behavior.</p>
<div>Some specific places that can be effective in terms of learning and potentially impacting conversion:</div>
<div>
<ul>
<li>Promotions: Impact&nbsp;from offering a trial or promotion (discount) for new subscriptions.</li>
<li>Pricing: Impact&nbsp;from offering different pricing options or the impact of different display variations of the pricing options.</li>
<li>Funnel / Checkout: Impact from changing the steps in your checkout process.</li>
<li>Messaging: The way you message about your product's benefits (on site, partner sites, email, physical collateral, etc.).</li>
<li>Channels: Impact from the places your product is promoted and referred from.</li>
</ul>
</div>
<p><strong>Customer Segmentation, Targeting and Personalization</strong></p>
<p><strong></strong>You can analyze customer data to identify different segments and their behavior patterns. This will help you understand which customers are more likely to make a purchase or subscribe to your services. You can use clustering algorithms like K-means or DBSCAN to group customers based on their characteristics, such as demographics, purchase history, and browsing behavior.</p>
<h3>4) How can you build awareness/traffic to your offerings?</h3>
<p>I do not claim to be a growth marketing expert, but I have worked with some very smart folks in this domain area and have learned some things along the way.&nbsp;</p>
<h4>Marketing Channels</h4>
<p>Here are some of the tactics I have seen used to varying degrees of effectiveness to build awareness and traffic to your subscription offerings as a growth marketer:</p>
<p><strong>Leverage social media platforms</strong></p>
<p><strong></strong>Facebook, Instagram, YouTube, Twitter, TikTok, LinkedIn and others have HUGE highly active audiences. They can be fantastic avenues for attracting potential customers.</p>
<p>Much of the volume is done as part of running brand-awareness (paid) ads that target specific audiences based on behaviors and preferences. This can help improve reach and recall, and even create 'lookalike' audiences similar to your existing followers. These platforms offer many different ad formats and creative display options that can appeal to your potential audience.</p>
<p>In addition to paid options, many of the social networks provide opportunities to promote your offerings (just be careful not to be too spammy or obnoxious about promoting your business). Facebook and LinkedIN groups are often dedicated to specific niche topics and may be a good avenue to find users.</p>
<p>Example - I am part of many Facebook groups related to sports autograph collecting, so I often answer questions letting newer collectors know they can find information they seek on&nbsp;<a href="https://www.sportscollectors.net/" target="_blank">SportsCollectors.Net</a>.</p>
<p><strong>Organize contests and giveaways</strong></p>
<p><strong></strong>Host simple content or giveaways to grow your following and drive brand awareness.</p>
<p>Example - Tech Influencer&nbsp;<a href="https://www.linkedin.com/in/alexxubyte/" target="_blank">Alex Xu</a>&nbsp;gives away his popular book "System Design Interview" in order to promote his excellent&nbsp;<a href="https://blog.bytebytego.com/" target="_blank">ByteByteGo</a>&nbsp;paid newsletter.</p>
<p><strong>Give something away for free</strong></p>
<p><strong></strong>Offer a free trial or free samples of your subscription service or product to give potential customers a taste of what you offer.</p>
<p>Example - I signed up for a free one month trial for&nbsp;<a href="https://github.com/features/copilot" target="_blank">Github's Copilot</a>&nbsp;service that offers suggestions as I write code.</p>
<p><strong>Content marketing</strong></p>
<p><strong></strong>Publish blog posts, articles, or other forms of content to establish your expertise and thought leadership in your industry. This can help build brand awareness and drive traffic to your website.</p>
<p>Example -&nbsp;<a href="https://www.youtube.com/@SportsCardInvestor" target="_blank">Sports Card Investor</a>&nbsp;hosts regular podcasts talking about the latest high level market trends and features in their MarketMovers subscription product for sports card sales data and analytics.</p>
<p><strong>Influencer marketing</strong></p>
<p><strong></strong>Collaborate with influencers and brand ambassadors to promote your subscription offerings and reach a wider audience.</p>
<p>Example - This&nbsp;<a href="https://www.youtube.com/watch?v=aNv1qZ54YzQ" target="_blank">youtube video</a>&nbsp;from Mr. Beast, which has 1M+ views &mdash; it&rsquo;s engaging and encourages the audience to give Honey a try. Companies such&nbsp;<a href="https://company.shopltk.com/en/company" target="_blank">LTK</a>&nbsp;can even help you find influencers that would work for your company.</p>
<p><strong>Email marketing</strong></p>
<p><strong></strong>Use email marketing to engage with your audience and promote your subscription offerings.</p>
<p>Example - While I was at ElysiumHealth, their quarterly newsletters (<a href="https://milled.com/elysium-health/the-abstract-scientific-breakthroughs-of-2022-m44p3qKmgIJQJF5f" target="_blank">the Abstract</a>) were packed with deep scientific dives on longevity topics related that drove interest to their core products.&nbsp;</p>
<p><strong>Employee advocacy</strong></p>
<p><strong></strong>Encourage your employees to share your brand and subscription offerings on their social media platforms. Create a culture where employees proactively want to evangelize your organization.</p>
<p>Example - I saw that many of the employees at&nbsp;<a href="https://www.renttherunway.com/" target="_blank">Rent the Runway</a>&nbsp;across business different functions in the company would publicly suggest the company's subscription offerings as well as specific rental products.&nbsp;</p>
<p><strong>Events</strong></p>
<p>Hosting Physical events to introduce your product to new audiences.</p>
<p>Example - Amazon hosts events at their&nbsp;<a href="https://aws-startup-lofts.com/amer/" target="_blank">AWS Startup Lofts</a>&nbsp;where experienced AWS architects can help you design solutions to solve your technical problems using AWS products.</p>
<p><strong>Search Engine Optimization (SEO)</strong></p>
<p>Marketing technique that is focused on bringing organic, non-paid traffic to your website by using high quality content to get to the top of a search engine results page.</p>
<h4>We want to analyze what works (and doesn't)</h4>
<p>We will want a way to understand the impacts of traffic and conversion/sales from each of the marketing channels your company employs.&nbsp;</p>
<p><strong>Common Problem - Disparate reporting does not reconcile</strong></p>
<p>I have found that many marketers (potentially in different marketing functions) generally use SaaS products for their specific channels that each allow you to set up tagging on your site to track activities within their application.&nbsp;</p>
<ul>
<li>Your email service provider (like&nbsp;<a href="https://mailchimp.com/" target="_blank">MailChimp</a>,&nbsp;<a href="https://iterable.com/" target="_blank">Iterable</a>,&nbsp;<a href="https://www.klaviyo.com/" target="_blank">Klaviyo</a>, etc) may attribute the full value of customer purchase if the user has opened an email sent from their platform.</li>
<li>Facebook and Google may attribute the full value of that same customer purchase if users were sent to your site from paid ads you bought on their systems.&nbsp;</li>
<li>Your influencer or referral partners (like&nbsp;<a href="https://www.yotpo.com/" target="_blank">Yopto</a>,&nbsp;<a href="https://www.talkable.com/" target="_blank">Talkable</a>,&nbsp;<a href="https://www.mention-me.com/" target="_blank">Mention Me</a>, etc) may also attribute the full value of that same customer purchase if users were sent to your site from referral links from their systems.&nbsp;</li>
</ul>
<p>So what generally happens is that the leads for email, paid, and influencer marketing download the performance reports from their respective SaaS system and present an overstated ROI for their area. You won't have a clear picture of how well each channel is contributing and how to prioritize spend between. This invariably leads to questions from your finance team when budgets are being planned out and the numbers don't all add up.&nbsp;</p>
<p><strong>How Rules Based Attribution Models Work</strong></p>
<p>Rules Based (non-model driven) marketing attribution models assign credit to specific marketing channels or touch points based on predetermined rules or assumptions, rather than analyzing data and customer behavior patterns to determine attribution.</p>
<p>These models are popular because they are easy to understand, relatively straight forward to implement and can provide directional signal around ROI. They can be a good choice for businesses that are just starting out, have limited resources or have less a complex/diverse marketing channel mix. I have personally seen most companies implement these kinds of models.</p>
<p>There are several types of rules based attribution models that businesses can use. These include:</p>

<p><img src="{{ site.baseurl }}/assets/images/4_questions_8.png" alt="" /></p>

<p>Our <a href="#user_journey">user journey</a>&nbsp;data contains the full set of actions associated with each user that we are able to track from internal and partner systems. It is industry standard that we set up&nbsp;<a href="https://en.wikipedia.org/wiki/UTM_parameters" target="_blank">UTM parameters</a>&nbsp;on incoming links that will inform us of the source, medium, and campaign associated with that user's visit. We will store those parameters as part of payload for each relevant event that we can later use for analysis.</p>
<p>We will want to use user journey to be able to track individual customer interactions with our brand. The closest method we have to do this is with sessions, defined as discrete periods of activity by a user. The industry standard is to define a session as a series of activities followed by a 30 minutes window without activity. So we can write some code to categorize the actions in our user journey into sessions, with the first action representing channel information we will use for attribution of that session. We can use all of the sessions before a conversion as part of our attribution calculation.&nbsp;</p>
<p>Based on the preferred attribution methodology shown in the diagram above, we can divide up the sessions for each transaction and attribute the revenue (and return on associated marketing spend) accordingly.</p>
<p>Claire Carroll wrote a helpful&nbsp;<a href="https://www.getdbt.com/blog/modeling-marketing-attribution/" target="_blank">blog post</a>&nbsp;detailing an example of how to model out user journey data into sessions with only SQL using the dbt framework. It is very similar to the methodology I have taken in the past do this.</p>
<p>My personal preference is to use either Time Decay or Position-Based models. I have found the most important thing is that you get consensus with the model that everyone will use up front, and then optimize around the related metrics.</p>
<p><strong>Data Driven Attribution</strong></p>
<p>Data-driven attribution gives credit for conversions based on how people engage with your various ads and decide to become your customers.</p>
<p>Unlike the previous discussed rules based models, data-driven attribution gives you more accurate results by analyzing all of the relevant data about the marketing moments that led up to a conversion. Google uses this data-driven attribution in Google Ads&nbsp; as the models takes multiple signals into account, including the ad format and the time between an ad interaction and the conversion. They can drive better conversions because their systems can better predict the incremental impact a specific ad will have on driving a conversion, and adjust bids accordingly to maximize your ROI.&nbsp;</p>
<p>There are two machine learning models that have become popular to use for data driven attribution:</p>
<p><span style="text-decoration: underline;">1. Shapely Value</span></p>
<p>The Shapley value is a way to fairly distribute credit for a shared outcome among team members by applying an algorithm based on a concept from cooperative game theory called the Shapley Value. In the case of data driven attribution, marketing touchpoints are the "team members", and the "output" of the team is conversions. The&nbsp; algorithm computes the counterfactual gains of each marketing touchpoint, which means it compares the conversion probability of similar users who were exposed to these touchpoints to the probability when one of the touchpoints does not occur in the path. The actual calculation of conversion credit for each touchpoint depends on comparing all of the different permutations of touchpoints and normalizing across them.</p>
<p>James Kinney posted a nice explanation of how Game Theory and Shapely Value can be applied to&nbsp;<a href="https://medium.com/towards-data-science/data-driven-marketing-attribution-1a28d2e613a0" target="_blank">Data Driven Marketing Attribution</a>. He also provides a helpful Jupyter notebook with python code on his&nbsp;<a href="https://github.com/jrkinley/game-theory-attribution/blob/main/game_theory_attribution.ipynb" target="_blank">Github</a>.</p>
<p><span style="text-decoration: underline;">2. Markov Model</span></p>
<p>A Markov model is a type of probabilistic model that describes a sequence of events where the probability of each event depends only on the state of the previous event. In the context of marketing attribution, a Markov model can help us model user journeys and how each channel factors into users moving from one channel to another to eventually make a purchase.</p>
<p>For example, let's say a user first sees a Facebook ad, then clicks on a Google search result, and finally makes a purchase on your website. A Markov model would help us understand the probability of users moving from Facebook to Google and from Google to your website.</p>
<p>One advantage of using a Markov model for marketing attribution is that it can account for the structure of your data, which may lead to more accurate results. However, it is more complex than other attribution models, and may require the help of a data scientist to implement at scale.</p>
<p>To use a Markov model for marketing attribution, we need to estimate a transition matrix that describes the probability of moving from one channel to another. We can then compute the "removal effects" of each channel, which tells us the probability of conversion when a channel is removed from the user journey. This allows us to determine each channel's contribution to conversion and/or value.</p>
<p>James Kinney posted Cloudera uses Markov models to solve multi-channel attribution in his post&nbsp;<a href="https://towardsdatascience.com/multi-channel-marketing-attribution-with-markov-6b744c0b119a" target="_blank">Marketing Attribution with Markov</a>.&nbsp;</p>
<p><strong>Challenges with Attribution&nbsp;</strong></p>
<ul>
<li>It requires set up and discipline to collect all of the necessary data in a central place (like a data warehouse). That means your team needs the data engineering skill sets to do the appropriate pipeline building, transformation and modeling.&nbsp;</li>
<li>Your user journey may be missing some percentage of actions. Many have found it to be challenging to associate actions with anonymous (non signed in) users using mobile applications and mobile browsers.&nbsp;</li>
<li>It is challenging to find a proxy for incorporating offline data, such as exposure to a TV, radio or print ad.</li>
<li>Lack of visibility into external trends that might affect marketing efforts and conversions, such as seasonality, without incorporating aggregate information.</li>
<li>Attribution models can be subject to correlation-based biases when analyzing the customer journey, causing it to look like one event cause another, when it may not have.&nbsp;</li>
<li>Consumers who may have been in the market to buy the product and would have purchased it whether they had seen the ad or not. However, the ad gets the attribution for converting this user.</li>
<li>Bias toward cheap Inventory gives an inaccurate view of how media is performing, making lower cost media appear to perform better due to the natural conversion rate for the targeted consumers, when the ads may not have played a role.</li>
<li>Attribution models can often overlook the relationship between brand perception and consumer behavior, or will only look at them at a trend regression level.</li>
<li>The quality of creative and messaging are just as important to consumers as the medium on which they see your ad. One common attribution mistake is evaluating creative in aggregate and determining that one message is ineffective, when in reality it would be effective for a smaller, more targeted audience. This emphasizes the importance of person-level analytics.&nbsp;</li>
</ul>