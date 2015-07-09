---
layout: post
title: What to expect when you're expecting
published: true
date: 2012-10-10
categories: []
---
<p>It is a while since I gave this presentation about the
<a href="https://github.com/radiospiel/expectation">expectation</a> ruby gem.
In short: the expectation gem lets you add assertions directly into the code. This will help
you catch a specific class of coding errors <strong>much</strong> faster &ndash; those where different components
of an application are developed separately and forget about the finer details of their respective interfaces.</p>

<p>Expectations look like this:</p>

<div class="CodeRay">
  <div class="code"><pre>def function(a, b, options = {})
  expect! a =&gt; /^http:/, b =&gt; [Integer, Float], 
    options =&gt; {
      :foo =&gt; String,
      :bar =&gt; [ Array, nil ]
    }
end</pre></div>
</div>


<p>If you missed the presentation, you may revisit it <a href="https://github.com/radiospiel/expectation">here.</a></p>
