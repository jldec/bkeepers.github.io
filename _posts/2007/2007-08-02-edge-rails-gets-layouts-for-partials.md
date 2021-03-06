---
layout: post
title: "Edge Rails gets layouts for partials"
id: 4cf5b66bdabe9d2c610002db
updated: 2010-11-30T21:43:55-05:00
date: 2007-08-02T22:21:00-04:00
tags:
- code
redirect_from: /blog/archives/2007/08/02/edge-rails-gets-layouts-for-partials/
---

Edge Rails picked up a new feature today we've often longed for at Collective Idea. [Partials can have a layout](http://dev.rubyonrails.org/changeset/7261):

{% highlight erb %}
<%= render :partial => "user", :layout => "admin", :collection => @users %>
{% endhighlight %}

This would wrap the user partial with the `admin` layout, which in this example adds an edit link for the admin user:

{% highlight erb %}
<% content_tag_for :li, user do %>
	<%= yield %>
	<%= link_to 'Edit', edit_user_path(user) %>
<% end %>
{% endhighlight %}

Another cool feature in this patch is the ability to add a layout to any block in a template, for those one-off situations, or to just tidy up repeated markup:

{% highlight erb %}
<% render :layout => "admin", :locals => { :user => owner } do %>
	<%= render :partial => "user", :collection => @users %>
  	(owner)
<% end %>
{% endhighlight %}
