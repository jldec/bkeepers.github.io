---
layout: post
title: "Why does #step return itself?"
id: 4cf5b67cdabe9d2c61000510
updated: 2010-11-30T21:44:12-05:00
date: 2006-12-11T23:33:00-05:00
tags:
- code
redirect_from: /blog/archives/2006/12/11/why-does-step-return-itself/
---

**update:** [problem solved!](/2007/2/13/fixing-range-step)

Today, I wanted to get an array of numbers from 5 to 250, incrementing by 5. You'd think this would be a perfect job for <code>Range\#step</code> or <code>Number\#step</code>, but strangely enough, both of them return <code>self</code>:

{% highlight ruby %}
(5..250).step(5) { } #=> 5..250
5.step(250, 5) { } #=> 5
{% endhighlight %}

I cannot figure out why in the world step would return <code>self</code>. The other annoyance is that <code>step</code> must receive a block, but, given it's return value, I understand why.

So, to accomplish my original goal, I could make use of <code>\#step</code> by creating a temporary variable:

{% highlight ruby %}
result = []
(5..250).step(5) {|n| result << n }
{% endhighlight %}

But, it feels unnatural in Ruby to create temporary variables, so I ended ditching <code>\#step</code>:

{% highlight ruby %}
(5..250).select {|n| n % 5 == 0 }
{% endhighlight %}
