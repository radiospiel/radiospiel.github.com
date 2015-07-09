---
layout: post
title: There are no nested functions in ruby!
published: true
date: 2009-05-31
categories: []
---
<p>Long time no read, I know. Well, I have been <a href="http://maps.google.com/maps?f=q&amp;source=s_q&amp;hl=en&amp;geocode=&amp;q=Japan&amp;sll=36.421282,139.059448&amp;sspn=2.15252,4.641724&amp;ie=UTF8&amp;ll=36.204824,138.252924&amp;spn=17.233619,37.133789&amp;t=h&amp;z=5">away</a>.</p>

<p>Anyways, while I am not back yet, I stumbled across something that made me wonder: consider this:</p>

<div class="CodeRay">
  <div class="code"><pre>class X
  def self.a
    def self.b; &quot;b&quot;; end
    b()
  end

  def self.x
    b
  end
end</pre></div>
</div>


<p>The ruby feature which allows you to define a function within another function is relatively new to me. Since I found out about it I used it to split a function into several parts but not to publish the parts in any namespace accessible from the outside, i.e. the parts should be accessible only from within the method.</p>

<p>Turns out that I was wrong. Apparently a "not-really inner function" is defined at whatever outer level exists (hence the need for the "self" part in "def self.b; ...; end" in the example above). The method "b" is defined on the X class object, i.e. as if was written</p>

<div class="CodeRay">
  <div class="code"><pre>class X
  def self.b; &quot;b&quot;; end
  def self.a; b(); end
end</pre></div>
</div>


<p>Seems I will stop using that idiom.</p>
