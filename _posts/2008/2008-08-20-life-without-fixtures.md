---
layout: post
title: "Life without fixtures"
id: 4cf5b65cdabe9d2c6100016c
updated: 2010-11-30T21:43:40-05:00
date: 2008-08-20T22:12:00-04:00
tags:
- code
redirect_from: /blog/archives/2008/08/20/life-without-fixtures/
---

All the cool kids tell me that fixtures just aren't the "in" thing anymore. Speaking of which, when did fanny-packs stop being cool? I wish someone would have said something…

Anyway, while I still haven't fallen out of love with (foxy) fixtures, I have taken some of the home-grown alternative methods for a spin, but so far, I've been slightly frustrated with my experience.

The predominant replacement seems to be the "factory" pattern, talked about by [Dan Manges](http://www.dcmanges.com/blog/38) and then followed up by the [FixtureReplacement](http://replacefixtures.rubyforge.org/) plugin. The basic idea is that all your tests/specs should setup the data that they require, and there is a factory that makes it easy to create the necessary valid test data. For example, a homegrown solution might look like this:

{% highlight ruby %}
class Generate
  def self.person(options={})
    p = Person.create!({
      :name     => 'Brandon',
      :login    => 'brandon',
      :password => 'testing'
    }.merge(options))
    p.account ||= Generate.account
  end
end

describe Person, '#authenticate' do
  it 'should return the person record if successful' do
    person = Generate.person
    Person.authenticate(person.login, 'testing').should == person
  end
end
{% endhighlight %}

This method scales pretty well. There are some issues, such as the use of `create!`. Sometimes you intentionally want an invalid record, or you want a new record with just some valid attributes initialized. It's also slower than fixtures, but I don't care too much about that right now.

But there's a bigger issue. Like a good little BDDer, I have also been stubbing and mocking all my "unit tests" so there is no interaction with the database or code outside of what is being tested. To make the factory method work, I now need to define 3 different methods depending on what I'm testing: one using `#create!`, one using `#new`, and another one using stubbing.

### What do we do about it?

I think the FixtureReplacement plugin is on the right track. It handles the new vs. create problem very nicely.

{% highlight ruby %}
module FixtureReplacement
  attributes_for :person do |u|
    u.name     = String.random
    u.login    = String.random
    u.password = String.random
    u.account  = default_account
  end
end

@person = new_person(:login => 'brandon')
@person = create_person
{% endhighlight %}

Before I heard about the FixtureReplacement plugin, I actually concocted my own little solution that handles all three scenarios. I'm not crazy about the syntax, but it works for my needs.

{% highlight ruby %}
Generate.add Person, :name => 'Brandon', :login => 'brandon' do |generator, u|
  u.account ||= generator.account
end

@person = Generate.person(:login => 'brandon')
@person = Generate.new_person
@person = Generate.stub_person
{% endhighlight %}

Anywho, for the fixture-less approach to work, I think stubs need to be supported.

### Is all this really worth it?

That's the question I find myself asking. What's wrong with fixtures anyway?

One thing I really like about fixtures, when done right, is that they help tell the story of your application. You get to know the fixture data as you work on the app.

The factory method also seems to go against convention-over-configuration. Instead of having a default "configuration" when your tests run, you have to configure each test. Call me lazy, but that just seems like too much work.

I find that testing takes a lot more effort now than it did with fixtures. And as a result, I'm more hesitant to test everything. So while the factory method and stubbing is theoretically supposed to help you test better, I feel like they've had the opposite effect.

What do you think? Have you had success using the factory pattern for tests?
