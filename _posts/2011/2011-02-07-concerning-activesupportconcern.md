---
layout: post
title: "Concerning ActiveSupport::Concern"
id: 4d49ddccdabe9d238e00060a
updated: 2011-03-02T09:12:53-05:00
date: 2011-02-07T23:16:00-05:00
tags:
- popular
- code
redirect_from: /blog/archives/2011/02/07/concerning-activesupportconcern/
---

I [pushed some changes to MongoMapper](https://github.com/jnunemaker/mongomapper/commit/00bba859b1f68e4c112e485a2d348995699ca79c) that replace the custom plugin system with [`ActiveSupport::Concern`](https://github.com/rails/rails/blob/master/activesupport/lib/active_support/concern.rb). `ActiveSupport::Concern` has been around for a while, but it's capabilities are not widely known.

Concerns wrap up the pattern of including a module, and having it extend class methods, include instance methods, and apply some configuration. If you've ever extended ActiveRecord, then you've likely written code that looks like this:

{% highlight ruby %}
module M
  def self.included(base)
    base.extend ClassMethods
    base.send :include, InstanceMethods
    base.some_class_method
  end

  module ClassMethods
    # ...
  end

  module InstanceMethods
    # ...
  end
end
{% endhighlight %}

When our module `M` is included into a class, it extends the base class with `ClassMethods`, includes `InstanceMethods`, and invokes `some_class_method`.

Cosmetics
---------

The first feature provided by `ActiveSupport::Concern` is purely cosmetic. Here is our module above rewritten:

{% highlight ruby %}
module M
  extend ActiveSupport::Concern

  included do
    some_class_method
  end

  module ClassMethods
    # ...
  end

  module InstanceMethods
    # ...
  end
end
{% endhighlight %}

We no longer have to manually extend and include our class an instance methods. Instead, we call `#included` with a block that gets class evaled when our concern is included.

Dependency resolution
---------------------

The second feature provided by `ActiveSupport::Concern` is very helpful for a library like MongoMapper with numerous of modules that depend on each other. Each concern can include the concerns that it depends on, and they will get included into the class first. For example:

{% highlight ruby %}
module A
  extend ActiveSupport::Concern
  # … define class and instance methods …
end

module B
  extend ActiveSupport::Concern
  include A

  included do
    class_method_from_module_a
  end

  # … define class and instance methods …
end
{% endhighlight %}

Now, users can include our module `B` into a class, and module `A` will get included.

{% highlight ruby %}
class C
  include B
end
{% endhighlight %}

How It Works
------------

I find the implementation of `ActiveSupport::Concern` fascinating. With only 3 methods and just over 20 lines of code, it cleverly overrides some of Ruby's default behavior. He is the source in full:

{% highlight ruby %}
module ActiveSupport
  module Concern
    def self.extended(base)
      base.instance_variable_set("@_dependencies", [])
    end

    def append_features(base)
      if base.instance_variable_defined?("@_dependencies")
        base.instance_variable_get("@_dependencies") << self
        return false
      else
        return false if base < self
        @_dependencies.each { |dep| base.send(:include, dep) }
        super
        base.extend const_get("ClassMethods") if const_defined?("ClassMethods")
        base.send :include, const_get("InstanceMethods") if const_defined?("InstanceMethods")
        base.class_eval(&@_included_block) if instance_variable_defined?("@_included_block")
      end
    end

    def included(base = nil, &block)
      if base.nil?
        @_included_block = block
      else
        super
      end
    end
  end
end
{% endhighlight %}

Let's walk through each section.

{% highlight ruby %}
def self.extended(base)
  base.instance_variable_set("@_dependencies", [])
end
{% endhighlight %}

When our module is extended with `ActiveSupport::Concern`, Ruby calls the `self.extended` method, and passes in our module. `ActiveSupport::Concern` sets an instance variable on our module to an empty array. This will be used to keep track of the modules that our concern depends on.

{% highlight ruby %}
def append_features(base)
  # …
end
{% endhighlight %}

Next `ActiveSupport::Concern` defines `#append_features`, which Ruby calls on our module whenever it is included into another module. Ruby's default implementation of this method will add constants, methods, and variables of this module to the base module. This is where `ActiveSupport::Concern` starts to get really sneaky.

{% highlight ruby %}
if base.instance_variable_defined?("@_dependencies")
  base.instance_variable_get("@_dependencies") << self
  return false
{% endhighlight %}

If the module that our concern is included into has the dependencies instance variable, then it is assumed to be another concern and our concern is appended to it's dependencies. Note that `#super` is never called, and thus Ruby's default behavior never happens. (Seriously, Ruby is awesome.)

{% highlight ruby %}
else
  return false if base < self
  @_dependencies.each { |dep| base.send(:include, dep) }
  super
  base.extend const_get("ClassMethods") if const_defined?("ClassMethods")
  base.send :include, const_get("InstanceMethods") if const_defined?("InstanceMethods")
  base.class_eval(&@_included_block) if instance_variable_defined?("@_included_block")
end
{% endhighlight %}

If the module that our concern is included into does not have the dependencies instance variable, then it's a plain ol' module and we want to actually include our concern into it. If this module has already been included then it simply returns false. It then loops over each dependency, includes it into the base (since our dependencies are always concerns too, they will each follow this same code path), and calls `#super` to perform Ruby's default behavior. It then extends `ClassMethods` and includes `InstanceMethods` if they are defined.

Lastly, it calls `#class_eval` on a block if it's defined, but where does this block come from?

{% highlight ruby %}
def included(base = nil, &block)
  if base.nil?
    @_included_block = block
  else
    super
  end
end
{% endhighlight %}

Ruby normally calls `#included` on a module whenever it is included into another module or class, passing in the module or class that it was included into. `ActiveSupport::Concern` hijacks this method to give us a pretty syntax for declaring what should happen when our concern is included.

Since Ruby will still call this method whenever our module is included, the original behavior has to be preserved. If a base class or module is passed in, then Ruby is calling this method so `#super` is called. If a base module or class is not passed in, then our concern is calling it with a block that should be saved for when it actually is included.

It's full of trickery, but I love it.
