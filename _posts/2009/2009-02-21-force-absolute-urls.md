---
layout: post
title: "Force Absolute URLs"
id: 4cf5b657dabe9d2c610000bf
updated: 2010-11-30T21:43:35-05:00
date: 2009-02-21T00:03:00-05:00
tags:
- code
redirect_from: /blog/archives/2009/02/21/force-absolute-urls/
---

Need to force all URLs in a controller or action to be absolute URLs, but don't want to use the `*_url` named routes or, even worse, `:only_path => false` everywhere?

### Background

As we were putting the finishing touches on an awesome Facebook app today (yes I said "awesome" in the same sentence as "facebook app"…you'll see), we needed to make it so that users can add the app as a tab in their profile. Facebook makes this very easy, but there's an interesting ~~quirk~~ *feature*. From the [developer's wiki](http://wiki.developers.facebook.com/index.php/New_Design_Tabbed_Profile):

<blockquote>
<ul>
<li>
Absolute URLs on a tab take the user to your application's canvas page.

</li>
<li>
Relative URLs are treated as if they are relative to your home canvas page and AJAX-loads in the tab.

</li>
</ul>
</blockquote>
We needed all of the URLs on the page to be absolute so that they would take the user out of the tab and into our application. So we were faced with two options:

1.  Change all of our named routes from `*_path` to `*_url` and use explicit paths instead of polymorphic urls (you know, that thing that lets you do <code>form\_for `model</code>, or <code>link_to h(`model.name), @model</code>)
2.  Figure out some quick hack that forced Rails to generate absolute URLs on this one page and go on about our lives

The page reused a lot of components from all over the site, so we weren't excited about option 1. And, like all good developers, we were just adverse to that much work.

So we started looking for a good fit for option 2. We needed to force `:only_path` to be false on any URL generation. There's a way to specify `default_url_options`, but as the name implies, those are only defaults. Using the `*_path` named routes forces `:only_path` to true.

### Solution

It turns out, Rails passes all URL generation through a method on your controller called `#rewrite_options`. So we just overrode that method to force `:only_path` to false on our one action. Here's the code we used:

{% highlight ruby %}
class ContentController < ApplicationController
  # …

  def profile
    # profile action
  end

private
  # Force absolute URLs on the profile tab.
  def rewrite_options(options)
    super(action_name == 'profile' ? options.merge(:only_path => false) : options)
  end
end
{% endhighlight %}

It's slightly evil, but it worked beautifully.
