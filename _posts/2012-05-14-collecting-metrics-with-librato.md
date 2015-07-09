---
layout: post
title: ! 'Collecting metrics with librato  '
published: true
date: 2012-05-14
categories: []
---
<p>@roidrage&rsquo;s presentation on metrics, measurements, and logging kept me thinking. At that point I had working metrics already, CPU, RAM, etc. are watched properly, and I would get warned when something bad happened. However, what I wanted on one of my application was application metrics.</p>

<p>This application is an update server for my <a href="http://bit.ly/kpilot">Kinopilot mobile app</a>. There is a job which runs daily and collects movie listings from various sources, enriches these with movie information etc, and packs this into a database. The Kinopilot app then fetches updates  from that server.</p>

<p>In my case I would like to see how many cinemas, movies, and listings are pulled and merged into the data source. Because: if the number of cinemas drops say, from 70 down to 10, there is probably something wrong with either the data or the import process. So seeing a nice graph would help.</p>

<p>Application metrics are probably a bit different from resource metrics. I could write a munin-plugin for sure, but then I would have to put on my &ldquo;root&rdquo; cap and install it. I like my application as self-contained as possible, and application metrics belong to the application domain, not the server administration domain.</p>

<p>Enter metrics.librato.com: @roidrage mentioned these guys along the lines of &ldquo;I don&rsquo;t want to sell you anything but this stuff is good&rdquo; a couple of times. So, yeah, why not give it a try? For starters, librato is close to free. Each measurement is US-$0.000002. There is a <a href="https://metrics.librato.com/pricing">calculator</a> which tells you how much it would cost depending on how much data you send. (<strong>Gotcha:</strong> once logged in you cannot see this page any longer). In my case: nothing. Nice.</p>

<p>Next step: signup, get API credentials. Works flawlessly, and even looks nice. Good!</p>

<p>Next step: pull the gem <code>gem "librato-metrics"</code>, browse the examples, add measurements to my update script. The gem offers a nice interface to the librato-metrics service, and yes, it just works. Nice thing: when you send a piece of data to a metric, then this metric comes into existence, if it doesn&rsquo;t exist.</p>

<p>So I add some measurements to my update script: time needed for an update, number of cinemas, movies, and listings found during the update. Now running the update&hellip; Oh boy, why is this so slow? But hey, there my data is, right on the website. Which is split into <em>Metrics</em>, <em>Instruments</em>, <em>Dashboards</em>. Here they are. In <em>Metrics</em>. This is just too easy.</p>

<p>But delivering each measurement taking ~500 msecs? (Note: my server is in Germany. Theirs probably not.) Ok, the gem offers a queueing option. Which is nice. Now I queue my measurements, run the script &ndash; fast as hell, as before &ndash; and then, &hellip;, no data. Sure, one probably has to flush the queue manually. No problem for me:</p>

<div class="CodeRay">
  <div class="code"><pre>at_exit do
  $librato_metrics_queue.submit
end</pre></div>
</div>


<p>The <code>at_exit</code> handler nicely flushes the queue. This works nice for a run-once script situation; I wouldn&rsquo;t want this inside a request/response cycle though. There is certainly room for improvement: just run the queue in a separate thread.</p>

<p>Still, I don&rsquo;t have a clue why delivering the data takes that long. Authentication is reasonably fast (~50 msecs), so this is certainly not a network issue. For some reason metrics.librato.com takes too long to respond, and the Librato::Metrics code works synchronously and blocks until it sees the response (which I don&rsquo;t even use)</p>

<p>Now, back to the gui. Once you have data you start building &ldquo;Instruments&rdquo;. Which is quite nice: just choose some of your metrics to add to your instrument. This works best when setting some properties for your metrics, like label, and min-value. Tip: In my case the label is <code>'#'</code> for cinemas, movies, and listings alike. Which is which is color-coded in the instruments and can be seen easily by just hovering the mouse.</p>

<p>(<strong>Gotcha</strong>: you have to <em>Save</em> the changes, and the Save button is on the bottom right of the page. On the other hand, when you save some metric properties, the Save button is on the bottom left of the page.)</p>

<p>Now head over to Dashboards: like in a car a dashboard is just a collection of instruments. You create a dashboard, add a couple of instruments, save it, and done. (<strong>Gotcha</strong>: the Save confirmation &ndash; with its Growl-like look IMHO out-of-place in a web app, but that is probably just a matter of taste &ndash; obscures the &ldquo;Dashboard&rdquo; menu point.) There are not many options in how to build the Dashboard. I would like to have one row of 3 instruments, and one row with one instrument spanning the entire page, but apparently this is not possible.</p>

<p>When done, you just click your dashboard, choose a time frame in the upper right, and see your data. I am said that this updates itself in realtime (or whatever passes as realtime these days.) A few points here, though:</p>

<ul>
<li>It seems impossible to share the dashboard with someone else, without giving out my account credentials.</li>
<li>The dashboard forgets the time frame entered. In my case, the default time frame (last 5 mins, if I  remember right) just doesn&rsquo;t make sense; so it would be nice if the dashboard would remember it.</li>
<li>And a future improvement: instruments that span different timespans. I could probably go with my application metrics as they are now for the last 4 weeks or so, and, say, number of update requests on a finer scale.</li>
</ul>


<p>metrics.librato.com: All in all a nice experience. A few improvements, mainly in the GUI, and this is my metrics service for the foreseeable future.</p>

<p>PS: one more addition: I really would like an &ldquo;instrument&rdquo;, which just gives me the latest readings for one or more specific metrics, as numbers, in a nice, large font.</p>
