---
layout: post
title: ! 'public privacy protection: ruby privacy flags explained'
published: true
date: 2009-02-09
tags:
- ruby
---
<p>...or: There is a difference between <em>methodname(args)</em> and <em>self.methodname(args)</em>!</p>

<p>The way how ruby deals with private, protected and public messages was a bit unclear to me. So I decided to dig a bit into it and to find out how it is really working. Finally I have <em>irb</em> installed: so lets go and explore.</p>

<p>This is the source that I used for my tests:</p>

```
#
# Explore how private, protected, and public works
#
class A
  def public_method; true; end

  protected

  def protected_method; true; end

  private

  def private_method; true; end
end

class B < A; end
class C; end

$target = A.new

module Call
  def call(sym)
    send("call_#{sym}") &amp;&amp; "Called #{sym} method" rescue "Could not call #{sym} method: #{$!}"
  end

  def call_private;           $target.private_method; end
  def call_checked_private;   $target == self ? private_method : $target.private_method; end
  def call_self_private;      $target == self ? self.private_method : $target.private_method; end
  def call_protected;         $target.protected_method; end
  def call_public;            $target.public_method; end
end

a = A.new
b = B.new
c = C.new

[ [ "$target", $target ], [ "a", a ], [ "b", b ], [ "c", c ] ].each do |name, obj|
  obj.extend Call

  puts "== #{name} =============================================="
  puts "  #{obj.call :private}"
  puts "  #{obj.call :checked_private}" if name == "$target"
  puts "  #{obj.call :self_private}"    if name == "$target"
  puts "  #{obj.call :protected}"
  puts "  #{obj.call :public}"
end


<p>and these are the results</p>

```
== $target ==============================================
  Could not call private method: private method `private_method' called for #<A:0x278a8>
  Called checked_private method
  Could not call self_private method: private method `private_method' called for #<A:0x278a8>
  Called protected method
  Called public method
== a ==============================================
  Could not call private method: private method `private_method' called for #<A:0x278a8>
  Called protected method
  Called public method
== b ==============================================
  Could not call private method: private method `private_method' called for #<A:0x278a8>
  Called protected method
  Called public method
== c ==============================================
  Could not call private method: private method `private_method' called for #<A:0x278a8>
  Could not call protected method: protected method `protected_method' called for #<A:0x278a8>
  Called public method


<p>(You can get that from a <a href="http://pastie.org/376001">pastie</a>) Now, what does it mean?</p>

<ul>
<li>Public methods can (unsurprisingly) be called from anywhere</li>
<li>Protected methods on an object can be called from
that object and from other objects of the same or an derived class</li>
<li>Private methods cannot be called via <strong>.</strong>_, not even
via <strong>self.</strong>! Consequently they can only be called from
the very same object, which for example rules out private attributes.</li>
</ul>
