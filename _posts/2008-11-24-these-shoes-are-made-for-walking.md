---
layout: post
title: These shoes are made for walking
published: true
date: 2008-11-24
categories:
- object walking
- ruby
---
<p>Wanna go for a stroll? It is getting winter again, at least <a href="http://maps.google.de/maps?q=Berlin&amp;amp;hl=de&amp;amp;ie=UTF8&amp;amp;ll=52.494278,13.441772&amp;amp;spn=0.148829,0.266762&amp;amp;z=12">in this part of the world</a>, so strolling outside might be a bit, err, cold. Instead I suggest you just walk some ruby objects this time instead?</p>

<p>Walking - in this context - means visiting an object and all connected subobjects, and visiting them only once. So to walk an object you would just get the object, collect all subobjects (read: instance variables), and any other connected object you know about: for Arrays these are the array elements, for hashes all keys and values. And then we use some piece of storage that remembers which object we already went to, to visit each one only once, and at the same time to prevent endless loops (which would otherwise be easy with objects that store "both ends of a link", i.e.</p>

```
class Node
  attr :parent, :children
  def initialize(parent, children=[])
    self.parent = parent
    self.children = children.each { |c| c.parent = self }
  end
end


<p>Well, anyways, here is the <a href="http://pastie.org/314725">code</a>.</p>

<h2>What is this good for?</h2>

<p>Again you can do a number of different things, some even remotely interesting :) Say you want to determine the 'mtime' (in Unix lingo this is the time of the last <strong>m</strong>odification) of an object, and say you have mtime_base methods on all supporting classes, that return the base modification time for that object only:</p>

```
class Object
  def mtime_base; nil; end # dummy implementation for nonsupporting objects
end

class ActiveRecord::Base
  def mtime_base; self.updated_at rescue nil; end
end


<p>then this could be your code</p>

```
class Object
  def mtime
    epoch = Time.at(0)
    m = epoch
    walk { |obj| m = [ obj.mtime_base || epoch, m ].max }
    return m  
  end
end


<p>Now writing this I realize that walk could behave more inject-y, may be like this:</p>

```
def Object.walk(val=nil, &amp;block)
  walk_object(Set.new) do |obj| val = yield val, obj; end
  val
end


<p>Then this last piece of code would simplify to</p>

```
def mtime
  epoch = Time.at(0)
  walk(epoch) { |m, obj| [ obj.mtime_base || epoch, m ].max }
end


<p>And, yes, building a temporary array for each object is bad, performancewise. But this exercise is left to the reader.</p>
