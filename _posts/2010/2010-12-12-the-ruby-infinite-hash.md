---
layout: post
title: "The Ruby Infinite Hash"
id: 4cfafe21dabe9d3fcb000274
updated: 2010-12-12T23:51:13-05:00
date: 2010-12-12T21:45:00-05:00
tags:
- code
redirect_from: /blog/archives/2010/12/12/the-ruby-infinite-hash/
---

[@tenderlove](http://twitter.com/tenderlove/status/5687291469107200) shows us ruby magic to create an infinite hash:

{% highlight ruby %}
>> hash = Hash.new { |h, k| h[k] = Hash.new(&h.default_proc) }
 => {}
>> hash['a'] = 1
 => 1
>> hash['a']
 => 1
>> hash['b']
 => {}
>> hash['b']['c']['d']
 => {}
{% endhighlight %}
