---
layout: post
title: The times they are achanging...
published: true
date: 2008-09-08
categories:
- Debugging Tools
- ruby
---
<p>...well, sometimes you just have to know how much time your application spends in a certain area.</p>

<p>We don't speak profiling here, profiling is an applied art and there are certainly enough tools around that do a wonderful job. But sometimes, you just want to know if one implementation variant is much faster than the other. Now of course you have Benchmark, but that is hard to write down. 'time' to the rescue:</p>


```ruby
time! { how_long_does_this_method_run? }
```


<p>which gives you a nice printout, but still returns the method's value, as if you would just have written:</p>


```ruby
how_long_does_this_method_run?
```


<p>You don't need the time output anymore? But still don't want to
erase the  <em>time! { ... }</em> part completely? Just change the exclamation
into a question mark:</p>

```ruby
time? { how_long_does_this_method_run? }
```


<p>And here is the implementation, part 1:</p>

```ruby
class Object
  def time?(msg=nil)
    yield
  end

  def time!(msg=nil)
    start = Time.now
    begin
      r = yield
      puts TimeFormattingHelper.format_time(msg, start)
      r
    rescue
      puts TimeFormattingHelper.format_time(msg, start, $!)
      raise
    end
  end
end
```


<p>Did I tell you, that you can use any custom message string you like? And the only thing
that still missing here is the TimeFormattingHelper, which could look like this - but feel free to adjust to your needs:</p>

```ruby
module Object::TimeFormattingHelper
  def self.format_time(msg, start, exception=nil)
    if exception
      msg += ": " if msg
      msg = "#{msg}Exception('#{exception.class}') after "
    else
      msg ||= "Timing: "
    end

    msg + format_duration(Time.now - start)
  end

  def self.format_duration(dur)
    hours = Integer(dur/3600)
    mins = Integer(dur/60) % 60
    secs = Integer(dur) % 3600

    dur = if mins == 0  then sprintf "%.3f secs.", dur
    elsif hours == 0    then sprintf "%d:%02.3f mins.", mins, dur - 60 * mins
    else                     sprintf "%d:%02d:%02d h.", hours, mins, secs
    end
  end
end
```