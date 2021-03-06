---
layout: post
title: "Cucumber and Sunspot"
id: 4cf5b652dabe9d2c61000017
updated: 2010-12-01T01:22:42-05:00
date: 2010-04-07T09:53:00-04:00
tags:
- code
redirect_from: /blog/archives/2010/04/07/cucumber-and-sunspot/
---

If you haven't checked out [Sunspot](http://outoftime.github.com) yet, you should. I have tried every solution for doing real full-text searching in a Rails app. Sunspot, backed by solr, is the only one that I haven't had issues with in production. Hopefully I'll get around to posting more about my experience with it.

But for today, here's how I got Sunspot working with Cucumber.

First, add a configuration for the cucumber environment in `config/sunspot.yml`. I usually just use the same settings as test.

{% highlight yaml %}
test: &TEST
  solr:
    hostname: localhost
    port: 8981
    log_level: WARNING

cucumber:
  <<: *TEST

{% endhighlight %}

Then, throw this bit of nastiness in the bottom of your env.rb file.

{% highlight ruby %}
$original_sunspot_session = Sunspot.session

Before("~@search") do
  Sunspot.session = Sunspot::Rails::StubSessionProxy.new($original_sunspot_session)
end

Before("@search") do
  unless $sunspot
    $sunspot = Sunspot::Rails::Server.new
    pid = fork do
      STDERR.reopen('/dev/null')
      STDOUT.reopen('/dev/null')
      $sunspot.run
    end
    # shut down the Solr server
    at_exit { Process.kill('TERM', pid) }
    # wait for solr to start
    sleep 5
  end
  Sunspot.session = $original_sunspot_session

  MyModel.remove_all_from_index!
end
{% endhighlight %}

This will start up Sunspot for scenarios tagged with <code>@search</code>, and mock out the sunspot connection for ones that aren't.
