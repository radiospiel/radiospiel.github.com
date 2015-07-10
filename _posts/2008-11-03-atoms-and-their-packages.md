---
layout: post
title: Atoms and their packages
published: true
date: 2008-11-03
categories:
- rails
- ruby
---
<p>Every now and then I come across someone that doesn't really know about Ruby <a href="http://www.ruby-doc.org/core/classes/Symbol.html">symbols</a>. That comes as no surprise, because most programming languages don't have something like that as a built-in type. And Rails' HashWithIndifferentAccess (this is the class of the params hash that you deal with during actions) certainly doesn't improve the situation: novices might think that whether or not to use a symbol is a random decision.</p>

<p>And symbols are a strange thing at first sight: you can turn each symbol into a string, that appears to have the same value, string-wise. So where is the difference between :symbol and "symbol"? Why do they exist?</p>

<p>A specifc symbol to always refers to the same internal ruby object. Check the following:</p>

```
:a.object_id == :a.object_id


<p>will always be true, while the situation for Strings is different.</p>

```
"a".object_id != "a".object_id


<p>To ensure that symbols always refer to the same internal object an implementation must guarantee the following:</p>

<ul>
<li>a symbol cannot be changed (while a String object can)</li>
<li>a symbol, once created, must be remembered by the current running process (because otherwise a newly created object could end up with just that object_id, or an identical symbol with a different one)</li>
</ul>
<blockquote class="posterous_short_quote">
  (Another important, but not strictly required property of a symbol is that it cannot be empty, i.e. _"".to_sym__ raises an error
</blockquote>


<h2>Now why is that important?</h2>

<p>Well, for one thing it is really fast to compare two symbols for equality. As symbols with an equal value always have the same <em>object_id</em> it is suitable just to compare the object_ids, which being an integer comparison is really really fast. In contrast a string comparison might have to take into account each character of the string - so comparing strings is wayyyy slower, especially with long strings.</p>

<blockquote class="posterous_short_quote">
  Keep in mind that you cannot order symbols. While "b" is greater than "a" the 
  same thing  is neither true nor false between :b and :a, it is just not
  defined.
</blockquote>


<p>But even more important is that <em>symbols are perfectly suitable as hash keys.</em> Please remember: an exemplary Hash implementation turns all key values through a "hash function" which results in a relatively small integer value. It then stores <a href="http://www.google.com/search?q=+key%2C+value+" target="_blank"> key, value </a> pairs in an array at exactly that offset. To be able to deal with values at that offset with a different key (which is called a "conflict") it might actually store it in a list-like structure. This allows for O(1) characteristics even for large datasets. The efficiency of a specific implementation depends mainly on the speed (obviously) and the quality of the hash function. "Quality" in this context means to find a hash function that produces the least number of conflicts on a given dataset.</p>

<p>Now really good hash functions for evenly distributed numbers are easy to find: just divide the number by a prime, and use the remainder. That results in an equally evenly distributed result between 0 and . At the same time this value is fast to calculate. To do the same thing with a string is more work: you would usually take into account a relevant number of characters and the string length, turn that somehow into a number, and after that use the prime method to get the hash value.</p>

<p>Therefore a symbol is suited perfectly as a hash key value - as would be an integer, of course, but in contrast to a String. This is important for the Ruby interpreter. Why?</p>

<h2>From data to code and back</h2>

<p>Just consider what constitutes a ruby object: some data in instance variables and some methods to work, that is. When you call a method on an object, the Ruby interpreter has to somehow find the specific piece of code that "is" the method and runs that. Each class or module internally stores a hash mapping method names to method definitions (i.e. the actual code to run). As Ruby defines method names as Symbols the lookup into those hashes works really fast, and much faster as a String based implementation would work. (And still Ruby has a substellar speed, but Ruby 2.0 hopefully changes that). And as Ruby has to use symbols internally anyways, thankfully it offers you, the programmer, the same tools for really speedy code.</p>

<p>This explains why compiled languages don't have Symbols built in the language in a similar way: there is just no need for it. The mapping between the name of a function or method and the actual code happens at compile or linkage time, so there is no need for something built into the language. However, as some applications might still need similar characteristics for some of their data, you find many implementations for it. Even the Windows API defines methods to manage symbols which it calls "Atoms".</p>

<p>For your every day Ruby business keep in mind that creating a Symbol is costly, because it involves looking up the symbol value in a global table of already defined symbols. Using a symbol, on the other hand, is pure speed. Use symbols when they do not change (for example because they are stated in the source code) and you don't need greater-than or less-than semantics. Don't use them dynamically, unless you know what you are doing.</p>

<h2>Railsy</h2>

<p>Lets get railsy for a moment: when using symbols as hash keys is so much better than using strings, why is it that the params hash in controllers uses String keys instead?</p>

<p>Actually this is the right thing to do. Remember that symbols will never be removed from the system. If the params hash would use symbols for its keys Rails had to translate all key entries in queries into their respective symbol representation. This would allow an attacker to fill the running processes memory just by sending requests with randomly changing parameter  names.</p>

<h2>Read on... and listen</h2>

<p>Here is an excellent article on Symbols, in more depth than what I write here: <a href="http://www.ruby-mine.de/2007/3/3/13-wege-um-ein-ruby-symbol-zu-betrachten">13 different views on Ruby Symbols</a> (in german).</p>

<p>And then this article's title is a homage to an artist I really admire: <a href="http://www.atomandhispackage.com/">Atom and His Package</a>. You are fun! Even more than night of pure Ruby coding!</p>
