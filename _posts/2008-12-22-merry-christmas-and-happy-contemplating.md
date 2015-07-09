---
layout: post
title: Merry Christmas and Happy Contemplating...
published: true
date: 2008-12-22
categories:
- ruby
---
<p>...some piece of ruby code that might not behave as you think (depending on your thinking, of course.). Do you know the results?</p>

<ul>
<li>
<p>What is the output of the following?</p>

<div class="CodeRay">
  <div class="code"><pre>puts false or 1
 puts false || 1
 puts false | 1</pre></div>
</div>

</li>
<li>
<p>What is the output of the following?</p>

<div class="CodeRay">
  <div class="code"><pre>puts 1 and false
 puts 1 &amp;&amp; false</pre></div>
</div>

</li>
<li>
<p>What is the result of</p>

<div class="CodeRay">
  <div class="code"><pre>puts File.dirname(&quot;foo.bar&quot;)
 puts File.dirname(&quot;/foo.bar&quot;)
 puts File.dirname(&quot;/foo.bar/&quot;)</pre></div>
</div>

</li>
<li>
<p>How often does this script wish you a merry X-Mas when run via '<i>ruby </i>'?</p>

<div class="CodeRay">
  <div class="code"><pre>puts &quot;Merry XMas!&quot;
 require __FILE__
 require File.dirname(__FILE__) + &quot;/&quot; + File.basename(__FILE__)</pre></div>
</div>

</li>
<li>
<p>And this script?</p>

<div class="CodeRay">
  <div class="code"><pre>puts &quot;Merry XMas!&quot;
 require __FILE__
 require File.dirname(__FILE__) + &quot;/&quot; + File.basename(__FILE__)

 Dir.mkdir &quot;a&quot; rescue nil
 require File.dirname(__FILE__) + &quot;/a/../&quot; + File.basename(__FILE__)</pre></div>
</div>

</li>
<li>
<p>And when running from...</p>

<p> ...within a Rails application via '<i>ruby script/runner</i>'?</p>
</li>
</ul>
