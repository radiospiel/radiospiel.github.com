---
layout: post
title: ! 'mongrel_rails restart: It might not work well.'
published: true
date: 2009-08-10
categories: []
---
<p>Just found out that mongrel_rails restart does <strong>NOT</strong> work, when the original mongrel was started with the <em>-c</em> option. Yuk!</p>

<p>Well, this is not entirely true. It works if the -c parameter contains an absolute path, or refers "to itself": <em><em>-c ../current</em></em> works like a charm, and in fact updates the current directory to whereever current points currently.</p>

<p>However, I could not get <em>mongrel_rails restart --soft</em> working....</p>
