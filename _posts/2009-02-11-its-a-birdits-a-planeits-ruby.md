---
layout: post
title: It's a Bird… It's a Plane… It's Ruby!
published: true
date: 2009-02-11
tags:
- ruby
---
<p>Ever done Erlang? Now I wondered how flexible ruby would be and if an erlang style pattern matcher can be done in ruby. As it turns out, I came pretty close:</p>

<p>You might recognize this:</p>

```ruby
m = matching! [ 1, [ :a, 2 ], 3 ] do
  on [ a ]                do puts [ "1", a ].inspect end
  on [ a, _, a ]          do puts [ "2", a ].inspect end  
  on [ a, [ :a, 3 ], z ]  do puts [ "3", a, z ].inspect end  
  on [ a, [ b, 2 ], z ]   do puts [ "4", a, b, z ].inspect; self.z = :z end
  on [ a, [ :a, 2 ] , z ] do puts [ "5", a, z ].inspect end
end
```

<p>Even if not, I bet you understand what it is supposed to do; if you don't: the output is <i><a href="http://www.google.com/search?q=%224%22%2C+1%2C+%3Aa%2C+3" target="_blank">"4", 1, :a, 3</a></i>. How this all works? Easy: start off a BlankSlate, add some method_missing magic, off you go! All in <a href="http://pastie.org/386086">under 100 lines</a>.</p>
