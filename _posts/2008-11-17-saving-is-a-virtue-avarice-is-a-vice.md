---
layout: post
title: Saving is a virtue, avarice is a vice
published: true
date: 2008-11-17
categories:
- 1rad
- Functional Test
- rails
- ruby
- Testing
---
<p>In functional tests a controller instance should not be reused: usually controller instances hold some state, that gets discarded in the normal Rails life cycle, and our tests must reflect that. Otherwise, tests can fail even if they shouldn't, but even worse tests could succeed when they shouldn't.</p>

<p>See this testcase:</p>

<div class="CodeRay">
  <div class="code"><pre>require File.dirname(__FILE__) + '/../test_helper'

class DuuController  &lt; ActionController::Base
  def index
    @test ||= params[:test]
    render :text =&gt; @test, :layout =&gt; false 
  end
end

class DontUseAControllerTwiceInATestcaseTest  &lt; ActionController::TestCase
  def setup
    @controller = DuuController.new
    ...
  end

  def test_with_strange_results
    get :index, :test =&gt; &quot;duu1&quot;
    assert_equal(&quot;duu1&quot;, @response.body)

    get :index, :test =&gt; &quot;duu2&quot;
    assert_equal(&quot;duu1&quot;, @response.body)    # !!Watch this!!!
  end
end</pre></div>
</div>


<p>This, of course, matches the Rails lifecycle of objects during an action: Rails creates a controller instance <em>for each incoming request</em>, and throws it away afterwards. This again matches the stateless behaviour of HTTP connections: each action response starts with a clean sheet. And wonderfully enough this ensures that all memory used by the response is cleaned up afterwards (unless you shoot yourself in our foot, of course, and if the ruby interpreter plays along nicely.)</p>

<p>If you need a series of simulated requests in a single test then you must recreate the @controller object from scratch:</p>

<div class="CodeRay">
  <div class="code"><pre>def test_duu2
  get :index, :test =&gt; &quot;duu1&quot;
  assert_equal(&quot;duu1&quot;, @response.body)

  @controller = DuuController.new

  get :index, :test =&gt; &quot;duu2&quot;
  assert_equal(&quot;duu2&quot;, @response.body)    # !!Watch this!!!
end</pre></div>
</div>


<p>To be honest I have no idea why rails best practices apparently recommend this. A simple change, and we could happily write</p>

<div class="CodeRay">
  <div class="code"><pre>class SmarterTestingTest  &lt; ActionController::TestCase
  testing :duu_controller
  ...
end</pre></div>
</div>


<p>No setup needed, at least not the boilerplate part. Remember DRY?</p>
