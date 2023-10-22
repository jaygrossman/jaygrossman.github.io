---
layout: post
title:  "Decorating sites with Browser Extensions"
author: jay
categories: [ analysis, business ]
tags: [ WeightWatchers, Chrome, extension, javascript, favorites ]
image: assets/images/headers/chrome_extensions.png
description: "Decorating sites with Browser Extensions"
featured: true
hidden: false
comments: false
redirect_from:
  - /post/2013/11/09
#rating: 4.5
---
<p>We&rsquo;re big fans of internet grocer FreshDirect in my household. When we lived in Brooklyn and now in North Jersey, we place a delivery order about once a week. While there is a lot I like about the service offering, there are a few features that could improve the user experience.</p>
<p>One of the big ones for me would be to associate WeightWatchers points values for each food on the site. It would save me a bunch of time and frustration if my wife and I would be able to see them while we were filling our cart.&nbsp;</p>
<p><img src="{{ site.baseurl }}/assets/images/ww_fd.gif" alt="" /></p>
<p>&nbsp;</p>
<p>I knew this was going to be a "fun" project since both FreshDirect and WeightWatchers do not offer public APIs. The two big challenges would be:</p>
<ol>
<li>How will I assign points values to foods?<br style="margin: 0px; padding: 0px;" /><br style="margin: 0px; padding: 0px;" /></li>
<li>How can we effectively present the points information as part of the shopping experience without needing FreshDirect to make changes to their site?</li>
</ol>
<p><strong style="margin: 0px; padding: 0px;">Getting foods and their points values</strong></p>
<p>The first requirement is that I need to associate the foods on their site with their respective points values. &nbsp;I wrote a process that spidered all of the category pages on FreshDirect.com and recorded Nutritional information for each product found. I then used that information to calculate the points values and saved it to a json file with the following format:</p>


    { "items": [
        { "id":"gro_sprite_diet_le_02" ,"points": "0" },
         { "id":"cat_hldy_chsplatter" ,"points": "3" }, 
         { "id":"mea_pid_3335013" ,"points": "4" } 
    ] }
