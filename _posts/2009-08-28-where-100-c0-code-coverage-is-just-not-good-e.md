---
layout: post
title: Where 100% C0 code coverage is just not good enough
published: true
date: 2009-08-28
tags:
- code
---
<p>I guess you are using rcov too to check that all your code was active in your tests at least once. Well, then, here are some bad news:</p>

<p>rcov code coverage only checks whether or not a line was executed, meaning any code from that source line. And this usually trips on</p>

```ruby
do_something if some_condition?
```

<p>because what happens if some_condition? always fails in your tests? (say, "Rails.env.production?"). A somewhat chatty way to detect such cases is to write it down on multiple lines:</p>

```ruby
if some_condition?
  do_something 
end
```

<p>Due to the heavily dynamic structure of a Ruby application nearly nothing is guaranteed to yield the same results when calling at a different time or in a different context, even code as simple as</p>

```ruby
%w(a b c).sort
```

<p>may yield unexpected - for the naive reader - results. So even if you have 100% code coverage, the case might be that the entire test framework which runs the tests for you behaves differently depending if called from rcov, and then your code might behave different within the framework and without it.</p>

<p>No, I don't strive for a 100% test coverage. I employ TDD any now and then: there are cases where that works just perfectly. I employ a big enough test suite to give me confidence in my app. And I employ black box tests - the real world is where the real app will be running. So testing against that is what gives me confidence in my application.</p>
