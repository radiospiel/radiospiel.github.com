---
layout: post
title: Add support for greylisted users to your site
published: true
date: 2009-08-26
tags:
- infrastructure
---
<p>In the ever ongoing fight against spam there is one really wonderful weapon: greylisting. For those that don't know how it works: whenever an email server sends an email for the first time, the receiving mail server rejects the email with a temporary error. The idea being that a legimitate server resends the email after a certain period of time, and then the email gets through, but a spam sender is likely not to resend the mail again.</p>

<p>Which works like a charm - I have a spam ratio of less than 1% since I enabled grey listing, and that without any spam classification on my servers - unless... you are registering at a new web service. In which case the confirmation email doesn't get through the first time, me having to wait for some indeterminate time. On a countless number of services I just decided that the wait was not worth my time...</p>

<p>If you want to give me and other grey listers a smooth user experience consider the following:</p>

<ul>
<li>reduce the grace period from one hour (which is quite likely the default value in your Linux distribution too) to a value around 5 minutes, and/or</li>
<li>modify your application that a user, which is registered, may use the service for a certain time without confirming the confirmation email.</li>
</ul>
<p>I understand that the second option is some work and might not always possible; but the first option is just a simple configuration setting and should take your system admin no longer than 5 minutes.</p>
