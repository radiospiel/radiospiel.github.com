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

<div class="CodeRay">
  <div class="code"><pre>time! { how_long_does_this_method_run? }</pre></div>
</div>


<p>which gives you a nice printout, but still returns the method's value, as if you would just have written:</p>

<div class="CodeRay">
  <div class="code"><pre>how_long_does_this_method_run?</pre></div>
</div>


<p>You don't need the time output anymore? But still don't want to
erase the  <em>time! { ... }</em> part completely? Just change the exclamation
into a question mark:</p>

<div class="CodeRay">
  <div class="code"><pre>time? { how_long_does_this_method_run? }</pre></div>
</div>


<p>And here is the implementation, part 1:</p>

<div class="CodeRay">
  <div class="code"><pre>class Object
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
end</pre></div>
</div>


<p>Did I tell you, that you can use any custom message string you like? And the only thing
that still missing here is the TimeFormattingHelper, which could look like this - but feel free to adjust to your needs:</p>

<div class="CodeRay">
  <div class="code"><pre>module Object::TimeFormattingHelper
  def self.format_time(msg, start, exception=nil)
    if exception
      msg += &quot;: &quot; if msg
      msg = &quot;#{msg}Exception('#{exception.class}') after &quot;
    else
      msg ||= &quot;Timing: &quot;
    end

    msg + format_duration(Time.now - start)
  end

  def self.format_duration(dur)
    hours = Integer(dur/3600)
    mins = Integer(dur/60) % 60
    secs = Integer(dur) % 3600

    dur = if mins == 0  then sprintf &quot;%.3f secs.&quot;, dur
    elsif hours == 0    then sprintf &quot;%d:%02.3f mins.&quot;, mins, dur - 60 * mins
    else                     sprintf &quot;%d:%02d:%02d h.&quot;, hours, mins, secs
    end
  end
end</pre></div>
</div>
