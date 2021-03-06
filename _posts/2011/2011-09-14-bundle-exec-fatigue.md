---
layout: post
title: "bundle exec fatigue"
id: 4e70ea4fdabe9d70ab0013b9
updated: 2011-09-15T10:15:07-04:00
date: 2011-09-14T13:54:00-04:00
tags:
- code
redirect_from: /blog/archives/2011/09/14/bundle-exec-fatigue/
---

Tired of typing `bundle exec rake`? Me too. I even aliased it to `bx`, but that still breaks autocomplete and annoys me in subtle ways. So this week I finally remedied that situation, and thought I would share my setup since I haven't seen anyone else with as nice of a setup.

First, bundler has a handy feature to install stubs for all of the bins in your gem dependencies.

{% highlight bash %}
$ bundle install --binstubs
{% endhighlight %}

This is great, but it creates them all in a `bin` directory in your project, so not only do you have to type \`./bin/rake\`, but you also have to ignore them from git. I'm lazy and that's **too much work**!

The Final Solution
------------------

My bundler setup has 3 essential components: 1) an alias for `bundle install` which always installs bin stubs into `.bin`, 2) adding `.bin` to `PATH`, and 3) globally excluding the `.bin` directory from all git repos.

First, put the following in `.bashrc`, `.profile`, or `.bash_profile` to alias `bundle install` and add `.bin` to your `PATH`. :

{% highlight bash %}
alias bi="bundle install --binstubs=.bin"
export PATH="./.bin:$PATH"
{% endhighlight %}

Now every time you run `bi`, it will install the gem dependencies and add the bin stubs in your path so you can just run `rake` like the good ol' days. Now all you need to do is create a global `~/.gitignore` that always excludes `.bin` from every project, and tell git to use it.

{% highlight bash %}
$ git config --global core.excludesfile ~/.gitignore
{% endhighlight %}

This approach has been working wonderfully for me. Now I just need to get out of the habit of typing `bx`.

*Update: Travis Tilley pointed out [rubygems-bundler](https://github.com/mpapis/rubygems-bundler) in the comments. It looks like another unobtrusive (and more secure) way to accomplish this same thing.*
