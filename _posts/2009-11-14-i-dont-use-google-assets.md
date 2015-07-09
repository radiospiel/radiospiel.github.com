---
layout: post
title: I) don't use Google assets
published: true
date: 2009-11-14
categories:
- ajax
- google assets
- google hosted assets
- performance
---
<h1>0x2b - (I) don't use Google assets</h1>

<p>Recently people are moving to google hosted Javascript APIs. Which sounds good, at the
first sight, because</p>

<ul>
<li>your client's browser connect not only to your site, but to google as well to
fetch needed assets, which might result in more parallel connections and a
faster download time, and</li>
<li>your client's browser might already have the google hosted assets in its cache.</li>
</ul>
<p>However, I recently stopped doing this. Why?</p>

<ol>
<li><p><em>Performance:</em> Well, for once the performance of google hosted assets seemed not extremely good; a
response time of ~400ms seems quite normal, at least for me here in Germany. And that
is a response time I outperform myself with ease.</p></li>
<li><p><em>Parallelity:</em> While the browser happily downloads Google's versions in parallel, this is
easy to be done myself. Set up a "parallel" host - same source directory, different hostname -
and use that as an asset host.</p></li>
<li><p><em>Custom assets:</em> When I use, say, jquery, I usually not only use the basic thing, but a bunch
of different plugins, and then of course I have my very own application javascript as well. If
I let google deliver the base jquery plugin, then I still have to deliver the other stuff myself.
Which means all of those Parallelity and Performane topics are back on my table.</p></li>
<li><p><em>Works offline</em>: yes, it does. Comes in handy when working on a train.</p></li>
<li><p><em>Privacy issues:</em> yes, google hosted assets do set a cookie. One can only speculate the reason
behind this; but unless my customers agree into using Google Analytics I don't want them pestered
with uninvited cookies.</p></li>
</ol>
<p><em>My solution: asset bundling:</em> Instead I bundle the assets myself. In the case of Javascript and CSS files this is easily done, by just concatenating these. I then embed my CSS files into the Javascript files (my web sites usually only work with javascript enabled anyways). This results in one
hand tailored asset file, ready to be cached and delivered by myself.</p>
