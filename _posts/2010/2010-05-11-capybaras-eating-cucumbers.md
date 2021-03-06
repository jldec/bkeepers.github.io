---
layout: post
title: "Capybaras eating cucumbers"
id: 4cf5b652dabe9d2c61000008
updated: 2010-12-01T01:19:42-05:00
date: 2010-05-11T09:52:00-04:00
tags:
- code
redirect_from: /blog/archives/2010/05/11/capybaras-eating-cucumbers/
---

### (or, testing static sites with Cucumber and Capybara)

While working on an app built purely in HTML and Javascript, we needed a good way to write integration tests. We played around with a few different approaches, including "functional" tests using one of the Javascript unit testing libraries. But for now, we settled on using Selenium.

### OMG you're crazy!

No, we're not. The latest version of [Cucumber](http://cukes.info) comes with [capybara](http://github.com/jnicklas/capybara), which makes it super simple to use Selenium. Capybara just uses any rack app, so we made a simple rack app that serves our static files. So here is what we ended up with in `features/support/env.rb`:

{% highlight ruby %}
require 'rubygems'
require 'spec'
require 'cucumber'
require 'rack'
require 'capybara'
require 'capybara/dsl'

Capybara.app = Rack::Builder.new do
  map "/" do
    use Rack::Static, :urls => ["/"], :root => 'public'
    run lambda {|env| [404, {}, '']}
  end
end.to_app

require 'capybara/cucumber'
require 'capybara/session'

Capybara.default_selector = :css
Capybara.default_driver = :selenium
{% endhighlight %}

Now just copy over the web\_steps.rb that cucumber generates from another project, and proceed as normal.
