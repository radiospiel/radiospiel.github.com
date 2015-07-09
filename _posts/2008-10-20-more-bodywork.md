---
layout: post
title: More bodywork
published: true
date: 2008-10-20
categories:
- 1rad
- controller
- rails
- ruby
---
<p>Now, <a href="/0x06-body-works">last monday</a> I promised to rework the bodywork code into a plugin, "tested and all". Some clashes with git later I finally managed to push it to github, you'll find it <a href="http://github.com/pboy/with_bodywork/">at github</a>. It even comes with a nice and cute README file, so head over and check it out.</p>

<p>Here I want to ask you, dear reader, for your praised input. As you might know, this is the proposed 'with_bodywork' usage:</p>

<div class="CodeRay">
  <div class="code"><pre>class BlaController &lt; ApplicationController

  def index
    render :layout =&gt; &quot;web&quot;, :text =&gt; &quot;This is some text.&quot;
  end

  private

  #
  # This adds the bodywork methods to the current controller.  
  with_bodywork

  #
  # An after_filter which uses some of the bodywork methods
  # on all actions
  after_filter :add_somefunkystuff

  def add_somefunkystuff
    link_to_file &quot;some_app.js&quot;    # add some JS file to the header
    link_to_file &quot;some_app.css&quot;   # add some CSS to the header

                                  # add some stuff to the HTML body
    append_html &quot;&lt;p&gt;This page is dignified with some bodywork.&lt;/p&gt;&quot;
  end
end</pre></div>
</div>


<p>This looks a bit chatty to me. Does any of you has any idea about a more concise syntax here?</p>