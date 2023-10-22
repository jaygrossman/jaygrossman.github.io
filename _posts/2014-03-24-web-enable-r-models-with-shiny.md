---
layout: post
title:  "Web Enable R models with Shiny"
author: jay
categories: [ code ]
tags: [ code ]
image: assets/images/headers/shiny.png
description: "Web Enable R models with Shiny"
featured: false
hidden: false
comments: false
#rating: 4.5
---


 <p>Last September I was preparing for my fantasy football league and I found this really cool<a href="http://fantasyfootballanalytics.net:3838/Auction%20Draft/" target="_blank">&nbsp;web app</a>&nbsp;that suggested the optimal draft recommendations based on the rules of your league. I then found the associated article on r-bloggers that explained how it was done:</p>
<p><a href="http://www.r-bloggers.com/win-your-fantasy-football-snake-draft-with-this-shiny-app-in-r/" target="_blank">http://www.r-bloggers.com/win-your-fantasy-football-snake-draft-with-this-shiny-app-in-r/</a></p>
<p><strong style="margin: 0px; padding: 0px;"><span style="margin: 0px; padding: 0px; text-decoration: underline;">Shiny is the secret sauce to make this possible:</span></strong></p>
<p>The Shiny package makes it super simple for R users like you to turn analyses into interactive web applications that anyone can use. Let your users choose input parameters using friendly controls like sliders, drop-downs, and text fields. Easily incorporate any number of outputs like plots, tables, and summaries. No HTML or JavaScript knowledge is necessary. If you have some experience with R, you&rsquo;re just minutes away from combining the statistical power of R with the simplicity of a web page.</p>
<table cellpadding="5">
<tbody style="margin: 0px; padding: 0px;">
<tr style="margin: 0px; padding: 0px;">
<td style="margin: 0px; padding: 0px;" valign="top"><img style="margin: 0px 10px 0px 0px; padding: 0px 10px 0px 0px; float: left;" src="{{ site.baseurl }}/assets/images/shiny_client_server.png" alt="shiny_client_server" width="600" /></td>
<td style="margin: 0px; padding: 0px;" valign="top">
<p style="margin: 0px 0px 1.5em; padding: 0px;">So Shiny takes advantage of a familiar Client-Server pattern. The package will generate HTML layout and javascript code (I see includes for bootstrap.js), that calls wraps calls to our R logic</p>
<p style="margin: 0px 0px 1.5em; padding: 0px;">CRAN has the full reference for the shiny package.&nbsp;<a href="http://cran.r-project.org/web/packages/shiny/shiny.pdf" target="_blank">Download pdf here</a></p>
</td>
</tr>
</tbody>
</table>
<p><span style="margin: 0px; padding: 0px; text-decoration: underline;"><strong style="margin: 0px; padding: 0px;">Nice Intro to Shiny</strong></span></p>
<p>This week I got an email from&nbsp;<a href="https://www.linkedin.com/profile/view?id=12174710" target="_blank">Vivian Zhang</a>&nbsp;that she was going to introduce Shiny as part of her weekly NYC Open Data meetup. Since it was walking distance from my office, I decided to attend. She posted a blog entry that does a pretty good job detailing the session (along with a video of the entire 2 hour session):</p>
<p><a href="http://www.nycopendata.com/2014/03/25/building-interactive-web-app-with-shiny/" target="_blank">http://www.nycopendata.com/2014/03/25/building-interactive-web-app-with-shiny/</a></p>
<p>The project for the class was to create a Shiny project to run linear regression and return the summary and plot of the summary. I was able to solve this and Vivian included&nbsp;<a href="https://gist.github.com/casunlight/9771411" target="_blank">my code</a>.</p>
<p>So here are the key takeaways:</p>
<ul>
<li>You need R version 3.0+.&nbsp;<a href="http://cran.r-project.org/bin/" target="_blank">Download here</a></li>
<li>You need R-Stuidio.&nbsp;<a href="http://www.rstudio.com/ide/download/desktop" target="_blank">Download here</a></li>
<li>The Shiny tutorials and examples are available on the&nbsp;<a href="http://www.rstudio.com/shiny/" target="_blank">rstudio site</a>, and they are really helpful</li>
<li>You'll need to create ui.R script. This is where you define the form controls (dropdown lists, numeric inputs, text boxes, check boxes, radio buttons, file upload) and the output panels to display the results of your models (text, tables, plots).&nbsp;</li>
<li>You'll need to create server.R script. This script takes in the form inputs, processes logic, and returns the content to output to the user.</li>
</ul>
<div>&nbsp;</div>
<div><span style="margin: 0px; padding: 0px; text-decoration: underline;"><strong style="margin: 0px; padding: 0px;">Fun toy I built with Shiny</strong></span></div>
<div>&nbsp;</div>
<p>Last June I did some work using R to build models to&nbsp;<a href="http://www.jaygrossman.com/post/2013/06/10/Predicting-eBay-Auction-Sales-with-Machine-Learning.aspx" target="_blank">predict Ebay Sales</a>. I thought this would be a really great opportunity to use Shiny to web enable those models.</p>
<p>On Thursday (3/27), I was able to create the Shiny app show below on my round trip train commute in one day (roughly 2 hours):&nbsp;</p>

<p><img src="{{ site.baseurl }}/assets/images/autograph_predictor.gif" alt="auction predictor" /></p>
<p>The user is prompted to:</p>
<ol>
<li>select a player</li>
<li>select the category</li>
<li>enter the seller's ebay id</li>
<li>indicate whether item is authenticated</li>
<li>enter the minimum bid</li>
</ol>
<div>The application returns:</div>
<div>&nbsp;</div>
<div><ol style="margin: 0px 0px 15px 35px; padding: 0px;">
<li>historical sales reference info for the sku (player+category)</li>
<li>the predicted probability that the listing will result in a sale</li>
<li>the predicted sale price and predicted price bucket (category)</li>
</ol></div>
<div>I have shared my code and a very limited sample data set on github at:</div>
<div>&nbsp;</div>
<div><a href="https://github.com/jaygrossman/EbaySalesPredictionShiny" target="_blank">https://github.com/jaygrossman/EbaySalesPredictionShiny</a>&nbsp;</div>
<div>&nbsp;</div>
<div>This is little app is going to make it so much easier for me to determine deals on eBay, as it's much easier than munging some data and firing up R on the command line,</div>
<div>&nbsp;</div>
