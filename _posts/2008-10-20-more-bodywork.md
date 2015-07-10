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

```
class BlaController < ApplicationController

  def index
    render :layout => "web", :text => "This is some text."
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
    link_to_file "some_app.js"    # add some JS file to the header
    link_to_file "some_app.css"   # add some CSS to the header

                                  # add some stuff to the HTML body
    append_html "<p>This page is dignified with some bodywork.</p>"
  end
end


<p>This looks a bit chatty to me. Does any of you has any idea about a more concise syntax here?</p>
