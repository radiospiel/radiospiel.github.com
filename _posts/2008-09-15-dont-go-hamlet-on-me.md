---
layout: post
title: Don't go Hamlet on me!
published: true
date: 2008-09-15
tags:
- ruby
---
<p>When something's rotten in the State of Denmark - I prefer to know! If you feel the same, and you have some C/C++-background: chances are that you know about assertions.</p>

<p>For everybody else: C/C++ programs can usually be compiled in two different modi, debug and release: in debug mode the following C source code line</p>

```
assert(0 == 1)


<p>tests the condition "0 == 1", which is false even in C, and then aborts the program with a message like this:</p>

```
source.c(12): Assertion failed: 0 == 1


<p>In release mode, however, this statement is completely ignored and doesn't affect speed and size of the final binary. This is an invaluable tool for us coders: using assert you can test for consistentency of application internal data, and when releasing these tests are justed skipped.</p>

<h2>ruby it!</h2>

<p>Now how can we do something like this in ruby? Sure we can, well, almost. An assert() function should have the following features:</p>

<ol>
<li><p>In debug and test mode: assert() tests for an expression. If the expression evaluates to false it should print a message and abort.</p></li>
<li><p>The C/C++ variant is implemented using the C preprocessor, which is used (amongst other things) to turn the source code of the expression - i.e. 0 != 1 - into a string that can be used to display the message. As we don't have a preprocessor of that kind in ruby we cannot implement that behaviour. Instead we allow to customdefine a message.</p></li>
<li><p>The message printed should contain the source code position of the failed assertion.</p></li>
<li><p>In production mode we want to have the least possible impact on execution speed of the final application: therefore the assertion should be ignored, and both expression and message must not be evaluated.</p></li>
</ol>
<p>What could the interface of such an assert function look like? As 4) states we need some way to write down code that should or should not be evaluated depending on some global setting. One way to achieve this are code blocks. That code block has always to test for the condition in some way: what we could use is the block's return value. To set a message for the assertion from within the code block we could pass in some object that provides a method to do just that. In code, that would be</p>

```
assert do |a|
  a.message = "Something is strange here!"
  0 != 1
end


<p>In this example we define and pass around the message string even in release mode. It should not have much impact, because it is a string constant (and not a dynamic message a la "#{the_result_of_some_complicated_and_long_running_method()}")</p>

<p>The following syntax could allow this:</p>

```
assert  "Something is strange here!" do
  0 != 1
end


<p>or, somewhat more compact</p>

```
assert("Something is strange here!") { 0 != 1 }


<p>Avoiding a custom message and sticking to some default message this turns into:</p>

```
assert { 0 != 1 }


<p>which is pretty close to the C example above. This gives us the following interface for the assert() function:</p>

```
assert(msg=nil, &amp;block)


<p>with the block either having 0 or 1 arguments.</p>

<h2>Name it, but name it right!</h2>

<p>What I found out only after some time: as it turns out if you have the <em>rdebug</em> gem installed you already have an <em>assert</em> method. Therefore we need to have a different name. I decided to go for "assert!" instead: this name even shows that this function might possibly raise an Exception.</p>

<h2>Hold your horses...</h2>

<p>we still have to decide what happens when the assertion fails.</p>

<p>The application should abort when an assertion fails. In C this is usually done by calling exit() or a similar function which usually kills the running process. While we could do something like that - using Kernel#exit - but there is a better way: we can have assert throw an Exception - that usually leads to abortion as well, but allows to catch it and do something about it. So, this could be our Exception class:</p>

```
class Assertion < RuntimeError
  attr :message, true

  # Rubys default exception handler prints the string representation
  # of the exception object, i.e. the to_s return value: This is where
  # we define our message.
  def to_s
    @message ? "assertion failed: #{@message}" : "assertion failed."
  end
end


<p>We still need some object to pass into the code block. This must just hold the message attribute. For reasons of simplicity we can just use an Assertion object: remember that there is nothing special about Assertion (and RuntimeError) objects: they behave like any other object.</p>

<p>Another valuable addition would be a fail! method, that lets the assertion fail from within the code block. This lets you write code like this:</p>

```
assert! do |a|
  a.fail! if !condition1
  a.fail!("Hey that is strange") if !condition2
end


<p>Note that the codeblock in the above example will never return a value
different from nil: the assert function must not throw in that case.</p>

```
class Assertion < RuntimeError
  def fail!(message=nil)
    self.message = message
    raise self
  end
end


<p>Now that we have the interface it is time for our <em>assert()</em> implementations. The production mode implementation, of course, is simple, but the debug mode is not that complicated either. But where is the right place for the implementations? I decided to add them as methods to the Object class - this way they exist in each object, and we could extend our implementation to print information about the "self" object in the assert's context.</p>

```
class Object
  def _release_assert(message=nil, &amp;block)
  end

  def _debug_assert(message=nil, &amp;block)
    a = Assertion.new
    a.message = message
    a.fail! if yield(a) == false
  end

  alias_method :"assert!", :_release_assert
end


<p>The alias_method adds an "assert" alias for the release_assert method, which deactivates the assert functionality by default. We still need to add some method to enable it - a Assertion class method seems the logical place here:</p>

```
class Assertion
  def self.enable(flag)
    Object.send :alias_method, :"assert!", flag ? :_debug_assert : :_release_assert
  end
end


<p>Note the use of <em>Object.send :alias_method</em>: as <em>alias_method</em> is a private method we can not call it directly. For Rails users we add an <em>Assertion.init</em> function that uses the RAILS_ENV value to automatically determine the right mode. This way you only have to add Assertion.init to your environment.rb and are ready to run.</p>

<h3>...but where is Denmark?</h3>

<p>The last piece missing is to include the source code position in the output. This is pretty easy thanks to the RuntimeError#backtrace method, which returns an array containing the source code positions of the call stack:</p>

```
class Assertion < RuntimeError
  def to_s
    source_code = backtrace[some_index] # what is the index??
    @message ? "assertion failed: #{@message}" : "assertion failed."
  end
end


<p>Now we only have to know the correct index into that array. And while we could use the Assertion's backtrace in principal (as in the last code piece) we don't have control over when the Assertion is failed. If a user does something like:</p>

```
assert! do |a|
  some_array.each { |item| a.fail!("Some invalid item") if item.invalid }
  a.fail!("or fail always - for demonstration purposes...")
end


<p>these two failures would give different backtraces. Therefore we raise and catch an exception in assert to determine the source code position:</p>

```
def _debug_assert(message=nil, &amp;block)
  backtrace = raise rescue $!.backtrace
  a = Assertion.new
  a.message = message
  a.source = backtrace[1]
  a.fail! if yield(a) == false
end


<p>And voila! assert!() to the rescue!</p>
