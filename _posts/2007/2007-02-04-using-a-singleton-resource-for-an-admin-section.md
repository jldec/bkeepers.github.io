---
layout: post
title: "Using a singleton resource for an admin section"
id: 4cf5b67adabe9d2c610004d9
updated: 2010-11-30T21:44:10-05:00
date: 2007-02-04T22:55:00-05:00
tags:
- code
redirect_from: /blog/archives/2007/02/04/using-a-singleton-resource-for-an-admin-section/
---

Applications with an admin section often have a URL like http://myapp.com/admin, with that URL being a summary page, and all the other admin pages be under that URL (like, http://myapp.com/admin/users to edit users.). Without defining a lot of routes, this hasn't been very easy with Rails...until now. [Singleton resources](http://dev.rubyonrails.org/changeset/6107) have been merged into the 1.2 branch from edge-rails, so I thought I would share a little pattern that I've been using in several of my apps to accomplish this.

Singleton resources are like regular resources in Rails 1.2, except that they don't have an :id parameter. This happens to work perfectly for using one controller to show a summary page, and then nesting the routes for other controllers inside of it.

The routes definition looks like this:

{% highlight ruby %}
map.resource :admin do |admin|
  admin.resources :users
end
{% endhighlight %}

> **Update:** The key here is <code>map.resource</code> (singular).

To make this work, generate an admin controller:

    script/generate controller admin

You can now set up the "show" action in the admin controller to display a summary page.

The named route <code>users\_path</code> now generates the URL <code>/admin/users</code>.
