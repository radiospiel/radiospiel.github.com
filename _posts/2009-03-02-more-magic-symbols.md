---
layout: post
title: More magic symbols
published: true
date: 2009-03-02
categories:
- improvement
- rails
- ruby
- symbol
---
<p>With just that small addition:</p>

<div class="CodeRay">
  <div class="code"><pre>class Symbol
  alias_method :old_eeq, :&quot;===&quot; 

  def ===(obj)
    (obj.respond_to?(self) &amp;&amp; obj.send(self)) ||
    old_eeq(obj)
  end
end</pre></div>
</div>


<p>you can do things like</p>

<div class="CodeRay">
  <div class="code"><pre>case current_user
when :admin?    then &quot;admin&quot;
when :regular?  then &quot;something_for_regulars&quot;
when :guest?   then &quot;do_guest_stuuff&quot;
else &quot;huh&quot;
end</pre></div>
</div>


<p>Isn't that cool much more readable?</p>
