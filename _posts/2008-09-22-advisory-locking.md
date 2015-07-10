---
layout: post
title: ! 'Advisory Locking    '
published: true
date: 2008-09-22
tags:
- ruby
---
<p>Strangely RoR doesn't come with support for Advisory Locking. However, a typical
rails application usually not only needs to synchronize access to the data in
the database but needs to synchronize external data as well. Given that a typical rails application has more than one parallel thread of execution, a programmer might need some tools to synchronize access to shared resources.</p>


>  Note: From now on I use the term 'thread' here to denote parallel threads of execution,
>  **not** to denote parallel threads of execution in the address space of a single
>   process!
>  These 'threads' can be multiple OS processes on more than one machine.

<p>In a typical RoR application each application instance connects to the same database. This puts the database server process in a somewhat unique position: It can be used as a synchronization point, i.e. as that piece of software that negotiates locks between clients. To not interfere with the genuine data flow in the application we can't use the algorithms in ActiveRecord::Locking: when they lock at all they are build upon transactions - and transactions are already put to use to ensure data integrity (by ActiveRecord::Base.save and friends).</p>

<p>What we need instead is an advisory looking mechanism that works regardless of transactions. Luckily many databases support this out of the box - we only have to turn it into some nice ruby code. What we want to achieve is code like this:</p>

```ruby
class Lockable < ActiveRecord::Base
  with_advisory_locks
end

lockable.locked :write do
  # now we have exclusive access to the external resource represented
  # by 'lockable'
end
```

<p>The lockable object will still be stored in the database; so it can be identified uniquely throughout the entire application.</p>

<p>The following module defines a mixin which implements the above interface. Note that
there is already some implementation detail in here: this code defines a polymorphic
association between would-be-lockable objects and a table that stores all those objects.
This is used here to allocate a global identifier to each object.</p>

```ruby
#
# This module is the mixin which implements the locked method.
module ActiveRecord::WithAdvisoryLocks
  def self.included(klass)
    klass.has_one :advisory_lock, :dependent => :delete,
      :as => :obj, :class_name => "ActiveRecord::AdvisoryLock"
    klass.after_create { |rec| ActiveRecord::AdvisoryLock.create! :obj => rec }
  end

  #
  # acquires a lock on that object and runs the block, passing in the then-locked
  # object.
  def locked(mode == :write, &block)
    advisory_lock.acquire_lock(mode)
    begin
      yield self
    ensure
      advisory_lock.release_lock(mode)
    end
  end
end
```

<p>The actual database-specific code has to implement acquire_lock and release_lock. The
following is a POSTGRESQL-specific version:</p>

```ruby
#
# A DB-specific AdvisoryLock implementation. The following is
# for postgresql
#
# create_table :advisory_locks do |t|
#   t.column :obj_id, :integer, :null => false
#   t.column :obj_type,  :varchar, :null => false
# end
#
# add_index :advisory_locks, [ :obj_id, :obj_type ], :unique => true
#
class ActiveRecord::AdvisoryLock => ActiveRecord::Base
  def acquire_lock(mode)
    mode = mode == :read ? "_shared" : ""
    connection.execute "SELECT pg_advisory_lock#{mode}(#{id})"
  end

  def release_lock(mode)
    mode = mode == :read ? "_shared" : ""
    connection.execute "SELECT pg_advisory_unlock#{mode}(#{id})"
  end
end
```

## Notes

A **MySQL**-based implementation would use MySQL's GET_LOCK() and RELEASE_LOCK()
functions instead. As MySQL doesn't support shared a.k.a. read locks
out of the box this mode had to be simulated in the implementation.

Note: As MySQL's functions support string arguments we would not necessarily
need an associated table here. Neither would we need such a table with
Postgresql, if we could safely translate the table name of an application
wide unique 32-bit number.


**Sqlite3** does not support any such functions. As a Sqlite3 database
can only be accessed locally an implementation could make use one
of the many possible implementations for a single machine.
See fcntl(2).
