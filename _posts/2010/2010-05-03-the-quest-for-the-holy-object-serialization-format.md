---
layout: post
title: "The quest for the Holy object serialization format"
id: 4cf5b652dabe9d2c61000010
updated: 2010-12-01T01:22:06-05:00
date: 2010-05-03T18:13:00-04:00
tags:
- code
redirect_from: /blog/archives/2010/05/03/the-quest-for-the-holy-object-serialization-format/
---

One of my favorite features of [delayed\_job](http://github.com/collectiveidea/delayed_job) is that you can delay execution of any method on any object. In order to get this to work, you have to be able to serialize the object into a database field, and then load it in a separate process.

For certain objects, like ActiveRecord or MongoMapper models, you don't actually want to "serialize" this object, but instead just reload the record from the database. To accomplish this, delayed\_job previously would call `#to_yaml` on the job, and [do a nasty hack](http://github.com/collectiveidea/delayed_job/commit/35a28085cb54f82562a7b869c3cdd85fae7b4b44) to store any objects that were ActiveRecord objects. This has always bothered me, so yesterday I set out to find a proper solution to serializing jobs.

### Scene 1: enter YAML

YAML had 2 major problems in the context of how delayed\_job was using it:

1.  It would serialize the attributes of the ActiveRecord class and reload them in the same state that they were in when the job was created. In most instances, I want to load the class in its current state from the database.
2.  You can't call `#to_yaml` on a class, which delayed\_job required to delay execution of class methods

{% highlight ruby %}
String.to_yaml
# TypeError: can't dump anonymous class Class
{% endhighlight %}

### Scene 2: who needs documentation?

It turns out that YAML has an undocumented feature (and YAML was originally written by \_why, so you have to be mad genius to understand the code) where you can define how objects should be serialized and unserialized.

Here is how it works for ActiveRecord:

{% highlight ruby %}
class ActiveRecord::Base
  yaml_as "tag:ruby.yaml.org,2002:ActiveRecord"

  def self.yaml_new(klass, tag, val)
    klass.find(val['attributes']['id'])
  end

  def to_yaml_properties
    ['@attributes']
  end
end
{% endhighlight %}

**Problem 1: solved.**

### Scene 3: a Class act

As luck would have it, someone submitted a [patch to YAML back in 2006 to add `#to_yaml` to Class and Module](http://blade.nagaokaut.ac.jp/cgi-bin/scat.rb/ruby/ruby-talk/178257). \_why was reluctant to accept the patch because "reloading these objects causes trouble if you haven't required the right libraries". This doesn't worry me with delayed\_job because the worker will be running in the same environment.

Here's the monkey patch in all its glory:

{% highlight ruby %}
class Module
  yaml_as "tag:ruby.yaml.org,2002:module"

  def Module.yaml_new( klass, tag, val )
    if String === val
      val.split(/::/).inject(Object) {|m, n| m.const_get(n)}
    else
      raise YAML::TypeError, "Invalid Module: " + val.inspect
    end
  end

  def to_yaml( opts = {} )
    YAML::quick_emit( nil, opts ) { |out|
      out.scalar( "tag:ruby.yaml.org,2002:module", self.name, :plain )
    }
  end
end

class Class
  yaml_as "tag:ruby.yaml.org,2002:class"

  def Class.yaml_new( klass, tag, val )
    if String === val
      val.split(/::/).inject(Object) {|m, n| m.const_get(n)}
    else
      raise YAML::TypeError, "Invalid Class: " + val.inspect
    end
  end

  def to_yaml( opts = {} )
    YAML::quick_emit( nil, opts ) { |out|
      out.scalar( "tag:ruby.yaml.org,2002:class", self.name, :plain )
    }
  end
end
{% endhighlight %}

**Problem 2: Solved**

### Scene 4: Finalé

This all seem seems to work wonderfully, but I'm left wondering if there's something I'm missing. Anyone see any problems with using YAML in this way, or have I found the Holy Grail?
