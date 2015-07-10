---
layout: post
title: How to setup a twitter application
published: true
date: 2012-10-12
tags:
- infrastructure
---
<ol>
<li>Go to <a href="https://dev.twitter.com">https://dev.twitter.com</a>, and sign in with your name and password.</li>
<li>Navigate to <a href="https://dev.twitter.com/apps/new">https://dev.twitter.com/apps/new</a>.</li>
<li>Enter name, description, website, and a callback URL. In my code the callback URL
will always be set from the code &ndash; I have no idea if this setting is important in that case.
However, there must be some entry.</li>
<li>Agree to the rules of the road. After all, this is Twitter country, where you are just a mere peasant.</li>
<li>Enter captcha to prove you are a peasant and not an agent of a higher power.</li>
<li>Repeat step 6, until you get the captcha right.</li>
<li>Twitter will now redirect you to <a href="https://dev.twitter.com/apps/NNNN/show">https://dev.twitter.com/apps/NNNN/show</a>, where you can review and edit more application details. It is probably a good idea to write down that URL.</li>
<li>The OAuth level is currently set to <em>&ldquo;Read-only&rdquo;</em>. This is ok for some applications &ndash; if you just need to identify a user of fetch some information from a user or from his/her timeline. If you want to be more active you need <em>&ldquo;Read and Write&rdquo;</em> access or even <em>&ldquo;Read, Write and Access direct messages&rdquo;</em>. You change this on the <strong>&ldquo;Settings&rdquo;</strong> tab.</li>
<li>Back to the <strong>&ldquo;Details&rdquo;</strong> tab.</li>
<li>If you want to use this &ldquo;application&rdquo; with a script you still need an access token. This will allow the script to interact with Twitter on behalf of your user account. Create this access token by clicking on &ldquo;Create my access token.&rdquo; on the bottom of the screen.</li>
<li>Now scribble down <em>Consumer key</em>, <em>Consumer secret</em>, <em>Access token</em> and <em>Access token secret</em>.</li>
</ol>


<p><strong>Note:</strong> a thing is not called &ldquo;secret&rdquo; by accident; it is called so to keep it secret.</p>
