---
layout: post
title: "Abusing Rails I18N to Set Page Titles"
id: 5097d37adabe9d269e0183c7
updated: 2012-11-05T10:22:18-05:00
date: 2012-11-05T09:55:00-05:00
tags:
- code
redirect_from: /blog/archives/2012/11/05/abusing-rails-i18n-to-set-page-titles/
---

I got tired of `@page_title` variables being sprinkled around in controllers and views on [Speaker Deck](https://speakerdeck.com), so I made good use of [Rails' I18n API](http://guides.rubyonrails.org/i18n.html) to define them all in one spot.

My `config/locales/en.yml` now looks something like this:

{% highlight yaml %}
en:
  title:
    default: "Speaker Deck - Share Presentations without the Mess"
    account:
      new: "Sign Up"
      show: "My Account"
    tags:
      show: "%{category}"
    likes:
      index: "Presentations Liked by %{user}"
    passwords:
      index: "Password Reset"
    talks:
      index: "All Presentations"
      edit: "Edit %{talk}"
      new: "New Presentation"
      show: "%{talk}"
    users:
      show: "Talks by %{user}"
{% endhighlight %}

To make this work, add a `#page_title` helper method, which looks up the page title translation using the controller and action name.

{% highlight ruby %}
module TitleHelper
  def page_title
    t page_title_translation_key,
      page_title_context.merge(:default => :"title.default")
  end

  def page_title_translation_key
    :"title.#{controller_name}.#{action_name}"
  end

  def page_title_context
    controller.view_assigns.symbolize_keys
  end
end
{% endhighlight %}

The helper also passes all of the instance variables assigned in the controller, so you can use any of those variables in your page title. The only caveat is that we have to define `#to_s` in order for our model objects to show up properly in the title:

{% highlight ruby %}
class User < ActiveRecord::Base
  def to_s
    username
  end
end
{% endhighlight %}

Now in our layout, we can use our new helper:

{% highlight erb %}
<title><%= page_title %></title>
{% endhighlight %}

I'm really happy with how this little hack worked out. This almost seems like something that should be in Rails core. Maybe I'll have to submit a pull request…
