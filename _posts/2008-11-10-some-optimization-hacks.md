---
layout: post
title: Some optimization hacks...
published: true
date: 2008-11-10
categories:
- performance
- rails
- ruby
---
<p><a href="http://mediapeers.com">We</a> are working on a somewhat larger Rails application, and one of the actions renders a table with up to 9.000 table elements. Now, somewhere when rendering that table we use a line like</p>

<div class="CodeRay">
  <div class="code"><pre>some_array.collect(&amp;:some_method)</pre></div>
</div>


<p>and this line gets called several thousand times. As you can imagine this turns out to be not super-fast. But to our astounishment we found that more of 20% of the processing time was used up by creating several 1000 Proc objects!</p>

<h2>Caching Proc</h2>

<p>That led to a first optimization idea: caching those Proc object inside the Symbols.
As you might know, the <em>&amp;:&lt;some_method_name&gt;</em> calls the <em>to_proc</em> method
of the <em>:some_method_name</em> Symbol. Rails come with
an <a href="http://api.rubyonrails.org/classes/Symbol.html">implementation</a> which looks like this:</p>

<div class="CodeRay">
  <div class="code"><pre>def to_proc
  Proc.new { |*args| args.shift.__send__(self, *args) }
end</pre></div>
</div>


<p>As these Proc objects always represent the same block for each individual Symbol they should be cacheable in the symbol itself:</p>

<div class="CodeRay">
  <div class="code"><pre>def to_proc
  @to_proc ||= Proc.new { |*args| args.shift.__send__(self, *args) }
end</pre></div>
</div>


<p>This little change sped up rendering by 25%!</p>

<h2>Garbage uncollecting</h2>

<p>We noticed some strange hickups during rendering of that very same action. While one specific partial usually rendered in 5 milliseconds on average, but a few times it took about 200 milliseconds - without any dramatic change in the data to be rendered. After some time we came up with the idea that this is the responsibility of Ruby's garbage collector.</p>

<p>Which makes sense: we create thousends of temporary objects, that become eligible for garbage collection pretty soon. And as you cannot finetune when Ruby's garbage collector kicks in without recompiling the ruby interpreter we tried instead to disable garbage collection while the response gets rendered:</p>

<div class="CodeRay">
  <div class="code"><pre>around_filter do |controller, action|
  GC.disable
  begin
    action.call
  ensure
    GC.enable
    GC.start
  end
end</pre></div>
</div>


<p>This still runs the garbage collector <em>before</em> the response is send back to the
 client. But at least the garbage collector runs only once per request. This gave
 us a 20% speedup at the cost of a somewhat higher memory consumption. Our tests
 showed that the memory usage doesn't grow over time: so garbage collection seems
 still to run fine.</p>

<blockquote class="posterous_medium_quote">
  Note: What I would call proper behaviour of Rails would be to run the garbage 
  collector only _after_ the response is sent back to the client. And I would 
  expect the code above without the _GC.start_ code line to behave just like this. 
  Our tests, however, showed a slowly but continuously rising memory consumption.
</blockquote>


<h2>Is this ready for production?</h2>

<p>While our tests are running perfectly with those hacks, we considered that those code snippets modify the core behaviour of the Rails framework. And because a live system has different access characteristics than a test environment we decided against using those hacks in production mode.</p>

<p>To prevent <em>Symbol#to_proc</em> to be called that often we explicitely write down a block in that one place. And when we evaluated <a href="http://www.rubyenterpriseedition.com/">ruby enterprise</a> we found that its memory allocator deals much better with our memory usage characteristics. With those two changes the performance advantages of our hacks are not so huge anymore. So we are fine for the moment.</p>

<p>But still: would you consider such hacks production safe? How would <em>you</em> test for that?</p>

<h2>Further reading</h2>

<ul>
<li><a href="http://www.ruby-forum.com/topic/161089">Symbol.to_proc is way slower than invoking methods directly</a></li>
<li><a href="http://pragdave.pragprog.com/pragdave/2005/11/symbolto_proc.html">PragDave: Symbol#to_proc</a></li>
</ul>
