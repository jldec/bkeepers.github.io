---
layout: post
title: "rspec: model.should be_valid"
id: 4cf5b672dabe9d2c610003dd
updated: 2010-11-30T21:44:02-05:00
date: 2007-04-18T11:11:00-04:00
tags:
- code
redirect_from: /blog/archives/2007/04/18/rspec-model-should-be_valid/
---

One of the things I always check when spec'ing is that the model that I just created is valid.

{% highlight ruby %}
@mymodel.should be_valid
{% endhighlight %}

This uses RSpec's predicate support (any call to `be_something?` calls `something?` on the target object), and returns a correct but very unhelpful error:

    expected valid? to return true, got false

Here is a little rspec matcher that I threw together this morning to check if a model is valid and spit out the validation errors:

{% highlight ruby %}
module Spec
  module Rails
    module Matchers
      class BeValid  #:nodoc:

        def matches?(model)
          @model = model
          @model.valid?
        end

        def failure_message
          "#{@model.class} expected to be valid but had errors:\n  #{@model.errors.full_messages.join("\n  ")}"
        end

        def negative_failure_message
          "#{@model.class} expected to have errors, but it did not"
        end

        def description
          "be valid"
        end

      end

      def be_valid
        BeValid.new
      end
    end
  end
end
{% endhighlight %}

Now, I get:

    MyModel expected to be valid but had errors:
      Password confirmation can't be blank
      Email has already been taken

This is nothing fancy, but it's the small stuff that make life meaningful, so I thought I would share it. Throw the matcher code above into your `spec_helper` and check if your models are valid.

**update**: added `negative_failure_message` so "`model.should_not be_valid`" will work
