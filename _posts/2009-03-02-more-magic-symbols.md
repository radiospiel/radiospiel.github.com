---
layout: post
title: More magic symbols
published: true
date: 2009-03-02
tags:
- ruby
---
<p>With just that small addition:</p>

```ruby
class Symbol
  alias_method :old_eeq, :"===" 

  def ===(obj)
    (obj.respond_to?(self) && obj.send(self)) || old_eeq(obj)
  end
end
```

<p>you can do things like</p>

```ruby
case current_user
when :admin?    then "admin"
when :regular?  then "something_for_regulars"
when :guest?    then "do_guest_stuff"
else "huh"
end
```


<p>Isn't that cool much more readable?</p>
