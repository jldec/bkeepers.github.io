---
layout: post
title: "How many introductions to Ajax do we need?"
id: 4cf5b67fdabe9d2c61000574
updated: 2010-11-30T21:44:15-05:00
date: 2006-10-09T16:00:00-04:00
tags:
- code
redirect_from: /blog/archives/2006/10/09/how-many-introductions-to-ajax-do-we-need/
---

Really, how many introductions to Ajax do we really need?

I've been a long time subscriber to Linux Journal but have been disappointed by the content in the past year or so. After Nicholas Petreley signed on as editor, I was encouraged to see their visual changes to the magazine (finally making it look like a magazine that could be understood by someone that doesn't know how to write a kernel module), and I was delighted when they finally discovered Ruby this past summer. When the latest issue arrived in my mailbox, I was excited to see Tim Bray on the cover.

That excitement quickly turned into the fuel for this rant when I started paging through the issue and I came across the first article, "Beginning Ajax". Not only do we not need another introduction to Ajax, why are we still writing introductions that use the low level XmlHttpRequest object and explaining to our readers how you have to use ActiveX on IE?

I use Ajax in some form on most of the apps that I write, but beyond the examples from the first introduction to Ajax article that I read, I have NEVER used the low level JavaScript calls for doing Ajax. Prototype eliminates the need to use the low level handling of Ajax:

{% highlight javascript %}
new Ajax.Updater('div_to_update', '/dosomething', {
    asynchronous:true,
    onComplete: doSomething(request),
    onLoading: showSpinner();
    }
);
{% endhighlight %}

That's a heck of a lot cleaner than the 25 line example in the Linux Journal article. xhr.readyState == 4, who cares?

What's even more interesting, using Rails, I rarely even write code using Prototype. More commonly, I write:

{% highlight erb %}
&lt;%= link_to_remote('edit',
  :update =&gt; 'div_to_update',
  :url =&gt; {:action =&gt; 'edit', :id =&gt; object})
%&gt;
{% endhighlight %}

90% of the time, this is more than sufficient, and for that other 10%, I am intimately familiar with Prototype and can accomplish the same thing in a fraction of the time that it takes to write the raw JavaScript.

So, again, I ask, why are we still writing introduction to Ajax articles that use low level JavaScript? I agree that it is important to understand how Ajax works, but there are plenty of articles that do that. No one (in their right mind) would actually use this code in their production apps.
