---
layout: post
title: ! '[BUG] cross-thread violation of rb_gc()...'
published: true
date: 2009-09-06
categories: []
---
<p>If you are running accross this error</p>

```
[BUG] cross-thread violation of rb_gc()
ruby 1.8.6 (2008-08-11) [universal-darwin9.0]
```

because, say, you are are installing the latest so-called "stable" <a href="http://github.com/fdv/typo/tree/master">typo</a> version on your OSX machine, then you might see the above error. Here is the solution: <em>remove the bundled json gem!</em>: It is version 1.1.3, which is way old, and apparently it contains some binaries that were compiled using ruby 1.8.6. Just install the "json" gem locally.</p>
