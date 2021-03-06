---
layout: post
title: "Plugging Rack into Rails"
id: 4cf5b657dabe9d2c610000b9
updated: 2010-11-30T21:43:35-05:00
date: 2009-03-03T08:33:00-05:00
tags:
- code
redirect_from: /blog/archives/2009/03/03/plugging-rack-into-rails/
---

The interwebs are all a'buzz about [Rack](http://rack.rubyforge.org/). For those that haven't been following along, Rack is a *specification* and a *library* for connecting web servers to Ruby libraries (a.k.a. "Middleware"). It's basically the Web 2.0 version of CGI, except that it doesn't suck and it's just for Ruby.

Rails 2.3 has Rack baked in. It uses Rack for things like sessions and parameter parsing. But what if you want to add your own middleware to a Rails app?

It's really easy! In the `init.rb` of a plugin, or just in a Rails initializer:

{% highlight ruby %}
ActionController::Dispatcher.middleware.use MyMiddleware
{% endhighlight %}

This will append your middleware to the end of the "stack", so it will be executed after all of Rails' middleware, but before anything else in the Rails framework.

But what if you need to massage request parameters before Rails parses them? All you need to do is insert your middleware in the stack before Rails parses the params. You can find the current list of middleware by inspecting `ActionController::Dispatcher.middleware`. In there you'll find `ActionController::ParamsParser`, which is what we want to insert our middleware before. Unfortunately, there's not an `insert_before` method (yet), so we'll need to use `insert_after`, giving it a middleware earlier in the stack:

{% highlight ruby %}
ActionController::Dispatcher.middleware.insert_after 'ActionController::Failsafe', MyMiddleware
{% endhighlight %}

If you don't like the way that one of the existing pieces of middleware handles things, you can swap it out for your own version:

{% highlight ruby %}
ActionController::Dispatcher.middleware.swap 'Rails::Rack::Metal', HeavyMetal
{% endhighlight %}

So there you have it. Eat, drink, and go write middleware.
