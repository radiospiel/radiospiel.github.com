---
layout: post
title: the fastest "is a power of 2" test I could think of
published: true
date: 2010-04-01
categories:
- performance
- ruby
---

<p>Can you beat this?</p>

```ruby
def ispow2me2(n)
  n & (n - 1) == 0
end
```

