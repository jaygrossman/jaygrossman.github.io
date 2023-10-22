---
layout: post
title:  "Adding Facebook Comments to BlogEngine.Net"
author: jay
categories: [ analysis, business ]
tags: [ blogengine, facebook, comments ]
image: assets/images/headers/facebook_comments.gif
description: "Adding Facebook Comments to BlogEngine.Net"
featured: false
hidden: false
comments: false
#rating: 4.5
---

  <p >This blog runs using the open source&nbsp;<a style="margin: 0px; padding: 0px; text-decoration: none; color: #1fa2e1;" href="http://www.dotnetblogengine.net/" target="_blank">BlogEngine.net</a>&nbsp;platform using ASP.Net 4.0. So far I have been pretty happy with the features provided overall, as it was pretty straight forward to set up and there are many themes/widgets.</p>
<p>The issue I had with using the out of the box comments system was seeing about 15 spams comments posted a day. I've seen some of the bigger blogs use Facebook Comments and they had less of the spammy junk, like ESPN in the screenshot above.</p>

<p >Since I couldn't find a pre-canned BlogEngine widget available using Facebook comments, I needed to use&nbsp;<a style="margin: 0px; padding: 0px; text-decoration: none; color: #1fa2e1;" href="https://web.archive.org/web/20161029091305/https://developers.facebook.com/docs/plugins/comments/" target="_blank">Facebook Comments plugin</a>&nbsp;to add this functionality. &nbsp;I was able to insert the following code into PostView.ascx of my theme to implement Facebook comments:</p>

    <fb:comments-count href="<%=Post.PermaLink %>"></fb:comments-count> 
    <%=Resources.labels.comments %>
    <div id="fb-root"></div>
    <script>   
        (function (d, s, id) {
            var js, fjs = d.getElementsByTagName(s)[0];
            if (d.getElementById(id)) return;
            js = d.createElement(s); js.id = id;
            js.src = "//connect.facebook.net/en_GB/all.js#xfbml=1";
            fjs.parentNode.insertBefore(js, fjs);
        } (document, 'script', 'facebook-jssdk'));
    </script>
    <div class="fb-comments" data-href="<%=Post.PermaLink %>" 
    data-width="470" data-num-posts="10"></div>

<p >Since I wanted the comments box to appear only on the post page (and not on the homepage), I wrapped the Facebook markup in if statement below:</p>

<p></p>
<p><strong>UPDATE (in 2020):</strong></p>
<p>I wound up removing the functionality described in this post due the volume of spam posts in Facebook comments.</p>
