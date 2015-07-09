---
layout: post
title: This is how Erlang makes sense, pt. 2
published: true
date: 2009-03-16
categories:
- Erlang
---
<p>This is part 2 of a series about Erlang. The final part 3 will be published next week here.</p>

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


<h3>Messages</h3>

<p>But how do Erlang applications share a global state then? One way to synchronize access to a ressource is by a message queue, where each message helds the type of requested access. And as Erlang/OTP was about an efficient parallel server infrastructure, this is easy to add on - after all, all servers implement some kind of message queue; even though some don't know about that because they don't care for the underlying network infrastructure.</p>

<p>So to have some global state you start an Erlang process dedicated to manage exactly that state information and nothing else and have it wait for and evaluate messages and send back answers.</p>

<p>Note: This is only about <b>global</b> states. To adjust a state over time you usually do some tail recursion like this:</p>

<div class="CodeRay">
  <div class="code"><pre>foo(State, In) -=&gt;
case In of
  done -=&gt; done;
  _ -=&gt; foo(calculateNewState(State, In))
end.</pre></div>
</div>


<h3>Efficiency: Pattern matching</h3>

<p>This message passing would add some runtime penalty on the implementation, one would expect. That is, of course, unless everything works really fast.</p>

<p>Erlang was not stupid enough to request a specified fixed length message format (as Windows was, at a time). Instead it allows you to pass around everything which is an erlang object. Being immutable once created it is safe to pass it on as a reference and not as a value, i.e. only as a couple of bytes.</p>

<p>After sending a message the next step involved is understanding it. You usually have erlang patterns like this:</p>

<div class="CodeRay">
  <div class="code"><pre>loop() -=&gt;
  receive
    {cmd, A, B, C} -=&gt; cmd_on(A,B,C), loop();
    stop           -=&gt; stop;
    _              -=&gt; io:format(&quot;Unknown input!~n&quot;), loop(Port)
  end.</pre></div>
</div>


<p>You see, pattern matching <b>must</b> be fast in Erlang, and let me tell you: it usually is, especially when using tuples instead of lists and symbols instead of strings.</p>