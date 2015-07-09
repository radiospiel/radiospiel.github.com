---
layout: post
title: url_for speed "issues"
published: true
date: 2008-12-15
categories:
- performance
- rails
- ruby
- url_for
---
<p>Everyone seems to know that building an URL from a hash has some speed issues. The reason for that is quite clear: it looks up the shortest route using routes.rb, no wonder that this would take some time.</p>

<p>But how slow is it really? Well, I ran some tests and compared that against building a tag from a hash, building just a hash, and directly building an URL from a string.</p>

<div class="CodeRay">
  <div class="code"><pre>Benchmark.bm do |x|
  n = 50000

  x.report(&quot;url_for&quot;) do for i in 1..n
    url_for(:controller =&gt; :content, :action =&gt; :list, :id =&gt; i)
  end end

  x.report(&quot;building tag&quot;) do for i in 1..n
    tag(&quot;div&quot;, :controller =&gt; :content, :action =&gt; :list, :id =&gt; i)
  end end

  x.report(&quot;building hash&quot;) do for i in 1..n
    { :controller =&gt; :content, :action =&gt; :list, :id =&gt; i }
  end end

  x.report(&quot;replacing in string&quot;) do for i in 1..n
    &quot;/content/list/&quot;.sub //, i.to_s
  end end

  x.report(&quot;building string&quot;) do for i in 1..n
    &quot;/content/list/#{i}&quot;
  end end
end</pre></div>
</div>


<p>This contains all the steps needed to go from a hash describing controller, action and parameters to a final tag, and, with "replacing in string" and "building string"  benchmarks some of the alternatives as well. Now have a look at my results:</p>

<div class="CodeRay">
  <div class="code"><pre>url_for              11.670000   0.070000  11.740000 ( 12.007942)
building tag          2.160000   0.010000   2.170000 (  2.199764)
building hash         0.050000   0.000000   0.050000 (  0.056826)
replacing in string   0.100000   0.000000   0.100000 (  0.110649)
building string       0.060000   0.000000   0.060000 (  0.067360)</pre></div>
</div>


<p>What do we make of this? For once: url_for is pretty fast. It generates an URL in 0.2 msecs. So unless you are running a website with thousands of hits per second or you create hundreds of URLs of you should be fine. And an astonishingly 20% of the time spent there is actually used by the tag building. And it is not building a temporary hash that is responsible here.</p>

<p>And any of the viable alternatives is sooo much faster.</p>
