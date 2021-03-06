---
layout: post
title: "Daily WTF: NilClass#method_missing"
id: 4cf5b662dabe9d2c610001f5
updated: 2010-11-30T21:43:46-05:00
date: 2008-04-18T03:14:00-04:00
tags:
- code
redirect_from: /blog/archives/2008/04/18/daily-wtf-nilclass-method_missing/
---

> With great power comes great responsibility.

I've been leading Ruby and Rails training over the last couple weeks for a company in Brisbane, Australia, Xerox in El Segundo, CA, and yellowpages.com in Glendale, CA. Visiting companies is always so interesting because you get to see how different people work together and how they go about solving problems. You also get to see some really interesting code.

I was helping one of the companies walk through some code they inherited from an acquisition and came across something similar to this wonderful snippet of code:

{% highlight ruby %}
  class NilClass
    def method_missing(method, *args)
      raise(NoMethodError, "undefined method '#{method}' for nil")
    rescue => e
      CustomLogger.error(e)
      nil
    end
  end
{% endhighlight %}

WTF? They're overriding `#method_missing` on `nil` to raise an exception, they then proceed to rescue it, log the error, and return `nil`!

I'm guessing they had some buggy code that was calling a method on `nil`, causing a `NoMethodError` to be raised. And instead of fixing the code, they just decided to change it so that you could call methods on `nil` and it would just silently fail.

{% highlight ruby %}
>> nil.foo.bar.baz
=> nil
{% endhighlight %}

It took me a while to figure out why they were raising an error and then rescuing it, but I'm guessing it's so they have access to a stack trace in the logger.

I'll give them credit for being creative, but whoever wrote that piece of code doesn't deserve to be using Ruby. Yes, Ruby's open classes are powerful, but that's just ridiculous.