<p><strong style="margin: 0px; padding: 0px;">Displaying values on FreshDirect.com</strong></p>
<p>I needed a way to show the points values on FreshDirect.com without requiring them to make any changes. The only way I think of injecting content into a site was triggering some javascript during the user&rsquo;s session. Lucky for me, that is exactly the purpose of browser extensions.</p>
<p>The first thing I did was open up a category page on FreshDirect.com in my Google Chrome browser. I opened Chrome&rsquo;s javascript console (Ctrl+Shift+J) and wrote some code to parse the DOM looking for all the products on the page. I then inserted the points value under each product link. I took the code from the console and saved it to a file called contentscript.js.</p>
<p>I needed to get access to my json file with all the points values (for this exercise it is hosted locally at http://localhost/points-foods.txt). When I tried to access the json file, Chrome threw an error message that reminded me that most browsers will block calls to other sites to prevent malicious activities. While I could have gotten this to work with&nbsp;<a style="margin: 0px; padding: 0px; text-decoration: none; color: #1fa2e1;" href="http://en.wikipedia.org/wiki/JSONP" target="_blank">JSONP</a>, extensions are designed to provide support for solving this problem.</p>
<p>To create a Chrome extension, you need a manifest.json file (a very basic example below). The &ldquo;matches&rdquo; attribute defines the domains you want the scripts in the extension to access. The &ldquo;js&rdquo; attribute defines the scripts that will execute when a user navigates to a page in one of those domains.</p>

    {
        "name": "WeightWatchers Points Values",
        "manifest_version": 2,
        "version": "1.0.0.0",
        "content_scripts": [{
                "js": ["contentscript.js"],
                "matches": ["https://www.freshdirect.com/*",
                "http://www.freshdirect.com/*",
                "http://localhost/*"]
        }]
    }


<p>I opened chrome://extensions/ and checked the &ldquo;Developer Mode&rdquo; checkbox. I loaded the unpacked extension by navigating to the folder that contained contentscript.js and manifest.json files. On the screen below, you&rsquo;ll see the WeightWatchers Points Values 1.0.0.0 available:&nbsp;</p>

<p><img src="{{ site.baseurl }}/assets/images/extensions.gif" alt="" /></p>
<p>&nbsp;</p>
<p>With the extension enabled, you&rsquo;ll see the WeightWatchers points value injected below each product on the screen below&nbsp;(with the blue points icons):</p>

<p><img src="{{ site.baseurl }}/assets/images/fresh_direct.gif" alt="" /></p>
<p>&nbsp;</p>
<p><strong style="margin: 0px; padding: 0px;">Conclusions</strong></p>
<ol>
<li>Seeing the points values changed our purchasing habits in some cases. We chose to buy a more expensive type of pork ribs because it was 5 points compared to the cheaper 9 point type we have ordered in the past. FreshDirect made more money and we were happy to make a healthier choice, that&rsquo;s a win-win.<br style="margin: 0px; padding: 0px;" /><br style="margin: 0px; padding: 0px;" /></li>
<li>I love loosely coupled design patterns (like decorators), where the systems can function completely independent of one another. &nbsp;This function works really well and it was built without any interaction of either FreshDirect or WeightWatchers.<br style="margin: 0px; padding: 0px;" /><br style="margin: 0px; padding: 0px;" /></li>
<li>This was my first browser extension. &nbsp;There is amazing potential with this as a mechanism for delivering recommendations and personalized content. You just need to convince folks of the value to install/use your extension.<br style="margin: 0px; padding: 0px;" /><br style="margin: 0px; padding: 0px;" /></li>
<li>My original project idea was to programmatically log into my Fresh Direct account and assign points values to all the food we had ordered (100+ orders). I spent about 3 hours to get to this work with Powershell code. Then I got more ambitious and decided to spider for all the products, which consumed another 2-3 hours.<br style="margin: 0px; padding: 0px;" /><br style="margin: 0px; padding: 0px;" /></li>
<li>While I know I could have done this with far more elegant code using JQuery (since Chrome has the awesome&nbsp;<a style="margin: 0px; padding: 0px; text-decoration: none; color: #1fa2e1;" href="http://developer.chrome.com/extensions/content_scripts.html" target="_blank">Content Scripts support</a>), I decided to use XMLHttpRequest and old school DOM parsing. This way the same code could be applied to other browsers that don't support content scripts.&nbsp;<br style="margin: 0px; padding: 0px;" /><br style="margin: 0px; padding: 0px;" />I took about 4-5 hours to write all the javascript code and figure out how extensions work.</li>
</ol>
<div><strong><span><span>The follow on project:</span></span></strong></div>
<div><span><span><br /></span></span></div>
<div><span><span>Since I saw a lot of my friends were using seamless to order from local restaurants, so I wanted to put my extension concept to the test.</span></span></div>
<div>&nbsp;</div>
<div><span style="text-decoration: underline;"><span><span>First there was some ground work to do:</span></span></span></div>
<div><span><span>- further built out my food catalog to over 500,000 foods + menu items at&nbsp;</span></span><span">restaurant chains</span><span><span>&nbsp;that published nutritional values.</span></span></div>
<div><span><span>- I cross referenced the menu items on the seamless ordering pages with my catalog (using the lucene search engine was a big help here).</span></span></div>
<div><span><span><br /></span></span></div>
<div><span style="text-decoration: underline;"><span><span>User features:</span></span></span></div>
<div><span><span>- I displayed points values for food items on seamless menu pages (also on menupages.com and grubhub).</span></span></div>
<div><span><span>- I also displayed indicators when I had previously tracked a menu item that I had eaten (within my weight watchers food journal)</span></span></div>
<div><span><span>- If I clicked on the points icon, a modal appeared to track my food item in the food journal and/or share it on social media</span></span></div>
<div><span><span>&nbsp;</span></span></div>
<div><span><span>Consistent with my activities on FreshDirect.com, I noticed that my lunch order choices changed because I had this new information.&nbsp;</span></span></div>
<div>
<div>&nbsp;</div>
<div><strong><span><span>Future applications for browser extensions I hope to explore:</span></span></strong></div>
<div><span><span><br /></span></span></div>
<div><ol>
<li>Having the ability to see opinions of my specific social groups for products/content. <br />Maybe try some sentiment analysis with thumbs up/thumbs down indicators or ratings scores. I would love this for Netflix!<br /><br /></li>
<li>Enhancing shopping experiences and deal finders. <br />Would be awesome for something to tell me the popularity of an item and optimal bid amount while shopping on eBay. Then I could integrate the extension with my sniper with a single click.<br /><br /></li>
<li>Building productivity/administrative tools on top of my every day web applications (Gmail, banking, my sites, etc.)</li>
</ol></div>
<div>&nbsp;</div>