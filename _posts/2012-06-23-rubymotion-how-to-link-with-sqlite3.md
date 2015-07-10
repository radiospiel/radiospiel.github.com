---
layout: post
title: ! 'rubymotion: How to link with sqlite3'
published: true
date: 2012-06-23
categories: []
---
<p>The iOS-platform comes with a sqlite3 library. To use it from within a rubymotion-project, just add these lines to your Rakefile:</p>

```
app.libs << "-lsqlite3"
