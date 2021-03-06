---
layout: post
title: "Ruby's require doesn't expand paths"
id: 4cf5b666dabe9d2c6100023f
updated: 2010-12-01T02:36:40-05:00
date: 2008-01-08T23:25:00-05:00
tags:
- popular
- code
redirect_from: /blog/archives/2008/01/08/ruby-s-require-doesn-t-expand-paths/
---

I ran across this issue [several weeks ago](http://rubyforge.org/pipermail/rspec-devel/2007-November/004235.html), but it came up at the latest [Grand Rapids Ruby Group](http://gr-ruby.org/) meeting, so I thought I would share it.

Ruby's `require`, for better or worse, doesn't expand paths. As the [docs point out](http://ruby-doc.org/core/classes/Kernel.html#M005966), `require 'a'; require './a'` will load `a.rb` twice. This doesn't matter most of the time, but there's one place it's used often that will bite you: tests and specs.

Every Rails' test has a line similar to this:

{% highlight ruby %}
require File.dirname(__FILE__) + '/../test_helper'
{% endhighlight %}

This doesn't normally cause any problems, as long as every test has an identical require statement. Where you start to get into problems is when you have tests in a nested subdirectory (like `test/controllers/admin/users_controller_test.rb`), in which case the require statement would look like this:

{% highlight ruby %}
require File.dirname(__FILE__) + '/../../test_helper'
{% endhighlight %}

`require` sees this as a different file and will re-load it. This still shouldn't hurt you unless you're doing something in `test_helper.rb` that would be changed by loading it twice (like aliasing a method). This also effects RSpec with requiring `spec_helper.rb`

The solution? Expand the path yourself.

{% highlight ruby %}
require File.expand_path(File.dirname(__FILE__) + '/../../test_helper')
{% endhighlight %}

It's not really clear to me why Ruby's `require` works this way. You would think a method that was intended to only load a file once would make sure that it never re-loaded the same file, no matter how it was referenced. It definitely doesn't adhere to the "principle of least surprise". Any idea why?
