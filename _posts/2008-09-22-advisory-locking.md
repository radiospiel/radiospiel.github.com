---
layout: post
title: ! 'Advisory Locking    '
published: true
date: 2008-09-22
categories:
- 1rad
- ruby
---
<p>Strangely RoR doesn't come with support for Advisory Locking. However, a typical
rails application usually not only needs to synchronize access to the data in
the database but needs to synchronize external data as well. Given that a typical rails application has more than one parallel thread of execution, a programmer might need some tools to synchronize access to shared resources.</p>

<blockquote class="posterous_medium_quote">
  Note: From now on I use the term 'thread' here to denote parallel threads of execution,
  <b>not</b> to denote parallel threads of execution in the address space of a single
   process!
  These 'threads' can be multiple processes on more than one machine.
</blockquote>


<p>In a typical RoR application each application instance connects to the same database. This puts the database server process in a somewhat unique position: It can be used as a synchronization point, i.e. as that piece of software that negotiates locks between clients. To not interfere with the genuine data flow in the application we can't use the algorithms in ActiveRecord::Locking: when they lock at all they are build upon transactions - and transactions are already put to use to ensure data integrity (by ActiveRecord::Base.save and friends).</p>

<p>What we need instead is an advisory looking mechanism that works regardless of transactions. Luckily many databases support this out of the box - we only have to turn it into some nice ruby code. What we want to achieve is code like this:</p>

<div class="CodeRay">
  <div class="code"><pre>class Lockable &lt; ActiveRecord::Base
  with_advisory_locks
end

lockable.locked :write do
  # now we have exclusive access to the external resource represented
  # by 'lockable'
end</pre></div>
</div>


<p>The lockable object will still be stored in the database; so it can be identified uniquely throughout the entire application.</p>

<p>The following module defines a mixin which implements the above interface. Note that
there is already some implementation detail in here: this code defines a polymorphic
association between would-be-lockable objects and a table that stores all those objects.
This is used here to allocate a global identifier to each object.</p>

<div class="CodeRay">
  <div class="code"><pre>#
# This module is the mixin which implements the locked method.
module ActiveRecord::WithAdvisoryLocks
  def self.included(klass)
    klass.has_one :advisory_lock, :dependent =&gt; :delete,
      :as =&gt; :obj, :class_name =&gt; &quot;ActiveRecord::AdvisoryLock&quot;
    klass.after_create { |rec| ActiveRecord::AdvisoryLock.create! :obj =&gt; rec }
  end

  #
  # acquires a lock on that object and runs the block, passing in the then-locked
  # object.
  def locked(mode == :write, &amp;block)
    advisory_lock.acquire_lock(mode)
    begin
      yield self
    ensure
      advisory_lock.release_lock(mode)
    end
  end
end</pre></div>
</div>


<p>The actual database-specific code has to implement acquire_lock and release_lock. The
following is a POSTGRESQL-specific version:</p>

<div class="CodeRay">
  <div class="code"><pre>#
# A DB-specific AdvisoryLock implementation. The following is
# for postgresql
#
# create_table :advisory_locks do |t|
#   t.column :obj_id, :integer, :null =&gt; false
#   t.column :obj_type,  :varchar, :null =&gt; false
# end
#
# add_index :advisory_locks, [ :obj_id, :obj_type ], :unique =&gt; true
#
class ActiveRecord::AdvisoryLock =&gt; ActiveRecord::Base
  def acquire_lock(mode)
    mode = mode == :read ? &quot;_shared&quot; : &quot;&quot;
    connection.execute &quot;SELECT pg_advisory_lock#{mode}(#{id})&quot;
  end

  def release_lock(mode)
    mode = mode == :read ? &quot;_shared&quot; : &quot;&quot;
    connection.execute &quot;SELECT pg_advisory_unlock#{mode}(#{id})&quot;
  end
end</pre></div>
</div>


<p>A MySQL-based implementation would use MySQL's GET_LOCK() and RELEASE_LOCK()
functions instead. As MySQL doesn't support shared a.k.a. read locks
out of the box this mode had to be simulated in the implementation.</p>

<blockquote class="posterous_medium_quote">
  Note: As MySQL's functions support string arguments we would not necessarily
  need an associated table here. Neither would we need such a table with
  POSTGRESQL, if we could safely translate the table name of an application
  wide unique 32-bit number.
</blockquote>




<blockquote class="posterous_medium_quote">
  Note: Sqlite3 does not support any such functions. As a Sqlite3 database
  can only be accessed locally an implementation could make use one
  of the many possible implementations for a single machine.
  See fcntl(2).
</blockquote>
