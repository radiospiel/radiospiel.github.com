---
layout: post
title: Web site localization the easy way
published: true
date: 2009-11-24
categories:
- i18n
- internationalization
- javascript
- jquery
- jquery.ez
- l10n
- localization
---

<div><ul>
<li class="toc-h2"><a href="#markup_for_easy_l10n">Markup for easy l10n</a></li>
<li class="toc-h2"><a href="#choosing_a_language">Choosing a language</a></li>
<li class="toc-h2"><a href="#css_changes_">CSS Changes </a></li>
<li class="toc-h2"><a href="#pros_cons">Pros &amp; Cons</a></li>
</ul></div>


<p>There are many ways to add multiple languages to your web site. You will certainly
know how you do it. But still... I came up with <a href="/projects/jquery.ez/jquery.ez.js">a new alternative</a>,
which I see as a light weight alternative, suitable especially for small web sites.</p>

<a name="markup_for_easy_l10n"></a><h2>Markup for easy l10n</h2>

<p>My solution uses the "lang" attribute, which is part of the HTML4 specification. You mark
up "alternative" content of your website with the lang attribute, in the following way:</p>

<div class="CodeRay">
  <div class="code"><pre>&lt;p&gt;This is the base language content&lt;/p&gt;
&lt;p lang=&quot;de&quot;&gt;Das ist der Inhalt in deutsch&lt;/p&gt;</pre></div>
</div>


<p>and may do so multiple times on a web page. The only rule: the pieces of alternate content
must be <em>siblings</em>. On page load a small piece of javascript takes over and chooses which
part of the content to show, and hides all other content. A sample implementation (as a
jQuery plugin) can be found here. The code honors node names (&lt;p&gt;, &lt;div&gt;, etc.)
and class names to check for matching siblings. So this would work fine:</p>

<div class="CodeRay">
  <div class="code"><pre>&lt;div&gt;
  &lt;p class=&quot;first&quot;&gt;First paragraph&lt;/p&gt;
  &lt;p class=&quot;first&quot; lang=&quot;de&quot;&gt;Erster Absatz&lt;/p&gt;
  &lt;p class=&quot;second&quot;&gt;Second paragraph&lt;/p&gt;
  &lt;p class=&quot;second&quot; lang=&quot;de&quot;&gt;Zweiter Absatz&lt;/p&gt;
&lt;/div&gt;</pre></div>
</div>


<p>while <strong>the following (currently) does not:</strong></p>

<div class="CodeRay">
  <div class="code"><pre>&lt;div&gt;
  &lt;p&gt;First paragraph&lt;/p&gt;
  &lt;p lang=&quot;de&quot;&gt;Erster Absatz&lt;/p&gt;
  &lt;p&gt;Second paragraph&lt;/p&gt;
  &lt;p lang=&quot;de&quot;&gt;Zweiter Absatz&lt;/p&gt;
&lt;/div&gt;</pre></div>
</div>


<a name="choosing_a_language"></a><h2>Choosing a language</h2>

<p>To select a specific language use the following:</p>

<div class="CodeRay">
  <div class="code"><pre>jQuery.ez_localize(&quot;body&quot;, &quot;de&quot;);</pre></div>
</div>


<p>The first parameter defines the scope for localization. It defaults to "body". To
skip the localization code completely just run <em>jQuery.ez_localize(null)</em>. The second
parameter defines the language. The default is <em>null</em>, which selects the "base language",
i.e. those parts of the page without a "lang" attribute.</p>

<p>To set the language at a later time just use the same piece of code again. This way your users
can switch between languages at any time.</p>

<a name="css_changes_"></a><h2>CSS Changes </h2>

<p>To hide the extra content even if the user has Javascript
turned off you must add a</p>

<div class="CodeRay">
  <div class="code"><pre>[lang] {
  display: none;
}</pre></div>
</div>


<p>rule to your stylesheet. This is a CSS3 selector - I honestly don't know which browsers do implement this already.</p>

<a name="pros_cons"></a><h2>Pros &amp; Cons</h2>

<p>Pros:</p>

<ul>
<li>Easily deployed: just add content in the alternative languages of your choice: if you
can modify the HTML content on your site, you can add translations with this.</li>
<li>One document to bind them all: don't change the overall structure of your site,
don't change URLs.</li>
<li>Change the language in the browser: no server traffic</li>
</ul>
<p>Cons:</p>

<ul>
<li>Only a light weight solution.</li>
<li>One document binds them all: this is not how the web usually works. Usual best practices
are different.</li>
</ul>