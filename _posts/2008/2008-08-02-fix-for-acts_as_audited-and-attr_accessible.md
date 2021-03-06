---
layout: post
title: "Fix for acts_as_audited and attr_accessible"
id: 4cf5b65edabe9d2c6100017b
updated: 2010-11-30T21:43:42-05:00
date: 2008-08-02T20:16:00-04:00
tags:
- code
redirect_from: /blog/archives/2008/08/02/fix-for-acts_as_audited-and-attr_accessible/
---

The biggest complaint I get about acts\_as\_audited is finally fixed: if you want to use acts\_as\_audited on a model that uses `attr_accessible`, you can set the `:protect` option to `false`:

{% highlight ruby %}
class User < ActiveRecord::Base
  acts_as_audited :protect => false
  attr_accessible :name
end
{% endhighlight %}

This will prevent acts\_as\_audited from using `attr_protected`, which would cause Active Record to raise an error in combination with `attr_accessible`.

Get the latest code from [GitHub](http://github.com/collectiveidea/acts_as_audited)

*Update: Timothy N. Jones had a [good suggestion to automatically disable `attr_protected` if `attr_accessible` has already been called](http://timothynjones.wordpress.com/2008/08/02/acts_as_audited-and-declare-either-attr_protected-or-attr_accessible-for-user-but-not-both/). I updated acts\_as\_audited, so now you only have to set `:protect` to false if you are declaring `attr_accessible` after you call acts\_as\_audited.*
