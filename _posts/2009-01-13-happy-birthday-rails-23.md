---
layout: post
title: Happy birthday, Rails 2.3
published: true
date: 2009-01-13
categories: []
---
<p>It is a bit early, but still. Rails is going to turn 4! Candles, Fireworks, and everything!</p>

<p>And yes: Rails 2.3 is somewhat <a href="http://weblog.rubyonrails.org/2008/12/5/this-week-in-edge-rails">announced</a>. And the fanbase rolls on the floor out of sheer excitement: rails now has :having! Whoa! Big news!</p>

> Justin: The `:having` feature is really nice because my :group options are really ugly with the HAVING in them.


Hey Justin, that is exactly the point: Rails is <del datetime="00">now</del> <del datetime="00">nearly</del> <del datetime="00">a bit</del> still not at all feature complete with respect to SQL. And it will never be. Having Rails DB agnostic is a pointless goal because

- your app wouldn't be DB agnostic anyways (or compare the quite different syntax for `NOW()`, String concatenation, `RAND()`, just to name a few)
- I want subqueries
- I want foreign keys
- I want views

<p>Rails will never be 100% SQL-feature complete, unless, of course, it recreates SQL completely. And where is the point in that? If I was Rails, I would drop <del datetime="00">all</del> most of the higl level db abstraction shit, or at least fix the stuff that is in Rails already (e.g. Merging named scopes might create illegal SQL code). And instead concentrate on real improvements.</p>

<p>Not that they were missing: this is my fav news:</p>


> <strong>Rack Integration:</strong>
>
> The tighter integration of Rails with Rack continues. This week saw the death of the venerable CGI processor within Rails, as well as the use of Rack to handle FCGI. There was also some refactoring down in the Rails tests to make them play nicer with Rack.


<p>and, of course, the announced <del datetime="00">merger</del> integration of Merb into Rails. Can't wait to see that happen!</p>
