---
layout: post
title: ! 'nginx: Adding expiration headers'
published: true
date: 2009-11-13
categories:
- assets
- nginx
- rails
---
<p>Today I was confronted with the tas of setting a proper expiration header to
asset files. As a reminder: this is the aim:</p>

<ul>
<li>when requesting an existing file with an additional timestamp
parameter (i.e. "?123") add an expiration header.</li>
<li>when requesting an existing file without an additional timestamp
parameter don't add an expiration header.</li>
<li>when requesting a non-existing file pass on the request to the passenger application.</li>
</ul>
<p>After surfing the web for ages I found no working solution. In any case, the
following did the trick. Read below for an explanation and how I checked this:</p>

<div class="CodeRay">
  <div class="code"><pre># Server settings. server_name and path should match.
server {
  listen 80;
  server_name server.dev;
  root /Users/me/sites/server/public;
  index index.html index.htm;

  error_page   500 502 503 504  /50x.html;
  location = /50x.html {
    root   html;
  }

  sendfile on;

  location / {
    if (-f $request_filename) { 
        set $file X;
    }

    if ($args ~* ^[0-9]+$) {
        set $file &quot;${file}C&quot;;
    }

    # existing and can be kept by the client?
    if ($file = &quot;XC&quot;) {
        expires 365d;    
    }

    # Doesn't exist? Go to rails app
    passenger_enabled on;
    rails_env production;
  }
}</pre></div>
</div>


<p>This takes the following into account:</p>

<ol>
<li>The location value doesn't include the request query parameter</li>
<li>This solution adds expiration headers to all requests where a file
exists, not only to the "classical" asset requests.</li>
<li>For some reason that I don't understand I had to put
the "passenger_enabled" and "rails_env"
<strong>inside</strong> the location block. Otherwise the rails application would
never pick up any request with a file extension.</li>
</ol>
<p>After I was done I found an apparently working configuration with a different
(and I must say more elegant) approach in the comment
section <a href="http://craigjolicoeur.com/blog/setting-static-asset-expires-headers-with-nginx-and-passenger">over at Craig's</a>.
It is sad you find the good stuff <strong>only</strong> after banging your head for hours :)</p>

<p>And this is how I tested it:</p>

<ol>
<li>
<p>Get the <em>robots.txt</em> file, it must not contain an "Expires" header:</p>

<div class="CodeRay">
  <div class="code"><pre>~/site&gt; curl -I http://server.dev/robots.txt
 HTTP/1.1 200 OK
 Server: nginx/0.7.61
 ...</pre></div>
</div>

</li>
<li>
<p>Get the <em>robots.txt</em> file, with a time stamp; it must contain an "Expires" header:</p>

<div class="CodeRay">
  <div class="code"><pre>~/site&gt; curl -I http://server.dev/robots.txt?1234
 HTTP/1.1 200 OK
 Server: nginx/0.7.61
 Date: Fri, 13 Nov 2009 00:01:23 GMT
 Last-Modified: Sat, 08 Aug 2009 20:28:57 GMT
 Expires: Sat, 13 Nov 2010 00:01:23 GMT
 ...</pre></div>
</div>

</li>
<li>
<p>Get a dynamically created javascript file; it must not contain an "Expires"
header, but must refer to Phusion Passenger in the "Server" header, and
this regardless of whether or not a timestamp is set:</p>

<div class="CodeRay">
  <div class="code"><pre>~/sites/whispler&gt; curl -I http://server.dev/xx.js?1234
  ...
  Server: nginx/0.7.61 + Phusion Passenger 2.2.5 (mod_rails/mod_rack)

  ~/sites/whispler&gt; curl -I http://server.dev/xx.js
  ...
  Server: nginx/0.7.61 + Phusion Passenger 2.2.5 (mod_rails/mod_rack)</pre></div>
</div>

</li>
</ol>
