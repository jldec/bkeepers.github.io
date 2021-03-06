---
layout: post
title: "Keepin' Sphinx Indexes Fresh"
id: 4cf5b655dabe9d2c61000089
updated: 2010-11-30T21:43:33-05:00
date: 2009-04-29T21:33:00-04:00
tags:
- code
redirect_from: /blog/archives/2009/04/29/keepin-sphinx-indexes-fresh-2/
---

&lt;infomercial-voice&gt;Stale indexes got you down? Embarrassed that your users' searches are coming up empty? Act now and you can avoid stale indexes with NEW and IMPROVED delayed delta indexing![]()&lt;/infomercial-voice&gt;

Ok, maybe it's not new and improved. It's actually [been around since January](http://groups.google.com/group/thinking-sphinx/browse_thread/thread/af6723f764329a41), but it's still awesome. [Thinking Sphinx](http://ts.freelancing-gods.com/) can use [delayed\_job](http://github.com/tobi/delayed_job/tree/master) to keep indexes fresh.

I was slow at jumping on the Sphinx bandwagon for one reason: the index has to be rebuilt to incorporate new data. Delta indexing alleviated some of this by storing frequent changes in a small separate index, but it still had to be occasionally reindexed. It also seemed to only index existing records in my trials with it. New records didn't ever seem to show up until I rebuilt the whole index.

From what I can tell, delayed delta indexing makes everything Just Work™, and here's how to use it…

After you've setup [ThinkingSphinx](:http://ts.freelancing-gods.com/), set the `:delayed` property to `:delta` in your index:

{% highlight ruby %}
class Listing < ActiveRecord::Base
  define_index do
    indexes title
    indexes description
    indexes user.login, :as => :user

    set_property :delta => :delayed
  end
end
{% endhighlight %}

The delayed delta support depends on delayed\_job, but if you're using the gem version, it's already bundled in. I'm using delayed job for some other things in my project, so I still have it installed separately and that seems to be working just fine.

delayed\_job uses a table to keep track of the jobs that need run, so create a migration containing:

{% highlight ruby %}
create_table :delayed_jobs, :force => true do |table|
   table.integer  :priority, :default => 0
   table.integer  :attempts, :default => 0
   table.text     :handler
   table.string   :last_error
   table.datetime :run_at
   table.datetime :locked_at
   table.datetime :failed_at
   table.string   :locked_by
   table.timestamps
end
{% endhighlight %}

And lastly, all you need to do is fire up the worker process:

    $ rake thinking_sphinx:delayed_delta

Now whenever changes are made to your models, the index will be updated moments later. And that's how you keep it fresh!
