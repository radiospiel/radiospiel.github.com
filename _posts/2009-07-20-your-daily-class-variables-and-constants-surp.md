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

```
class A
  @@t = "A::@@t"
  T="A::T"

  def self.s1; @@t; end
  def self.s2; T; end
end

class B < A
  def self.s1; @@t; end
  def self.s2; T; end
end

class C < A
  @@t = "C::@@t"
  T="C::T"
  def self.s1; @@t; end
  def self.s2; T; end
end

[ A.s1, A.s2, B.s1, B.s2, C.s1, C.s2 ]


<p>gives you</p>

```
["C::@@t", "A::T", "C::@@t", "A::T", "C::@@t", "C::T"]
