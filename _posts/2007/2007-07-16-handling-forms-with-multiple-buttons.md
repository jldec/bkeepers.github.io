---
layout: post
title: "Handling forms with multiple buttons"
id: 4cf5b66cdabe9d2c6100030f
updated: 2010-11-30T21:43:56-05:00
date: 2007-07-16T06:32:00-04:00
tags:
- code
redirect_from: /blog/archives/2007/07/16/handling-forms-with-multiple-buttons/
---

With an app that I'm working on, the client wants to have several buttons for doing different actions on every form: "Save", "Save & Continue Editing", "Save & Add Another", and "Cancel". HTML only allows you to have one action defined per form (instead of per button), and Rails pretty much assumes that if you submit a request to a specific action, you expect to execute it.

So, instead of littering my code with all kinds of `if/else` statements, I decided to wrap up the functionality into a little plugin that makes it a little cleaner.

[`with_action`](http://github.com/collectiveidea/with_action) is a `respond_to` style helper for executing different blocks based on presence of certain request parameters.

{% highlight ruby %}
def create
  with_action do |a|
    a.cancel { redirect_to articles_path }
    a.any do
      @article = Article.new(params[:article])
      if @article.save
        a.save { redirect_to article_path(@article) }
        a.edit { redirect_to article_path(@article) }
        a.approve do
          @article.approve!
          redirect_to article_path(@article)
        end
      else
        render :action => 'new'
      end
    end
  end
end
{% endhighlight %}

A block is invoked if a parameter with the same name exists and is not blank. Here is an example of the form that submits to this action:

{% highlight erb %}
<%= submit_tag 'Save', :name => 'save' %>
<%= submit_tag 'Save & Continue Editing', :name => 'edit' %>
<%= submit_tag 'Save & Approve', :name => 'approve' %>
<%= submit_tag 'Cancel', :name => 'cancel' %>
{% endhighlight %}

If an `any` block is present and no parameter that matches one of the other blocks, it is called by default, otherwise the first block will be called. The `any` block is the only one that can have nesting and be called multiple times.

I realize this could be considered trivial, but this looks a lot cleaner than the alternative, and more importantly, gave me a way to standardize on how I handle these actions. Let me know what you think.

    http://github.com/collectiveidea/with_action
