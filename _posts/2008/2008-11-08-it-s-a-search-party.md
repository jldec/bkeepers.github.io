---
layout: post
title: "It's a search party!"
id: 4cf5b65adabe9d2c6100011f
updated: 2010-11-30T21:43:38-05:00
date: 2008-11-08T15:03:00-05:00
tags:
- code
redirect_from: /blog/archives/2008/11/08/it-s-a-search-party/
---

While chatting with Dr. [John Nunemaker](http://railstips.org) at [RubyConf](http://rubyconf.org), I realized that I have several problems. Ignoring the many character flaws that are beyond the scope of this post, my problems are:

1.  I tag things I find useful on [Delicious](http://delicious.com/bkeepers). But I rarely look back to delicious because it's just easier to search Google, resorting to Delicious if I can't find something that I remember tagging.
2.  It's easier to search Google because not everything that I find useful is in Delicious. I don't want to have to think about *where* useful things are, I just want to search for them.

I want a search engine that prioritizes things that I've found useful in the past. Ideally, google would let me tag things and take that into account when calculating page rank. But, in the mean time…

### Let's have a search party!

I threw together a [simple search interface](http://search.collectiveidea.com) that pulls in results from Google, Delicious, and just for fun, [GitHub](http://github.com). It shows primarily Google results, but then in the sidebar, it shows results from Delicious, with my bookmarks highlighted at the top, and also results from GitHub. [Check it out.](http://search.collectiveidea.com)

<div class="figure">
<a href="http://search.collectiveidea.com"><img src="http://opensoul.org/assets/2008/11/8/Picture_3.png" title="SearchParty" /></a>

</div>
And if you use Firefox, you can add it as your search provider:

<div class="figure">
<img src="http://opensoul.org/assets/2008/11/8/searchparty-opensearch.png" title="Firefox Search Provider" />

</div>
It is an extremely simple app that has 2 pages and uses JavaScript to read in JSON from all of the services. There is one Ruby class that does screen-scraping since Delicious doesn't provide an API to their full search.

I first implemented it in [Merb](http://merbivore.com), but upon realizing how simplistic it is, I switched it over to [Sinatra](http://sinatra.rubyforge.org). And I used [jQuery](http://jquery.com) to do the JSON and other JavaScripty goodness. Thanks to [Mark Van Holstyn](http://mutuallyhuman.com/) for helping implement it.

The code is available on [GitHub](http://github.com/collectiveidea/searchparty), so check it out, fork it, and make it more awesome.

I will be making a few other posts about specific things I learned when building this, including deploying sinatra apps and using jQuery to do JSON with Merb and Sinatra.
