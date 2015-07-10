---
layout: post
title: Body works
published: true
date: 2008-10-13
tags:
- ruby
---
<p>One cool Rails feature - however undervalued - are after-filters. You might ask: What is the point of doing things after I am already done doing all things? Well, to prevent micromanagement: Sometimes you just want to do things all the time, or regardless of the specific request: say, embedding some <a href="/0x05-sql-instruments">SQL stats</a> on the bottom of all of your pages. This is just one example where you could should look into after_filters first thing in the morning.</p>

<p>When an after_filter is activated the "result" of the action is already build completely and stored in  "result.body". Now all activate after_filters have a chance to take matters into their own hands; they are even free to throw away everything! So this is the time where you are able to modify an action's result any way you want. There is, however, some rope to hang yourself: beware that</p>

- not every server response is HTML: RESTful responses are not HTML. AJAX responses might or might not be HTML, depending on the way you implement and use AJAX. Some requests might even send image data generated on-the-fly. Your code must be able to detect those cases - and, when unsure, leave the response alone. 'request.xhr?' and 'response.content_type' are quite handy to figure out what kind of data you are dealing with.
- While you can append some HTML data to a (X)HTML document, you shouldn't do that: the result would not validate (even though browsers are usually displaying such content regardless). Instead you should put your inserts in a place where they are valid: insert HTML code, that should be visible, into the <body> tag. CSS rules and links belong in the <head> node, as should `<script src=''>` tags. <em>In fact, you should put `<script>` tags into the body, if possible. This makes for much faster loading times.</em>
- Don't even think about parsing the HTML response. This would be terribly slow. Instead use string relacements to add your code in the right place. After all, if your application produces otherwise valid (X)HTML you know that your code has a <head> node in the beginning and a body node in the end.

  - To add javascript code to a HTML response you use a `<script>` tag (obviously). Embedding it into a <a href="http://en.wikipedia.org/wiki/CDATA">CDATA</a> section might be wise.
  - Adding HTML nodes to a javascript response? Think twice - what do you want to achieve? To add some nodes to the document that is visible right now you could use a "document.write('...');" sequence.

<p>Enough sweettalk, here is a code example:</p>

```ruby
class OradController < ApplicationController

  # ...

  private

  # --- check for response types --------------------------------------

  def response_is_html?                                 #:nodoc:
    # TODO: this is certainly not the only HTML Content-Type!? 
    headers["Content-Type"] == nil 
  end

  # --- append some HTML to the response body -------------------------

  def append_html(html)
    return unless response_is_html?
    response.body.gsub! /<\/body=>\s*<\/html=>\s*$/i, "#{html}</body=></html=>"
 end

  after_filter :hello_after_filter

  def hello_after_filter
    append_html, "<p>Kilroy was here!</p=>"
  end
end
```
