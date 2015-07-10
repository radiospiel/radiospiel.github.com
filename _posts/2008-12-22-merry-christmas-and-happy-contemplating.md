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

```
puts false or 1
 puts false || 1
 puts false | 1

</li>
<li>
<p>What is the output of the following?</p>

```
puts 1 and false
 puts 1 &amp;&amp; false

</li>
<li>
<p>What is the result of</p>

```
puts File.dirname("foo.bar")
 puts File.dirname("/foo.bar")
 puts File.dirname("/foo.bar/")

</li>
<li>
<p>How often does this script wish you a merry X-Mas when run via '<i>ruby </i>'?</p>

```
puts "Merry XMas!"
 require __FILE__
 require File.dirname(__FILE__) + "/" + File.basename(__FILE__)

</li>
<li>
<p>And this script?</p>

```
puts "Merry XMas!"
 require __FILE__
 require File.dirname(__FILE__) + "/" + File.basename(__FILE__)

 Dir.mkdir "a" rescue nil
 require File.dirname(__FILE__) + "/a/../" + File.basename(__FILE__)

</li>
<li>
<p>And when running from...</p>

<p> ...within a Rails application via '<i>ruby script/runner</i>'?</p>
</li>
</ul>
