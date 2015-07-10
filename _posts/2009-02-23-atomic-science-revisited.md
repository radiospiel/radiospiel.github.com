---
layout: post
title: Atomic science, revisited
published: true
date: 2009-02-23
categories:
- activerecord
- atomic
- find_or_update
- race condition
- rails
- ruby
---
<p><a href="http://1rad.wordpress.com/2008/09/29/0x04-atomic-science/">I lamented earlier</a> about the absence of a racecondition-free find_or_create implementation and somewhat promised to check back with a working implementation. YES, WE CAN: NOW is the time, folks!</p>

<p>As stated there the first thing missing is an advisory locking implementation. With the database as the natural choice for a synchronisation point that implementation would be DB dependant, the following being a MySQL-implementation:</p>

```ruby
module ActiveRecord::ConnectionAdapters::MysqlAdapter::AdvisoryLock
  TIMEOUT=10

  def locked(lock)
    lock = "#{current_database}_#{lock}"

    begin
      execute "SELECT GET_LOCK(#{quote(lock)},#{TIMEOUT})"
      yield
    ensure
      execute "SELECT RELEASE_LOCK(#{quote(lock)})"
    end
  end
end

class ActiveRecord::ConnectionAdapters::MysqlAdapter
  include AdvisoryLock
end
```

<p>Now, whats still left is a find_or_create implementation. I like that one:</p>

```ruby
ActiveRecord::Base

class ActiveRecord::Base
  def self.find_first_by_hash(hash)
    find :first, :conditions => hash
  end

  def self.find_or_create(hash)
    find(hash) || connection.locked("#{self.table_name}.find_or_create") do
      find(hash) || create(hash)
    end
  end
end
```

<p>Where is the "find_or_create_by_attribute_names", you might ask? Well, I am not too fond of those anyways, because writing</p>

```ruby
find_by_name_and_zipcode name, zipcode
```

<p>adds several hundred line to the ActiveRecord implementation, doesn't work with attributes that have an underscore in its name, and saves only a few typestrokes over</p>

```ruby
find :first, :name => name, :zipcode => zipcode
```

<p>which could even be reduced down to one by an extended find() implementation, which takes a Hash as a possible first argument.</p>
