---
layout: post
title: "acts_as_audited and authlogic"
id: 4cf5b653dabe9d2c61000049
updated: 2010-12-01T01:59:20-05:00
date: 2009-10-29T11:52:00-04:00
tags:
- code
redirect_from: /blog/archives/2009/10/29/acts_as_audited-and-authlogic/
---

For those using authlogic that have had issues with auditing your User model, version 1.0.2 of [acts\_as\_audited](github.com/collectiveidea/acts_as_audited) should cure your woes.

All you need to do is exclude the `last_request_at` and `perisable_token` fields from being audited. We also excluded a few other fields that don't need to be audited:

{% highlight ruby %}
class User < ActiveRecord::Base
  acts_as_audited :except => [
    :crypted_password,
    :persistence_token,
    :single_access_token,
    :perishable_token,
    :last_request_at,
  ]
end
{% endhighlight %}
