---
layout: post
title: You will know me by the trail of the dead
published: true
date: 2008-12-08
tags:
- performance
- ruby
---
<p>Say you are running a bookstore. (Didn't you know that Rails is <a href="http://www.jonathansng.com/ruby-on-rails/ruby-on-rails-tutorial-now-with-more-202/">all</a>
<a href="https://partnernet.amazon.de/gp/associates/network/build-links/individual/simple-get-html.html?ie=UTF8&amp;amp;assoc%5Fss%5Fref=http%3A%2F%2Fwww.amazon.de%2Fgp%2Fproduct%2F0977616630%3Fie%3DUTF8%26ref%255F%3Dsr%255F1%255F1%26s%3Dbooks-intl-de%26qid%3D1228655474%26sr%3D1-1&amp;amp;asin=0977616630&amp;amp;parentASIN=0977616630">about</a> <a href="https://partnernet.amazon.de/gp/associates/network/build-links/individual/simple-get-html.html?ie=UTF8&amp;amp;assoc%5Fss%5Fref=http%3A%2F%2Fwww.amazon.de%2Fgp%2Fproduct%2F0321445619%3Fie%3DUTF8%26ref%255F%3Dsr%255F1%255F3%26s%3Dbooks-intl-de%26qid%3D1228655571%26sr%3D1-3&amp;amp;asin=0321445619&amp;amp;parentASIN=0321445619">selling</a> <a href="https://partnernet.amazon.de/gp/associates/network/build-links/individual/simple-get-html.html?ie=UTF8&amp;amp;assoc%5Fss%5Fref=http%3A%2F%2Fwww.amazon.de%2Fgp%2Fproduct%2F0978739205%3Fie%3DUTF8%26ref%255F%3Dsr%255F1%255F4%26s%3Dbooks-intl-de%26qid%3D1228655474%26sr%3D1-4&amp;amp;asin=0978739205&amp;amp;parentASIN=0978739205">books</a>?). Like everybody else you moved over to an IT infrastructure for managing sales during the last years, and now you have 5000 customers in the database and know about all the books these guys bought at your store - on average a swooping 15 books per person.</p>

<p>Now Christmas is coming: you plan a small campaign, where you would send a gift coupon to your customers. And as the package is designed nicely (so it wouldn't disappear amongst all those buy-at-as-for-xmas-coupons) it is somewhat expensive, and you want to send it only to your most valuable customers - i.e. to those that bought more than 5 hardcover books.</p>

<p>Easy enough to do:</p>

```ruby
User.find(:all) do |user|
  number_of_hardcovers = user.books.inject(0) do |cnt, book| 
    cnt += book.hardcover? ? 1 : 0
  end
  print_address(user) if number_of_hardcovers >= 5
end
```

<p>(Well, you still need the package designed, packed, and put to the post office. But this exercise is left to you, reader. If you want my address for some holiday gift just ask.)</p>

<blockquote class="posterous_short_quote">
  Note: Yes, I know about count. This example is somewhat made up - 
  and should just highlight the point.
</blockquote>


<h2>Now, 1 year later...</h2>

<p>Last years x-mas campaign was just a hit! You now have 100.000 customers in the database with an average number of books bought of 30. Congratulation! But of course, this years campaign will be even better than last years! You are well-prepared, are you not?</p>

<p>No, in fact you are not. The above code would still work, but really really slooow this time; it will clog your website, and drive your online customers away from you! And all that just because that little piece of code collects data on all users, along with all the books they have bought, in your computer's memory. This sums up to RAM usage for 100k users and 3M books - which rises easily into several gigabyte.</p>

<p>The problem is that even though you are requesting the books on a per-user level, ActiveRecord's built in "caching" saves the books for all the users (for the tech savvy: in the "@books" instance variable in each of  the User models). Usually this is exceptionally smart (TM) behaviour, but this time it is really shit hitting the fan.</p>

<p>There are several ways to deal w/that issue. You could nil-ing those instance variables, which will then garbage collect the no-longer needed memory at some point...</p>

```ruby
User.find(:all) do |user|
  ...
  user.instance_variable_set "@books", nil
end
```

<p>..or you could just reload the user model:</p>

```ruby
User.find(:all) do |user|
  ...
  user.reload
end
```

<p>However, the first solution is somewhat dirty, because it relies on a non-documented implementation detail. The second one is inefficient because it runs an additional database query on an object that we just don't need any longer...</p>

<h2>Enter <em>destructive_each!</em>
</h2>

<p>Just don't need any longer? Well then, let's get away with these objects as soon as possible! And while <em>Array#each</em> doesn't do this for us - using it the earliest point to get rid of these objects would be after each returned - we can roll out our own each, which is just a little bit destructive. It works (quite) like each, but removes each object after it is not needed anymore.</p>

```ruby
class Array
  def destructive_each!
    until empty?
      yield first
      shift
    end
  end
end
```

Note: the `yield first; shift` order is intended and has one significant 
difference to `yield shift` - think Exceptions!
