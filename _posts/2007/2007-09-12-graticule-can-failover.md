---
layout: post
title: "Graticule can failover"
id: 4cf5b669dabe9d2c61000295
updated: 2010-11-30T21:43:53-05:00
date: 2007-09-12T20:38:00-04:00
tags:
- code
redirect_from: /blog/archives/2007/09/12/graticule-can-failover/
---

[Graticule](http://graticule.rubyforge.org/) has a new geocoder that simply calls other geocoders in succession until it gets a result.

{% highlight ruby %}
geocoder = Graticule.service(:multi).new(
  Graticule.service(:google).new("api_key"),
  Graticule.service(:yahoo).new("api_key"),
)
geocoder.locate '49423' # <= tries geocoders in succession
{% endhighlight %}

This can also be used in [acts\_as\_geocodable](http://graticule.rubyforge.org/plugin.html) by simply setting `Geocode.geocoder` to the new multi-geocoder. See the [API docs](http://graticule.rubyforge.org/graticule/classes/Graticule/Geocoder/Multi.html) for more information.

Thanks to [Matt King](http://www.mattking.org/2007-04/graticule-failover-geocoding.html) for some ideas on the implementation and [Ben Tucker](http://stream.btucker.org/) for nudging me to implement it.
