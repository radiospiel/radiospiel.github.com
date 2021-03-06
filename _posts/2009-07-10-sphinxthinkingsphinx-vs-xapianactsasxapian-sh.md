---
layout: post
title: Sphinx/ThinkingSphinx vs Xapian/ActsAsXapian shootout
published: true
date: 2009-07-10
tags:
- databases
- performance
- ruby
---
<p>This benchmark compares thinking_sphinx with acts_as_xapian. We need a search engine that gives us the IDs of matching documents from a fulltext index, basic text search only.</p>

<h2>Data</h2>

<ul>
<li>one table with 200k entries with 5k of text (avg) in one column</li>
<li>one table with 500k entries with 7k of text (avg) in 6 columns</li>
<li>one table with 500k entries with 7k of text (avg) in 4 columns</li>
</ul>
<h2>Indexing</h2>

<p>Initial indexing took 10 mins with thinking_sphins and 75 mins(!!) on acts_as_xapian</p>

<h2>Search performance</h2>

<p>The search performance on queries that return only a few items is nearly identical.</p>

<p>The search performance on queries that return many items (~10000) is nearly
identical, 90% of the time is spend in ActiveRecord.</p>

<p>In our case - we only need IDs and not the entire documents - sphinx runs
at 0.6 secs for a particular query (with 10000 results),
where acts_as_xapian needs 4.5 secs. This is because thinking_sphinx allows
you to only fetch the ids, where acts_as_xapian insists of pulling the
models from the database. When patching acts_as_xapian to allow for pulling
ids only, we land at 0.6 vs 0.4 secs.</p>

<h2>Results</h2>

<p>We will choose sphinx because</p>

<ul>
<li>it is similarily fast to xapian</li>
<li>runs over the network by default</li>
<li>Indexing is way faster (I guess because acts_as_xapian pulls all data to be index from the database to hand it over, while sphinx can do that itself)</li>
<li>acts_as_xapian would need to be patched for performance reasons.</li>
</ul>
<h2>And here you find the related tools:</h2>

<ul>
<li><a href="http://xapian.org/">Xapian</a></li>
<li><a href="http://github.com/frabcus/acts_as_xapian/tree/master">acts_as_xapian</a></li>
<li><a href="http://sphinxsearch.com/">Sphinx</a></li>
<li><a href="http://freelancing-god.github.com/ts/en/">thinking_sphinx</a></li>
</ul>
