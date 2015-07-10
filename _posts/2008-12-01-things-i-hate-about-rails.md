---
layout: post
title: things I hate about rails
published: true
date: 2008-12-01
tags:
- ruby
---
<p>These are somewhat random rants, proceed with utmost care!</p>

<h2>Inconsistent naming schemes</h2>

<p>So, it is called "find(id)" and raises an exception if it doesn't find the object? When I call it "find_by_id(id)" it doesn't raise on error? And now, with Rails 2.2 I can call "find_by_id!(id)" which raises an exception. Shouldn't find be called "find!" then?</p>

<p>Another example: "respond_to?" or "responds_to?". "responds_to?" reads better, right? Well, gotcha!</p>

<h2>Rails doesn't do MVC</h2>

<p>"What? Rails doesn't do MVC? But we have models, controllers, and views!" - You don't believe me? Read the article on MVC at <a href="http://en.wikipedia.org/wiki/Model-view-controller">wikipedia</a> and meditate on the dashed links in the graphic, as a mental exercise.</p>

<h2>Rails doesn't use exceptions well enough</h2>

<p>...and noone understands why. Well, I at least don't understand why.</p>

<h2>un-DRY migrations</h2>

<p>DRY up your code! Everyone is saying this, again and again. This seems to be the mantra of everybody who loves rails. But where is DRYness in migrations? Why do we have to create a table in the "up" method and remove a table in "down". Rails is so much about convention, and the usual and sensible convention is that the "down" method reverses what is going on in the "up" method.</p>

<h2>Well, migrations anyway...</h2>

<p>...are one of the areas where Rails' imaginative naming schemes hit hardest. Why is it "add_index", when SQL uses "CREATE INDEX"? Why is it "remove_table", where SQL uses "DROP TABLE"? Do you want to discourage database developers from using rails?</p>

<h2>...and SQL</h2>

<p>Speaking of SQL: ActiveRecord is sometimes really hard to use, because it tries to hide so much of the SQL logic, but misses some crucial points. Views? Foreign keys? Database based validations? All this stuff is sometimes easier to do in SQL as it is in Rails, doesn't have race conditions, and performs usually much better. And nested associations can get pretty ugly. I mean, where is the point of writing</p>

```
customer.latest_contracts.
  with_pictures.
  ordered_backwards.
  limit(5).find(:all, :include => "images", :order => "RANDOM()")


<p>(ok, I made that up) when the same can a) be expressed in SQL as elegantly b) the code is not DB agnostic anyways (or how does your database call the RAND() function) and c) a SQL code can run on many "customer" objects at the same time while this is hard to do with plain ActiveRecord. In hiding some parts of SQL Rails actually discourages developers from learning database stuff, which might effect in inefficient or erroneous applications.</p>

<p>I will look into <a href="http://code.google.com/p/ruby-datamapper">Datamapper</a>, I hope those guys get it done better.</p>

<h2>I18N and Unicode</h2>

<p>C'mon guys.. everyone has Unicode. Unicode is not that new, and only because you are living in the U.S. of A. doesn't mean everybody revolves around the same things that you are revolving around. And, yes, I know, it is basically Ruby's fault. But still: do we all have to wait for ruby 2.0, which given the current speed of development will not be available before 2010? Please add some real unicode support into the Rails framework - a switch to ruby supported Unicode strings shouldn't be that hard later.</p>

<h2>Dangerous 'innovations'</h2>

<p>Do you remember when Rails 2.0.1 rolled out and broke more or less every website out there that cached pages containing forms? Because of the (in fact even somewhat useful) authencity token, which was enabled by default? Changes like this <b>must</b> be prepared more thoroughly, please.</p>

<p>Another example: Rails 2.2 will bring the "Array#second, Array#third ... Array#tenth" method. I would like to vote that one for the most useless innovation in ages... I mean, guys, where is the innovation in that? I guess some people might want to enforce the use of those methods, but: a) using this you need more keystrokes, b) is it eightth? eighth? eigth? Not each of us is a native writer and c) I see a number of off-by-one-errors coming along this way. Remember: "array<a href="http://www.google.com/search?q=4" target="_blank">4</a>" is NOT "array.forth", it is "array.fifth"!</p>

<h2>Rails help you shooting in your foot...</h2>

<p>...and quite efficiently at times: Rails offers things that quite invite you to shoot you in your own foot. See the style in which rails suggests writing <a href="/0x0b-saving-is-a-virtue-avarice-is-vice/">functional tests</a>. (If you don't understand the following please check that article.) A much better and cleaner solution would be, of course, something along the lines of</p>

```
class FunctionalTest < ActionController::TestCase
  tests :duu_controller
  ...
end


<p>Or see the "great new feature" where Rails supports not_modified_since. I am pretty sure that many people will try to use that feature and will just fail, because a model's modification date is usually not really consistent: if you modify a has_many association the 'updated_at' value of the model holding the association will <strong>NOT</strong> reflect that change! That means the change will not be sent to the client.</p>

<h2>updated_at/created_at</h2>

<p>Speaking of updated_at/created_at: where is the reason behind having that timestamp created in the application? This results in inconsistent timestamps when an application is served from application servers on multiple machines. It is just impossible to sync different machines to the same time in high precision.</p>

<p>Everything would be much easier with database generated timestamps. They might be off a couple of seconds (or even more), but they are consistent. And SQL has it built in: this is what "NOW()" is for, guys!</p>

<h2>Eager loading vs. mass loading</h2>

<p>Say you have Books, and each Book has a thumbnail. Say you show a list of books. Say your HTML designer wants to embed thumbnails. Easy as pie:</p>

```
<%= image book.thumbnail.image_file %=>


<p>and then the performance goes down. Easy to fix, right? Just use eager-loading. So you change the controller action into something like</p>

```
@books = Book.find :all, 
  :conditions => [ "user.id=?", current_user ], 
  :include => [ "thumbnail" ]


<p>But wait! Why should the controller code know about a designers decision to show thumbnail images? What is missing here is some piece of code to mass load an association on an array of objects, in an efficient manner. The designer would put that piece into the view, like this:</p>

```
<% @books.load_association "thumbnail" %=>


<h2>But still...</h2>

<p>Rails is the platform where Web development is more fun than with everyone else (I know of and have tried). Suggestions, as always, welcome.</p>
