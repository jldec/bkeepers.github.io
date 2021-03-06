---
layout: post
title: "Sinatra serenades Rails 2"
id: 4d01ad2fdabe9d3295000001
updated: 2010-12-10T01:00:05-05:00
date: 2010-12-10T00:54:00-05:00
tags:
- code
redirect_from: /blog/archives/2010/12/10/sinatra-serenades-rails-2/
---

It turns out it's really easy to mount a Sinatra application into your Rails 2 app (yes, there are still a few Rails 2 apps out there). Here's how.

First, declare Sinatra as a gem dependency in `config/environment.rb`:

{% highlight ruby %}
config.gem 'sinatra', :lib => 'sinatra/base'
{% endhighlight %}

<aside>
The `:lib` option is somewhat important. Without it, [Sinatra will pollute the `Object` namespace](https://github.com/sinatra/sinatra/blob/master/lib/sinatra/main.rb#L28), and you'll spend 3 hours trying to figure out why calling `#set` on your memcached client returns an instance of `Sinatra::Application`. Not that I'm bitter or anything…

</aside>
Next, create a directory to put Sinatra apps in and add them to the Rails load path:

{% highlight ruby %}
config.autoload_paths << Rails.root.join("app/sinatra")
{% endhighlight %}

<aside>
If you're using any version of Rails before 2.3.10, the config option will be `load_paths` instead of `autoload_paths`.

</aside>
Then, define a Sinatra app in a file in `app/sinatra`:

{% highlight ruby %}
class TheJsonApi < Sinatra::Base
  before { content_type(:json) }

  get '/v2/foo' do
    {:foo => 'bar'}.to_json
  end
end
{% endhighlight %}

And finally, insert your Sinatra app as middleware:

{% highlight ruby %}
config.after_initialize do
  config.middleware.insert_before("ActionController::Failsafe", "TheJsonApi")
end
{% endhighlight %}

Sinatra, being the classy framework that it is, is smart enough to act as a Rack middleware OR an endpoint ([what's the difference?](http://intridea.com/2010/4/20/rack-middleware-and-applications-whats-the-difference?blog=company)). If you use it as a middleware, it will serve any routes that it knows about and pass on any that it doesn't.

Why would you use Rails and Sinatra?
------------------------------------

I'm working on large Rails application that consists mostly of an XML API. We're working on a new version of the API with some significant changes. Since the new API will only have 7-10 endpoints, and only return JSON, we are thinking it will just be cleaner to build it as a Sinatra app. It will be easier to document and easier to maintain. And we are embedding it in the Rails app so it can make use of our existing models.
