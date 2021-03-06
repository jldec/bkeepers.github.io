---
layout: post
title: "Obscure RubyGems Error"
id: 4cf5b660dabe9d2c61000190
updated: 2010-11-30T21:43:44-05:00
date: 2008-06-22T22:15:00-04:00
tags:
- code
redirect_from: /blog/archives/2008/06/22/obscure-rubygems-error/
---

A week or so ago my `gem` command magically stopped working. Honestly, it was magic! There is no other logical explanation. The fetcher's socket marshaled all over, spewing this error:

{% highlight bash %}
$ gem --version
1.1.0
$ sudo gem update --system
Updating RubyGems
ERROR:  While executing gem ... (Gem::RemoteFetcher::FetchError)
    Hostname not known: gems.rubyforge.org (SocketError)
	getting size of http://gems.rubyforge.org/Marshal.4.8
{% endhighlight %}

Googling different parts of that error didn't yield anything useful. Lo, I post my solution in case some other poor soul has the same problem. Upgrade to 1.2.0.

Since `gem upgrade --system` is broken, download the [rubygems-update](http://rubyforge.org/frs/download.php/38645/rubygems-update-1.2.0.gem) manually.

{% highlight bash %}
$ sudo gem install ~/path/to/rubygems-update-1.2.0.gem
$ sudo update_rubygems
{% endhighlight %}

Ah, all better. Oh, by the way, rubygems 1.2 rocks!
