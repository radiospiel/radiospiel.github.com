---
layout: post
title: Web site localization the easy way
published: true
date: 2009-11-24
tags:
- infrastructure
- javascript
---

There are many ways to add multiple languages to your web site. You will certainly
know how you do it. But still... I came up with <a href="/projects/jquery.ez/jquery.ez.js">a new alternative</a>,
which I see as a light weight alternative, suitable especially for small web sites.


- <a href="#markup_for_easy_l10n">Markup for easy l10n</a>
- <a href="#choosing_a_language">Choosing a language</a>
- <a href="#css_changes_">CSS Changes </a>
- <a href="#pros_cons">Pros &amp; Cons</a>

<a name="markup_for_easy_l10n"></a><h2>Markup for easy l10n</h2>

My solution uses the "lang" attribute, which is part of the HTML4 specification. You mark
up "alternative" content of your website with the lang attribute, in the following way:

```html
<p>This is the base language content</p>
<p lang="de">Das ist der Inhalt in deutsch</p>
```

and may do so multiple times on a web page. The only rule: the pieces of alternate content
must be <em>siblings</em>. On page load a small piece of javascript takes over and chooses which
part of the content to show, and hides all other content. A sample implementation (as a
jQuery plugin) can be found here. The code honors node names (`<p>`, `<div>`, etc.)
and class names to check for matching siblings. So this would work fine:

```html
<div>
  <p class="first">First paragraph</p>
  <p class="first" lang="de">Erster Absatz</p>
  <p class="second">Second paragraph</p>
  <p class="second" lang="de">Zweiter Absatz</p>
</div>
```

while the following (currently) does not:

```html
<div>
  <p>First paragraph</p>
  <p lang="de">Erster Absatz</p>
  <p>Second paragraph</p>
  <p lang="de">Zweiter Absatz</p>
</div>
```


<a name="choosing_a_language"></a><h2>Choosing a language</h2>

<p>To select a specific language use the following:</p>

```
jQuery.ez_localize("body", "de");
```


<p>The first parameter defines the scope for localization. It defaults to "body". To
skip the localization code completely just run <em>jQuery.ez_localize(null)</em>. The second
parameter defines the language. The default is <em>null</em>, which selects the "base language",
i.e. those parts of the page without a "lang" attribute.</p>

<p>To set the language at a later time just use the same piece of code again. This way your users
can switch between languages at any time.</p>

<a name="css_changes_"></a><h2>CSS Changes </h2>

<p>To hide the extra content even if the user has Javascript
turned off you must add a</p>

```css
[lang] {
  display: none;
}
```


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
