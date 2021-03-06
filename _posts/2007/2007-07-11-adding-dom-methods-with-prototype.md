---
layout: post
title: "Adding DOM methods with Prototype"
id: 4cf5b66edabe9d2c6100032c
updated: 2010-11-30T21:43:58-05:00
date: 2007-07-11T16:24:00-04:00
tags:
- code
redirect_from: /blog/archives/2007/07/11/adding-dom-methods-with-prototype/
---

I just discovered that [prototype](http;//prototypejs.org) has a really cool way to add methods to all instances of any tag. Simply call `Element.addMethods(tagname, methods)`. For example, here's how I added a `request` method to anchors, which will just make an ajax call for the anchor's `href`:

{% highlight javascript %}
if (!window.Anchor) { var Anchor = new Object(); }

Anchor.Methods = {
  request: function(anchor, options) {
    anchor = $(anchor)
    options = Object.extend({method: 'get'}, options || {});
    return new Ajax.Request(anchor.readAttribute('href'), options);
  }
}
Element.addMethods('a', Anchor.Methods);
{% endhighlight %}

So now I can go through and hijack links with a specific tag name and just call `request()` on them:

{% highlight javascript %}
$$('a.jax').each(function (link) {
  link.observe('click', function(event) {
    link.request({evalScripts: true});
    Event.stop(event);
  });
})
{% endhighlight %}
