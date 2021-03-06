---
layout: post
title: "Live search with knockout.js"
id: 4e02c2fcdabe9d25ea000643
updated: 2011-06-23T10:44:28-04:00
date: 2011-06-23T10:26:00-04:00
tags:
- code
redirect_from: /blog/archives/2011/06/23/live-search-with-knockoutjs/
---

[Knockout.js](http://knockoutjs.com/) is an interesting take on building rich web applications. While I was initially turned off by the <acronym title="Model-View-View-Model">MVVM</acronym> approach of embedding bindings in the view, it has grown on me after playing around with it for a few days. I look forward to experimenting with it on a larger application.

My first experimentation with knockout is to implement live search. Here is a demo of searching for beers by name:

<div class="demo">
<iframe src="/assets/knockout-demo.html">
</iframe>
</div>
How to build it
---------------

To start with, we just want to display our list of beers. Once we get that done, then we can start implementing the search. I used a hard-coded list for this example, but this could easily be the result of an API request.

{% highlight javascript %}
var beers = [
  {name:"…", brewery:"…", style:"…"},
  // …
];
{% endhighlight %}

We need to tell knockout about these beers so we can bind them to the view.

{% highlight javascript %}
var viewModel = {
  beers: ko.observableArray(beers)
};

ko.applyBindings(viewModel);
{% endhighlight %}

Displaying this list of beers is simple. We create our markup for the list, and use knockout's template binding.

{% highlight html %}
<ul data-bind="template: {name:'beer', foreach:beers}"></ul>


<script type="text/html" id="beer">
  <li>
    <strong data-bind="text: name"></strong> –
    <span data-bind="text: brewery"></span> –
    <span data-bind="text: style"></span>
  </li>
</script>
{% endhighlight %}

Now we should be able see our list of beers. Next let's implement the search functionality, starting with an input field to search with.

{% highlight html %}
<input placeholder="Search…" type="search" data-bind="value: query, valueUpdate: 'keyup'" autocomplete="off">
{% endhighlight %}

The data-bind attribute tells Knockout that we want to bind the value of this input field to a variable called *query*, and we want to update that value on keyup. We need to set up *query* as an observable attribute.

{% highlight javascript %}
var viewModel = {
  // …

  query: ko.observable('')
}
{% endhighlight %}

Whenever the query attribute is changed, we want to perform our search and update our list of beers, so we subscribe to updates on `query` and call a search function.

{% highlight javascript %}
var viewModel = {
  // …

  search: function(value) {
    // remove all the current beers, which removes them from the view
    viewModel.beers.removeAll();

    for(var x in beers) {
      if(beers[x].name.toLowerCase().indexOf(value.toLowerCase()) >= 0) {
        viewModel.beers.push(beers[x]);
      }
    }
  }
};

viewModel.query.subscribe(viewModel.search);
{% endhighlight %}

Our search function begins by removing all the beers from the array observed by knockout, then loops through our list of beers and does a simple search, adding each beer back to the knockout array if it matches, which in turn updates it in the view.

View the [source on the demo](/assets/knockout-demo.html) to see all of the code in context.
