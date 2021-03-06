---
layout: post
title: "Fixing Range#step"
id: 4cf5b677dabe9d2c61000472
updated: 2010-11-30T21:44:07-05:00
date: 2007-02-13T18:20:00-05:00
tags:
- code
redirect_from: /blog/archives/2007/02/13/fixing-range-step/
---

**Update**: This has been [added to Rails' active\_support](http://dev.rubyonrails.org/changeset/7800).

My [last post](/2007/2/13/ranges-include-or-overlap-with-ranges) got me thinking about my post a couple months ago about [being annoyed that <code>Range\#step</code> requires a block and returns itself](http://opensoul.org/2006/12/12/why-does-step-return-itself).

Well, it's Ruby. If you don't like it, change it:

{% highlight ruby %}
class Range
  alias_method :original_step, :step
  def step(value, &block)
    if block_given?
      original_step(value, &block)
    else
      returning [] do |array|
        original_step(value) {|step| array << step }
      end
    end
  end
end
{% endhighlight %}

{% highlight ruby %}
(0..10).step(2)                 #=> [0, 2, 4, 6, 8, 10]
(0..10).setp(2) {|i| puts i }   #=> (0..10)
{% endhighlight %}

Now, before you start yelling at me for being an irresponsible programmer, may I remind you that this change and the ones in my previous post in no way change the original functionality of Range. <code>step</code> raises an error if no block is given, and <code>include?</code> simply returns false if a range is passed (you can't have a Range of Ranges, so previously, a range would never include another Range).

I think I may submit a patch for ActiveSupport with these little nuggets.

**update**: test your code before you publish it. Code has been fixed so it **really** doesn't have any adverse side-effects.
