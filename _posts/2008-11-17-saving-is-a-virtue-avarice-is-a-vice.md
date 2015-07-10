---
layout: post
title: Saving is a virtue, avarice is a vice
published: true
date: 2008-11-17
tags:
- ruby
- testing
---
<p>In functional tests a controller instance should not be reused: usually controller instances hold some state, that gets discarded in the normal Rails life cycle, and our tests must reflect that. Otherwise, tests can fail even if they shouldn't, but even worse tests could succeed when they shouldn't.</p>

<p>See this testcase:</p>

```ruby
require File.dirname(__FILE__) + '/../test_helper'

class DuuController  < ActionController::Base
  def index
    @test ||= params[:test]
    render :text => @test, :layout => false 
  end
end

class DontUseAControllerTwiceInATestcaseTest  < ActionController::TestCase
  def setup
    @controller = DuuController.new
    ...
  end

  def test_with_strange_results
    get :index, :test => "duu1"
    assert_equal("duu1", @response.body)

    get :index, :test => "duu2"
    assert_equal("duu1", @response.body)    # !!Watch this!!!
  end
end
```

<p>This, of course, matches the Rails lifecycle of objects during an action: Rails creates a controller instance <em>for each incoming request</em>, and throws it away afterwards. This again matches the stateless behaviour of HTTP connections: each action response starts with a clean sheet. And wonderfully enough this ensures that all memory used by the response is cleaned up afterwards (unless you shoot yourself in our foot, of course, and if the ruby interpreter plays along nicely.)</p>

<p>If you need a series of simulated requests in a single test then you must recreate the @controller object from scratch:</p>

```ruby
def test_duu2
  get :index, :test => "duu1"
  assert_equal("duu1", @response.body)

  @controller = DuuController.new

  get :index, :test => "duu2"
  assert_equal("duu2", @response.body)    # !!Watch this!!!
end
```

<p>To be honest I have no idea why rails best practices apparently recommend this. A simple change, and we could happily write</p>

```ruby
class SmarterTestingTest  < ActionController::TestCase
  testing :duu_controller
  ...
end
```

<p>No setup needed, at least not the boilerplate part. Remember DRY?</p>
