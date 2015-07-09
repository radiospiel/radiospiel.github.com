---
layout: post
title: Body works
published: true
date: 2008-10-13
categories: []
---
<p>One cool Rails feature - however undervalued - are after-filters. You might ask: What is the point of doing things after I am already done doing all things? Well, to prevent micromanagement: Sometimes you just want to do things all the time, or regardless of the specific request: say, embedding some <a href="/0x05-sql-instruments">SQL stats</a> on the bottom of all of your pages. This is just one example where you could should look into after_filters first thing in the morning.</p>

<p>When an after_filter is activated the "result" of the action is already build completely and stored in  "result.body". Now all activate after_filters have a chance to take matters into their own hands; they are even free to throw away everything! So this is the time where you are able to modify an action's result any way you want. There is, however, some rope to hang yourself: beware that</p>

<ul>
<li><p>not every server response is HTML: RESTful responses are not HTML. AJAX responses might or might not be HTML, depending on the way you implement and use AJAX. Some requests might even send image data generated on-the-fly. Your code must be able to detect those cases - and, when unsure, leave the response alone. 'request.xhr?' and 'response.content_type' are quite handy to figure out what kind of data you are dealing with.</p></li>
<li><p>While you can append some HTML data to a (X)HTML document, you shouldn't do that: the result would not validate (even though browsers are usually displaying such content regardless). Instead you should put your inserts in a place where they are valid: insert HTML code, that should be visible, into the &lt;body&gt; tag. CSS rules and links belong in the &lt;head&gt; node, as should &lt;script src=''&gt; tags. <em>In fact, you should put &lt;script&gt; tags into the body, if possible. This makes for much faster loading times.</em></p></li>
<li>
<p>Don't even think about parsing the HTML response. This would be terribly slow. Instead use string relacements to add your code in the right place. After all, if your application produces otherwise valid (X)HTML you know that your code has a &lt;head&gt; node in the beginning and a body node in the end.</p>

<ul>
<li><p>To add javascript code to a HTML response you use a &lt;script&gt; tag (obviously). Embedding it into a <a href="http://en.wikipedia.org/wiki/CDATA">CDATA</a> section might be wise.</p></li>
<li><p>Adding HTML nodes to a javascript response? Think twice - what do you want to achieve? To add some nodes to the document that is visible right now you could use a "document.write('...');" sequence.</p></li>
</ul>
</li>
</ul>
<p>Enough sweettalk, here is a code example:</p>

<div class="CodeRay">
  <div class="code"><pre>class OradController &lt; ApplicationController

  # ...

  private

  # --- check for response types --------------------------------------

  def response_is_html?                                 #:nodoc:
    # TODO: this is certainly not the only HTML Content-Type!? 
    headers[&quot;Content-Type&quot;] == nil 
  end

  # --- append some HTML to the response body -------------------------

  def append_html(html)
    return unless response_is_html?
    response.body.gsub! /&lt;\/body=&gt;\s*&lt;\/html=&gt;\s*$/i, &quot;#{html}&lt;/body=&gt;&lt;/html=&gt;&quot;
 end

  after_filter :hello_after_filter

  def hello_after_filter
    append_html, &quot;&lt;p=&gt;Kilroy was here!&lt;/p=&gt;&quot;
  end
end</pre></div>
</div>


<p><strong>Bold announcement:</strong> next week I will revisit Body Works again with an actually working plugin, fully tested and all.</p>