---
layout: post
title: Atomic science
published: true
date: 2008-09-29
categories:
- 1rad
- ruby
---
<h2>find_or_create_by_: think twice. before racing
</h2>

<p>The usually recommended way to create an item if it doesn't exist already is by using
find_or_create_by... This, however, comes with a race condition built in. Which doesn't always race,
so think twice when and how you use it.</p>

<p>This code, for example, <em>might</em> deploy the race condition:</p>

```
class Model < ActiveRecord::Base
  def dependant
    @dependant ||= Dependant.find_or_create_by_model_id(id)
  end
end


<p>(This code tries to create the <em>dependant</em> model only if really needed.) This runs fine when you test it in the console, but beware: danger's ahead! To understand the problem let's have a look at the SQL code generated and executed by running the following piece of code, which should result in exactly one dependant object:</p>

```
m = Model.create! :key => "key", :data => ""
puts m.dependant.inspect

m2 = Model.find_by_id(m.id)
puts m2.dependant.inspect


<p>This code results in the following SQL code (note that I am using Postgresql, and that I edited the log to show only the important stuff)</p>

```
BEGIN
  INSERT INTO models ("key", "data") VALUES('key', '')
COMMIT

SELECT * FROM dependants WHERE (dependants."model_id" = 9) LIMIT 1
BEGIN
  INSERT INTO dependants ("model_id") VALUES(9)
COMMIT

SELECT * FROM models WHERE (models."id" = 9) LIMIT 1
SELECT * FROM dependants WHERE (dependants."model_id" = 9) LIMIT 1


<p>This code seems to result in one and only one Dependant object, which is linked with the Model object. However, just imagine you would run the same sequence of SQL code from two different database connections (as would be the case with two mongrel instances, for example). The execution order could be:</p>

<ul>
<li>
<p>Connection 1:
</p>
```
SELECT * FROM models WHERE (models."id" = 3) LIMIT 1
  SELECT * FROM dependants WHERE (dependants."model_id" = 3) LIMIT 1

</li>
<li>
<p>Connection 2:
</p>
```
SELECT * FROM models WHERE (models."id" = 3) LIMIT 1
  SELECT * FROM dependants WHERE (dependants."model_id" = 3) LIMIT 1

</li>
</ul>
<p>...at which point <em>both</em> connections would find no Dependant object for the model with id=3, and would both decide  to create it. Which leaves us with <em>two</em> entries in the database, or with an exception, if uniqueness is enforced on the database.</p>

<blockquote class="posterous_short_quote">
  Note that this error condition is hard to provoke in a test scenario.
</blockquote>


<h2>Transactions to the rescue?</h2>

<p>Your college graduate with a diploma in database design now starts mumbling about transaction isolation levels, doesn't (s)he? Well, that, of course, could help, if only...</p>

<p>The code above would work fine when wrapped into SERIALIZABLE. This, however, has only a limited usefulness, because transaction isolation levels are implemented so differently on different RDBMs. In Postgres, for example, you have to set the serializable isolation level after a transactions BEGIN, but - if I am not mistaken - before your first SELECT in the transaction, and definitely before the first INSERT/UPDATE/DELETE. Which is problematic, because ActiveRecord opens a transaction whenever an AR object is created, updated, or deleted, and had to know at that stage already, if that strict isolation level is required. And you wouldn't want to run <em>all</em> your queries in that transaction isolation level: this would be the fastest way to kill your apps performance.</p>

<h2>What about database constraints?</h2>

<p>To prevent the creation of two dependant objects for the same model object we can add a constraint on the dependants table: by doing it in ruby code (validates_uniqueness), or by adding a unique index to the dependant table. As you usually employ an index on the dependant column there was no big deal in making it UNIQUE.</p>

<p>But does that help? In the error case we would get an exception from <em>Dependant#find_or_create_by_model_id</em> which rollback the current transaction, if there is any. Which leaves you with no created objects in a case where creation of an object is perfectly fine and legal, the only problem an agreement between different database client processes. Is this what you want your users to see: "Could not store your input because my database processes run out of sync. This, however, is unlikely to happen again, so just keep trying..."?</p>

<h2>Working around...</h2>

<p>Well, the problem remains, and is not easy to solve. What we would need is a database connection which would allow to increase the isolation level during a transaction. Unless we get that we have to take other measures, <a href="/0x03-advisory-locking/">Advisory locking</a> or explicitely <a href="http://www.postgresql.org/docs/8.3/static/explicit-locking.html">locking the involved tables</a>.</p>

<h2>Further reading</h2>

<ul>
<li>Not exactly the topic of this weeks entry, but a quite interesting post
about <a href="http://tektastic.com/2007/09/on-ruby-on-rails-with-postgresql-and.html%22><a href="http://tektastic.com/2007/09/on-ruby-on-rails-with-postgresql-and.html">http://tektastic.com/2007/09/on-ruby-on-rails-with-postgresql-and.html</a>">using foreign keys</a> in ruby.</li>
<li>At "Shades of Gray" you'll find some <a href="http://blog.grayproductions.net/articles/five_activerecord_tips">thoughts</a>
about that as well. I do disagree with that solution, though.</li>
</ul>
