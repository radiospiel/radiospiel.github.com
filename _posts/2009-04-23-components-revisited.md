---
layout: post
title: Components, revisited
published: true
date: 2009-04-23
categories:
- 1rad
- components
- distribute
- performance
- rails
- xc2
---
<p>Lately I started looking into components again. What appealed me is that they could provide a uniform interface to parts of an application, rendering and all included. And the way they <del>are</del> were built into Rails they even spoke HTTP right out of the box.</p>

<p>Think about the possibilities! Using components you could move caching out of the application, getting rid of ridicously hard to maintain cache sweepers, and use well-established HTTP caching proxies. Using components you could easily have parts of a page delivered via an AJAX request, speeding up the initial response to the user! Using components you could distribute rendering between different application instances, and actually start using these multiple cores in your server's CPU!</p>

<p>But wait! "Components"?</p>

> I really like what DHH said in the above link: &ldquo;The problem with components is that they&rsquo;ve never actually been in style. We just forgot to tell people that. Our bad, now being rectified.&rdquo; So, for starters, they were never popular.
> 
> The other main reason is that Railers who have implemented components are noticing themselves refactoring away from them. It&rsquo;s becoming clear that, in most circumstances, plugins or helpers or possibly even an engine would serve an application better than components.
> 
> Lastly, they&rsquo;re slow. Blecchhh. That&rsquo;s a show-stopper.

<p>(Shamelessly cited from <a href="http://mysterycoder.blogspot.com/2008/02/rails-components-i-do-not-think-that.html">mysterycoder</a>.)</p>

<p>Well, DHH has some style in saying some things. However, there are some compelling reasons to use components in the first place: whenever you are about to do a dashboard like page. I have never seen some code that does this in a clean and dry way and is efficient at the same time. And with the other goodies that we could throw in (see above)...</p>

<p>But here are some good news: at least the speed issue is not really a speed issue anymore. I tested a do-nearly-nothing application, with only a few before_filters thrown in for amusement. This is the controller portion:</p>

```ruby
def test1; render :layout => false;end
def test2; render :layout => false;end

def innard; render :partial => "innard";end
```


<p>and the html templates are along the lines of</p>

```erb
hi from <%= __FILE__ %>
<%= render :partial => "innard" %>

hi from <%= __FILE__ %>
<%= render_component :action => "innard" %>
```


<p>With this setup I measured using "ab" against a single mongrel serving the pages, and I found the "test1" testcase, i.e. without components, to be delivered in 24 ms. This includes the network overhead; rails logs the response time to be 6 msecs. The render times when using components is 30 msecs (12 msecs on the Rails side only.) While 6ms vs. 12 ms sounds like only half the speed, this is for more or less empty actions. The plain difference would still be 6 ms: on any real action this should not be that much of an issue. If it was, you shouldn't use rails in the first place but look into Merb or Metal instead. Right?</p>
