---
layout: post
title: Your daily class variables and constants surprise
published: true
date: 2009-07-20
categories:
- class variable
- constant
- ruby
- scope
---
<p>It is not chrismas, hence no quiz, but this was strangely surprising:</p>

<div class="CodeRay">
  <div class="code"><pre>class A
  @@t = &quot;A::@@t&quot;
  T=&quot;A::T&quot;

  def self.s1; @@t; end
  def self.s2; T; end
end

class B &lt; A
  def self.s1; @@t; end
  def self.s2; T; end
end

class C &lt; A
  @@t = &quot;C::@@t&quot;
  T=&quot;C::T&quot;
  def self.s1; @@t; end
  def self.s2; T; end
end

[ A.s1, A.s2, B.s1, B.s2, C.s1, C.s2 ]</pre></div>
</div>


<p>gives you</p>

<div class="CodeRay">
  <div class="code"><pre>[&quot;C::@@t&quot;, &quot;A::T&quot;, &quot;C::@@t&quot;, &quot;A::T&quot;, &quot;C::@@t&quot;, &quot;C::T&quot;]</pre></div>
</div>
