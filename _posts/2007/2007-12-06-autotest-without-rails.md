---
layout: post
title: "Autotest without Rails"
id: 4cf5b667dabe9d2c61000257
updated: 2010-11-30T21:43:51-05:00
date: 2007-12-06T09:28:00-05:00
tags:
- code
redirect_from: /blog/archives/2007/12/06/autotest-without-rails/
---

Autotest is a gem (well, technically ZenTest is a gem that includes autotest). But seriously, autotest is a gem! I can't write code without it anymore. It has become my coding security blanket.

I started going through withdrawal last night while I was working on a Ruby library because autotest doesn't work out of the box with plain ol' Ruby projects. It assumes you're using one of the Ruby web frameworks.

Here's how to remedy that.

[Aizatto shows us](http://rails.aizatto.com/2007/11/19/autotest-ing-your-rails-plugin/) how to use autotest with your Rails plugins and RSpec. That's a great start, but unfortunately, not all of my projects use RSpec (yet), and not all of them are Rails plugins.

To make autotest work with `Test::Unit`, we need to tell it how to map files to their tests. Here is a simple mapping that maps each ruby file in `lib/` to the a corresponding test `test/*_test.rb`. For example, `lib/foo/bar.rb` would map to to a test in `test/foo/bar_test.rb`.

{% highlight ruby %}
# autotest/testunit.rb
require 'autotest'

class Autotest::Testunit < Autotest
  def initialize # :nodoc:
    super
    @exceptions = /^\.\/(?:config|doc|log|tmp|website)/

    @test_mappings = {
      %r%^test/.*\.rb$% => proc { |filename, _|
        filename
      },
      %r%^lib/(.*)\.rb$% => proc { |_, m|
        ["test/#{m[1]}_test.rb"]
      },
      %r%^test/test_helper.rb$% => proc {
        files_matching %r%^test/.*_test\.rb$%
      },
    }
  end

  # Given the string filename as the path, determine
  # the corresponding tests for it, in an array.
  def tests_for_file(filename)
    super.select { |f| @files.has_key? f }
  end
end
{% endhighlight %}

We have the mapping, now all we need to do is tell autotest about it.

{% highlight ruby %}
# autotest/discover.rb
require File.dirname(__FILE__) + '/testunit'

Autotest.add_discovery { "testunit" }
{% endhighlight %}

Throw both of those files into a `autotest/` directory in your plain ol' Ruby project and autotest should just work for you.

Extra credit: Make it work with RSpec on plain ol' Ruby projects.
