---
layout: post
title: ! 'Rails thread threats, and run_later '
published: true
date: 2009-10-18
categories: []
---
<p>We all know that rails has a rocky history regarding threads. Sadly, that seems also include the Rails port of one of my favourite Merb features: <a href="http://github.com/mattmatt/run_later">run_later</a>.</p>

<p>Basically run_later takes a block, turns it into a Proc, and sends it to a worker threads for later execution. That way the request processing is not hindered in any way. Say, you want to send a "you have just signed-up" email to your users: this is a perfect solution: easy to use, lightweight (you don't need extra middle ware), and semi reliable (no fallback when your system breaks down).</p>

<p>However, I ran into few problems using mattmatt's solution in, at least, development mode:</p>

<ul>
<li>the app regularly ran into class (un)loading issues, and</li>
<li>Rails' mysql adapter apparently didn't disposed of used database connections, refusing new connections</li>
</ul>
<p>While all of the above can be explained as some of the quirks of the development environment it didn't increase the "trust level" into that solution (and I have to point out here, that markmark's code looks pretty good to me, and that these problems more likely arise from the somewhat idiosyncratic behaviour of Rails towards threads)</p>

<p>That made me thinking: why use threads in the first place? After all, what we need is a defined point during request processing that gives us a handle to yield of some piece of code, and which occurs after the request's response has been sent back to the client. And, yes, thanks to metal, I found one.</p>

<p>So <a href="http://github.com/pboy/threadless">here</a> is a solution. It is not as feature complete as markmark's, tests are still missing, and it blocks the server process until the run_later block is finished. (But you have more than one server process running, haven't you?)</p>

<p>Someone out there wants to help move that into a regular plugin?</p>
