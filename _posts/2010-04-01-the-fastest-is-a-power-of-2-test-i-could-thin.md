---
layout: post
title: the fastest "is a power of 2" test I could think of
published: true
date: 2010-04-01
categories:
- performance
- ruby
---

<p>Can you beat this?</p>

<div class="CodeRay">
  <div class="code"><pre>def ispow2me2(n)
  n &amp; (n - 1) == 0
end</pre></div>
</div>
