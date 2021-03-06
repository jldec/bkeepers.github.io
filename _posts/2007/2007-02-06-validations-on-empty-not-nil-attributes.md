---
layout: post
title: "Validations on empty (not nil) attributes"
id: 4cf5b679dabe9d2c610004c8
updated: 2010-11-30T21:44:09-05:00
date: 2007-02-06T22:57:00-05:00
tags:
- code
redirect_from: /blog/archives/2007/02/06/validations-on-empty-not-nil-attributes/
---

One of the first problems that I ran into when I started using Rails was trying to validate the format of attributes that aren't required. Most of the validations have an <code>:allow\_nil</code> option, but the problem is that when a form is submitted with empty form fields, those fields are empty strings instead of nil values. So the validation fails because the attribute is not nil.

For example, here's a Person model with a validation on <code>:social\_security\_number</code>, an optional attribute:

{% highlight ruby %}
class Person < ActiveRecord::Base
  validates_format_of :social_security_number,
    :with => /\d{3}[-]?\d{2}[-]?\d{4}/, :allow_nil => true
end
{% endhighlight %}

When this model is used in a form, validation will fail if the social security number field is left blank, even though <code>:allow\_nil</code> is set to true.

### The solution

It turns out that the solution is really simple: a <code>before\_validation</code> callback that just goes through and sets all the empty attributes to nil.

{% highlight ruby %}
class ActiveRecord::Base
  before_validation :clear_empty_attrs
protected
  def clear_empty_attrs
    @attributes.each do |key,value|
      self[key] = nil if value.blank?
    end
  end
end
{% endhighlight %}

I've packaged this little nugget into a plugin. Install it and go on your merry validating way.

    script/plugin install -x git://github.com/collectiveidea/clear_empty_attributes.git
