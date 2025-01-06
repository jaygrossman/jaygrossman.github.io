---
layout: post
title:  "Chrome extension to search for person's address across multiple sites"
author: jay
tags: [ chrome, extension, javascript, sportscollectors ] 
image: assets/images/headers/address_search.png
description: "Chrome extension to search for person's address across multiple sites"
featured: false
hidden: false
comments: false
---

<table style="width: 100%; border-color:red;" border="1" cellpadding="5">
<tr>
<td>
   <strong><i>Please Note:</i></strong><br>
   This post is for entertainment and research purposes only.  I am not responsible for any actions you take based on the information in this post. Please respect the privacy of others and use the information you find responsibly.
    </td>
</tr>
</table>
<br>

<p><a href="https://developer.chrome.com/docs/extensions/develop" target="_blank">Chrome extensions</a> allow developers to add functionality to your Chrome Browser.  While folks can do malicious things within chrome extensions, I have had a lot of fun building extensions that can improve my personal productivity. I <a href="https://jaygrossman.com/decorating-sites-with-browser-extensions/" target="_blank">blogged about an extension</a> in 2013 that showed the WeightWatchers points values next to food items on grubhub and other sites.</p>

<h2>The Challenge:</h2>

<p>I run an autograph community (<a href="https://sportscollectors.net" target="_blank">sportscollectors.net</a>) where collectors mail letters to athletes to request autographs. Sometimes I want to locate or confirm an address of a player, and I'd like to make the process more efficient.</p>

<h2><span style="color: #ff0000;"><em><strong>I want to initiate searches for a person's address on multiple free sites in one action.</strong></em></span></h2>

<hr>
<h2>My Solution: Address Finder Extension</h2>

<p>Code for this extension is posted on github:<br>
<a href="https://github.com/jaygrossman/AddressFinderExtension" target="_blank">https://github.com/jaygrossman/AddressFinderExtension</a>
</p>

<h4>Project Requirements:</h4>


Chrome extension needs to:
<ol> 
<li>Prompt the user for information about the person:
  <ul>  
    <li>First Name (required)</li>
    <li>Last Name (required)</li>
    <li>City</li>
    <li>State</li>
    <li>Age</li>
  </ul>
</li>
<li>Initiate searches on the following sites:
  <ul>
    <li><a href="https://www.anywho.com" target="_blank">anywho.com</a></li>
    <li><a href="https://www.familytreenow.com" target="_blank">familytreenow.com</a></li>
    <li><a href="https://www.fastpeoplesearch.com" target="_blank">fastpeoplesearch.com</a></li>
    <li><a href="https://www.searchpeoplefree.com" target="_blank">searchpeoplefree.com</a></li>
    <li><a href="https://www.smartbackgroundchecks.com" target="_blank">smartbackgroundchecks.com</a></li>
    <li><a href="https://thatsthem.com/" target="_blank">thatsthem.com</a></li>
    <li><a href="https://www.truepeoplesearch.com" target="_blank">truepeoplesearch.com</a></li>
    <li><a href="https://www.usphonebook.com" target="_blank">usphonebook.com</a></li>
    <li><a href="https://people.yellowpages.com/whitepages/" target="_blank">yellowpages.com</a></li>
  </ul>
</li>
<li>Display the results from each site in a new tab.</li>
</ol>

<h4>How to use the extension:</h4>

<p>In the browser's toolbar, click on the <img src="{{ site.baseurl }}/assets/images/address_finder/black_home_icon.png" width="30" alt="home icon"/> icon and the search form will appear:</p>

<p>
<img src="{{ site.baseurl }}/assets/images/address_finder/search_form.png" alt="search_form" width="50%" style="border: 1px solid #000000;"/><br/></p>

<p>In the form below, I am searching for Otis Sistrunk (former Oakland Raiders All Pro and Super Bowl champ). Once the First Name and Last Name fields are populated, the Search button will be enabled.</p>

<p>
<img src="{{ site.baseurl }}/assets/images/address_finder/search_form_with_values.png" alt="search_form" width="50%" style="border: 1px solid #000000;"/><br/></p>

<p>After I click the Search button and the extension will open a new tab for each of the sites listed above with the search results for the person you entered:</p>
<p>
<img src="{{ site.baseurl }}/assets/images/address_finder/browser_with_tabs_open.png" alt="browser_with_tabs_open" style="border: 1px solid #000000;"/><br/></p>


<h4>Installing the extension:</h4>

<p>
<ol>
<li>Clone the <a href="https://github.com/jaygrossman/AddressFinderExtension" target="_blank">AddressFinderExtension repo</a> locally to your computer</li>
<li>Open Chrome and navigate to <a href="chrome://extensions/" target="_blank">chrome://extensions/</a><br>
<br>
You will see a page like this:<br>
<img src="{{ site.baseurl }}/assets/images/address_finder/install_extension.png" alt="install extension" style="border: 1px solid #000000;"/><br/>
<br>
</li>
<li>Click the "Load unpacked" button and navigate to the folder where you cloned the repo.  Select the folder and click "Select Folder".</li>
<li>You will see an entry for Address Finder Extension 1.0 as shown in the screen above.</li>
</ol>
</p>

<h4>Enabling the extension:</h4>

<p>
<img src="{{ site.baseurl }}/assets/images/address_finder/enable_extension.png" alt="enable extension" style="border: 1px solid #000000;"/><br/>

<ol>
<li>Click on the puzzle icon in the browser toolbar (as shown in the screen above)</li>
<li>Click on the <img src="{{ site.baseurl }}/assets/images/address_finder/pin_icon.png" width="25" alt="pin icon"/> icon next to Address Finder Extension</li>
<li>You should now see the <img src="{{ site.baseurl }}/assets/images/address_finder/black_home_icon.png" width="30" alt="home icon"/> icon in the toolbar next to the puzzle icon</li>
</ol>




<hr>
<h2>Frequent Questions about searching for addresses:</h2>

<p><b>Is the information on these sites accurate?</b></p>
<p><blockquote>
The sites I am searching are free sites that provide information about people.  The information is not always accurate or completely up to date.<br>
<br>I have stronger confidence when I see that multiple sites have the same information and when the results indicate they have been updated recently.</blockquote></p>

<p><b>How do you know the result you are seeing is for the person you are searching for?</b></p>
<p><blockquote>It is more challenging when searching for people that have common names.<br>
<br>I generally start by looking for the person's age and city to confirm that the address is for the person I am searching for. <br>
<br>
Also many of these sites provide a list of previous addresses the person has lived at or owned, so I try to cross reference those addresses with ones I may know (they are often recorded on sportscollectors.net).</blockquote></p>

<p><b>What if the person you are searching for is not found?</b></p>

<p><blockquote>It is possible that the person you are searching for is not in the databases of the sites I am searching.  It is also possible that the person has taken steps to remove their information from these sites.</blockquote></p>
