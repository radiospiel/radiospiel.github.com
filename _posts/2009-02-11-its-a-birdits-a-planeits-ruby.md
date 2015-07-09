---
layout: post
title: It's a Bird…It's a Plane…It's Ruby!
published: true
date: 2009-02-11
categories:
- Erlang
- pattern matching
- ruby
---
<p>Ever done Erlang? Now I wondered how flexible ruby would be and if an erlang style pattern matcher can be done in ruby. As it turns out, I came pretty close:</p>

<p>You might recognize this:</p>

<div class="CodeRay">
  <div class="code"><pre>m = matching! [ 1, [ :a, 2 ], 3 ] do
  on [ a ]                do puts [ &quot;1&quot;, a ].inspect end
  on [ a, _, a ]          do puts [ &quot;2&quot;, a ].inspect end  
  on [ a, [ :a, 3 ], z ]  do puts [ &quot;3&quot;, a, z ].inspect end  
  on [ a, [ b, 2 ], z ]   do puts [ &quot;4&quot;, a, b, z ].inspect; self.z = :z end
  on [ a, [ :a, 2 ] , z ] do puts [ &quot;5&quot;, a, z ].inspect end
end</pre></div>
</div>


<p>Even if not, I bet you understand what it is supposed to do; if you don't: the output is <i><a href="http://www.google.com/search?q=%224%22%2C+1%2C+%3Aa%2C+3" target="_blank">"4", 1, :a, 3</a></i>. How this all works? Easy: start off a BlankSlate, add some method_missing magic, off you go! All in <a href="http://pastie.org/386086">under 100 lines</a>.</p>

<blockquote class="posterous_short_quote">Note to wordpress: This sucks, really sucks. Limited to a numbr of 25 or so templates sucks, and then each comes with its own limitations. I guess I have to go somewhere else...
</blockquote>
