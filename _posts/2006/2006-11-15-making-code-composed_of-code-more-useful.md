---
layout: post
title: "Making #composed_of more useful"
id: 4cf5b67edabe9d2c61000548
updated: 2010-11-30T21:44:14-05:00
date: 2006-11-15T19:54:00-05:00
tags:
- code
redirect_from: /blog/archives/2006/11/15/making-code-composed_of-code-more-useful/
---

**Update:** my patch [finally got added to edge rails](http://dev.rubyonrails.org/changeset/8003).

Active Record allows you to abstract fields into an aggregate object by using the <a href="http://api.rubyonrails.org/classes/ActiveRecord/Aggregations/ClassMethods.html#M000829"><code>composed\_of</code></a> declaration. This is handy, but the current implementation can be a real pain.

The first and somewhat trivial issue is the `composed_of` declaration builds a string and evals it to define the attribute accessors. It's not a big deal, but it's dirty. The second issue is that aggregate objects are not easy to manipulate in Rails, especially in forms.

### Fixing it

So, I've written a plugin that overrides the Active Record implementation of <code>composed\_of</code>, which allows you to specify a block to convert incoming parameters to the correct type.

Personally, this has been most helpful when using the [Money gem](http://rubyforge.org/projects/money/):

{% highlight ruby %}
class Account < ActiveRecord::Base
  composed_of :balance, :class_name => "Money", :mapping => %w(cents cents) do |amount|
	  amount.to_money
  end
end
{% endhighlight %}

If <code>\#balance=</code> receives anything besides a <code>Money</code> object, it will call the block to try to convert the parameter to a <code>Money</code> object.

{% highlight ruby %}
>> account = Account.new :balance => 100
>> account.balance
=> #&lt;Money:0x2612770 @cents=10000, @currency="USD"&gt;
{% endhighlight %}

And now it can transparently be used in forms:

{% highlight irb %}
  <%= text_field :account, :balance %>
{% endhighlight %}

This can even be used for more advanced aggregations:

{% highlight ruby %}
class User < ActiveRecord::Base
  composed_of :address, :class_name => "Address"
		:mapping => [%w(street street), %w(city city), %w(state state), %w(zip zip)] do |addr|
	Address.new(addr[:street], addr[:city], addr[:state], addr[:zip])
  end
end
{% endhighlight %}

A user can now be created from a hash:

{% highlight ruby %}
  User.new(:address => {:street => "123 A Street", :city => "Somewhere", :state => "NO", :zip => 12345})
{% endhighlight %}

I didn't quite realize all the implications of this extension until I was writing up the docs for this plugin. Active Record magically does type casting for a limited set of types, namely dates and numbers. But this essentially allows you to have a form of type casting for any attribute. Interesting...

### Installation

This has also been submitted as a [patch to the Rails trac](http://dev.rubyonrails.org/ticket/6322) but hasn't been accepted yet.

      script/plugin install http://source.collectiveidea.com/public/rails/plugins/composed_of_conversion
