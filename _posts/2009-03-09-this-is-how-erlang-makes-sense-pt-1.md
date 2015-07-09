---
layout: post
title: This is how Erlang makes sense, pt 1
published: true
date: 2009-03-09
categories:
- Erlang
---
<p>This is part 1 of a series about Erlang. Parts 2 and 3 will be published in the next weeks here.</p>

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


<p>If you remember the history: Erlang/OTP is a solution to one problem: to handle a really large number of telephone connections efficiently and reliably. We are talking metadata management here (phone billing, routing etc.): in that time available hardware was hardly able to audiostream tens of thousands of connections simultaneously.</p>

<h3>Processes</h3>

<p>Simply speaking, a reliable system can only be built off simple components; because all humans can only deal with a limited complexity every other system will have some hidden bugs or otherwise unintended behaviour. Now the simplest way to implement a server system which is capable of dealing with parallel requests is in multiple processes. (If you don't believe that just go compare a simple inetd-based HTTP server with a threaded or an event-based solution.) On the other hand, it is not feasible to spawn tens or even hundreds of thousands of processes in a regular OS environment: any OS that deals with real multiprocessing must be prepared to do so with processes, that, request access to the same resources, use the same CPU registers, the same real memory, and so on. To make that work the OS takes some precautions: swapping away CPU and stack state, etc, which needs some resources. This is the reason why both spawning a process and scheduling processes take up quite some time (well, relatively spoken: we a re speaking a few hundred microseconds or even less.) You can get over with that if your processes do some "real" processing. In a case where you just move around a few byte of data you don't want to waste your hardware on process management.</p>

<p>What is needed is a less ressource consuming process switching mechanism. Now Windows and some Unices came up with threads at that time (not Linux, though: Linux' thread implementation was notoriously bad at the time. It was just a thin layer over Unix' core process functionality, and had all the backdraws of those, performancewise) and fibers/microthreads etc shortly after. What is common to those techniques is that process wide data synchronisation is left to the programmer. Which results in complex code which ends up with probably errorneous software which is not what we would want.</p>

<p>Why is that so hard? Take, for example, a list of users in a webserver, which is stored in an in-memory array. Each user might register and end up in exactly that array. So, if the system deals with two users at a time that both want to register it has to store them in there. To make that safe both threads have to "talk to each other" to make sure they don't get into each others way: "hey, now it is my turn to add a user; everybody else please back off."</p>

<p>If, however, someone would guarantee that this can never happen then there was no need to do that. Enters Erlang: it is literally impossible to change a value in Erlang. No two processes can write at a value because there are no writers, only creators. SO it is save to pass around Erlang values to all Erlang processes. They are read only, no synchronization is needed, an entire class of race conditions just disappears in a puff: no two writers at the same time may exist, and all of that without locking.</p>

<p>Please note: an erlang process and an OS process are quite different things. An erlang process might be implemented by an OS process, but quite likely it is implemented by a green thread solution instead.</p>
