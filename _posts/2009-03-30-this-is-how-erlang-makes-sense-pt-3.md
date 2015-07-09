---
layout: post
title: This is how Erlang makes sense, pt. 3
published: true
date: 2009-03-30
categories:
- Erlang
---
<p>This is the third and final part of a series about Erlang.</p>

<ul>
<li>Part 1: <a href="/0x19-why-erlang-makes-sense-pt-1/">Processes</a>
</li>
<li>Part 2: <a href="/0x1a-why-erlang-makes-sense-pt-2/">Messages and Pattern matching</a>
</li>
<li>Part 3: <a href="/0x1b-why-erlang-makes-sense-pt-3/">Lists, Strings, Binaries, Tuples</a>
</li>
</ul>
<blockquote class="posterous_short_quote">
Erlang/OTP didn't come out of thin air. Quite the opposite - seldom you see a language and a platform where the implementation mirrors the intention that closely. 
</blockquote>


<h2>Lists, Strings, Binaries, Tuples: what is what and why?</h2>

<p>A tuple is one strange creature: you cannot append to a tuple, you cannot loop through a tuple - all these things you can do with lists. So why do they exist in the first place?</p>

<p>There is something good about tuples: tuples - being of a fixed size - can be implemented in a super-efficient manner, being memory-efficient and providing fixed-time random access at the same time. Lists, on the other hand, are neither of both. For efficient encoding, decoding and parsing of messages that need structured data in some way you should always use tuples and nothing else.</p>

<p>So then why using lists in so many other places? Well, for most of the issues you don't need fixed-time random access, but a structure, which can be enumerated and that can grow quite fast. And yes, this is your list. And finally, asking why a language stemming from a LISP heritage would have lists is like asking why the milkman is selling this white stuff :)</p>

<p>But even though you can do many things without fixed-time random access, there is still an important area where you just cannot do that efficiently, and this is string processing. Erlang implements strings as a lists of bytes (or byte blocks), and this cannot be done efficiently: to find out whether a pathname ends in, say, ".html" the platform has to scan to the end of the list and to look and see just then. Other implementations have to do the same, but they are faster in finding the end: an  implementation with ASCIIZ-strings (i.e. C-style) looks for the first NUL-byte in the data - something that todays and even yesterdays CPUs support out-of-the-box, and PASCAL-type strings (i.e. length byte(s) + data) just add the length offset to the start address of the memory buffer.</p>

<h2>Where are my objects?</h2>

<p>The guys inventing Erlang/OTP wanted one feature, which is "live updates". That means you can just update the erlang code of some part, and all new processes that need that piece of code start using the updated code right away. As a Rails developer you feel reminded of Rails' development mode; but this time it is done in the language and is done right. What sounds like magic is, in fact, pure technology. Unices do the same all the time with dynamically linked applications: when you deploy a new version of, say, libc on your system all new processes start to use that version; while all the old processes still run with the old version of the library. No reboot required. (By the way, Windows doesn't do that: a Windows system <b>locks</b> all .DLLs in use and prevents them from being replaced. Hence the need for a reboot when updating.)</p>

<p>Now you might ask: what happens to the data? When do you migrate old-version data to new-version data? This is something that you have to do yourself, if you need it. Better you don't have incompatible changes to your data - Erlang doesn't help you with that. But the same is true for the code itself. A change from, say,</p>

<div class="CodeRay">
  <div class="code"><pre>fun(Opt) 
case Opt of
stop -=&gt; do_stop()
end.</pre></div>
</div>



<p>to</p>

<div class="CodeRay">
  <div class="code"><pre>fun(Opt) 
case Opt of
finish -=&gt; do_stop()
end.</pre></div>
</div>



<p>could break the application. Live updates have to rely on code that is compatible to previous versions in terms of the API and of Data.</p>

<p>What has this to do with Objects? In a narrow sense this is totally unrelated. However, objects in an OOP implementation no longer expose the public interface only. To allow for inheritance the objects must expose its "protected" interfaces (i.e. every method that can be called from a related object) and all methods that can potentially be overridden in a derived class. These massively blows up the size of the application interface. Which makes an incompatible change more likely. Which breaks live updates. Case closed. (And hey: while OOP is nice to have, it is never strictly needed, right?)</p>

<h2>Final Disclaimer</h2>

<p>Opposed to Erlang/OTP this article does come out of thin air, sort of. I am no authoritative source regarding the history of Erlang/OTP, and as I never intended to be one. I do, however, like the intellectual exercise to ask not only the how, but the why, the when, and by whom too.</p>
