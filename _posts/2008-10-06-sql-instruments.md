---
layout: post
title: SQL instruments!!
published: true
date: 2008-10-06
tags:
- ruby
---

<p>There are times, when you build a really complicated web page. Take a page like <a href="http://www.sportme.de">http://www.sportme.de</a>: this page has more than eight parts that are to be taken straight from the database (assuming you are not logged in and get the front page of that website). You see blocks showing professional members, newest members, random members, a news ticker, a "who is online list", etc. Each entry in these boxes usually pulls more than one row from one table from the database; the most obvious case when showing user teasers, where the user's image itself would not come from the table holding the users, but from "somewhere else".</p>

<p>Now that you know, that a page like this inflicts some heavy database traffic, but - how heavy is it really? We all know the action stats line in the log, which gives at least some hints:</p>


    Completed in 1.92195 (0 reqs/sec) | Rendering: 1.91364 (99%) | DB: 0.00120 (0%) | 200 OK [http://xxx/user?user_id=2]


<p>but what it doesn't say is how many queries are involved. Especially on a site that handles many requests in parallel, the higher the number of queries the higher the stress on the database server would become, resulting in a worsening response time. And the more effect you would expect from optimizing your data and your queries.</p>

<p>But help is never far away in ruby land, where everything is dynamic. The first step in getting some more insight is to add some code to collect those stats from the database connection.</p>

<p>Many applications use the same database for all their needs. As RoR is not multithreaded in itself, this gives us basically one database connection per mongrel-or-whatever-your-server-is instance, which is <em>ActiveRecord::Base.connection</em>. Depending on the database you are using, this object is of one of the <em>ActiveRecord::ConnectionAdapters::*Adaptor</em> classes, that all inherit  <em>ActiveRecord::ConnectionAdapters::AbstractAdapter</em>. Which makes that class the single point of attack.</p>

<p>A quick look at <em>ActiveRecord::ConnectionAdapters::AbstractAdapter</em>'s source code shows, that the <em>:select_all</em> method is most likely the one that sends a query to the database server. So here could we add a counter, and, as we are at it, another database connection timer. (Note: This code needs the 'traits' gem which I use because I am a bit lazy sometimes):</p>

```ruby
require 'traits'

module ActiveRecord::QueryStats
  def self.included(base)
    base.class_eval do
      alias_method_chain :select_all, :query_stats
    end
  end

  has :stats_query_count => 0

  def select_all_with_query_stats(*args)
    self.stats_query_count += 1
    select_all_without_query_stats(*args)
  end
end

class ActiveRecord::ConnectionAdapters::AbstractAdapter
  include ActiveRecord::QueryStats
end
```


<p>For a proper log entry we then need to adjust the ActionController's control flow. We need some point just before Rails works an action, and some point just when the action is finished. This time Rails even offers a public API for just that stuff: around_filters. And here is how to do it:</p>

```ruby
module ActionController::QueryStats
  def self.included(klass)
    klass.around_filter do |controller, action|
      queries = ActiveRecord::Base.connection.stats_query_count

      action.call

      queries = ActiveRecord::Base.connection.stats_query_count - queries

      controller.logger.debug "Query stats on #{controller.controller_name}/" +
        "#{controller.action_name}: " +
        "#{queries} queries"
    end
  end
end

class ActionController::Base
  include ActionController::QueryStats
end
```


<p>which gives us what we want:</p>

```
Query stats on user/index: 3 queries.
Completed in 1.92195 (0 reqs/sec) | Rendering: 1.91364 (99%) | ...
```


Note: sometimes, for some reasons, Rails' timing code doesn't work perfectly. 
I have seen stats lines with a DB ratio of more than 100%, and I have seen 
negative timings. Besides, timing is one area where you experience huge 
differences between platforms (hardware and software). So treat all timing 
measurements produced by any application with some care.
