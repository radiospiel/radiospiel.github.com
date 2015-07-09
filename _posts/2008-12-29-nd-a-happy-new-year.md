---
layout: post
title: nd a Happy New Year
published: true
date: 2008-12-29
categories:
- ruby
---
<p>for all of you. Before you enter the New Years Day frenzy, here are the results of last week's puzzler. (Haven't been a big secret anyways: every one with a Ruby interpreter could know in the meantime. Yes, that means you!)</p>

<blockquote class="posterous_short_quote">What is the output of the following?</blockquote>




<div class="CodeRay">
  <div class="code"><pre>~ =&gt; irb
&gt;=&gt; puts false or 1
false
=&gt; 1
&gt;=&gt; puts false || 1
1
=&gt; nil
&gt;=&gt; puts false | 1
true
=&gt; nil</pre></div>
</div>



<p>You see, <em>or</em> binds weaker than everything else, more or less. Consequentyl, <em>"puts false or 1"</em> prints false, but because puts returns nil the value of the expression would be 1.</p>

<blockquote class="posterous_short_quote">What is the output of the following?</blockquote>


<p>For similar reasons - "and" binds pretty weak as well:</p>

<div class="CodeRay">
  <div class="code"><pre>~ =&gt; irb
&gt;=&gt; puts 1 and false
1
=&gt; nil
&gt;=&gt; puts 1 &amp;&amp; false
false
=&gt; nil
&gt;=&gt;</pre></div>
</div>





<blockquote class="posterous_short_quote">What is the result of</blockquote>




<div class="CodeRay">
  <div class="code"><pre>puts File.dirname(&quot;foo.bar&quot;)     # &quot;.&quot;
puts File.dirname(&quot;/foo.bar&quot;)   # &quot;/&quot;
puts File.dirname(&quot;/foo.bar/&quot;)  # &quot;/&quot;</pre></div>
</div>



<p>Would you have expected that?</p>

<blockquote class="posterous_short_quote">How many times...</blockquote>


<p></p>

<p>this script file wishes you a merry X-Mas three times:</p>

<div class="CodeRay">
  <div class="code"><pre>puts &quot;Merry XMas!&quot;
require __FILE__
require File.dirname(__FILE__) + &quot;/&quot; + File.basename(__FILE__)</pre></div>
</div>



<p>Even though "require" loads a file only once, it determines whether or not a file has already been loaded by the parameter to <i>rquire</i>. So "a.rb" and "./a.rb" are differenet files with respect to "require" - even though both filenames refer to the same file, as we humans are well aware. That means, if one of your source files must not be loaded more than once - for whatever reason - you must do something like</p>

<div class="CodeRay">
  <div class="code"><pre>unless defined?(THIS_FILE_ALREADY_LOADED)
THIS_FILE_ALREADY_LOADED=true
  ...
end</pre></div>
</div>



<p></p>

<blockquote class="posterous_short_quote">And how many times does this script?</blockquote>


<p></p>

<div class="CodeRay">
  <div class="code"><pre>puts &quot;Merry XMas!&quot;
require __FILE__
require File.dirname(__FILE__) + &quot;/&quot; + File.basename(__FILE__)

Dir.mkdir &quot;a&quot; rescue nil
require File.dirname(__FILE__) + &quot;/a/../&quot; + File.basename(__FILE__)</pre></div>
</div>



<p>This script will at some point crash with a "no such file to load" exception. When require loads a file, the <strong>FILE</strong> value will be the parameter passed to require, and this one gets prepended "/a/../" in each turn. Which results in growing filenames, that still refer to the same file. But at some point the operating system will choke and refuse to resolve a filename like this, because the OSes imüose a limit on filename length and a limit on directory references in a filename.</p>

<blockquote>And when running via '<i>ruby script/runner</i>'?</blockquote>


<p>script/runner apparently doesn'lt load the file via require. That results in the <strong>FILE</strong> value set to "(eval)", which usually doesn't refer to an existing file. Remind yourself, that using the <strong>FILE</strong> value is not always safe, as you can see here.</p>

<blockquote class="posterous_short_quote">
A Happy New Year to all of you!
</blockquote>