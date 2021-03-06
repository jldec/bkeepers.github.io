---
layout: post
title: "Git: the NoSQL database"
id: 4e5d7350dabe9d09d0003809
updated: 2012-06-23T06:33:56-04:00
date: 2011-09-01T09:00:00-04:00
tags:
- popular
- code
- talk
redirect_from: /blog/archives/2011/09/01/git-the-nosql-database/
---

We all know that Git is pretty amazing. It's fast, reliable, flexible, and it keeps our project history safely nuzzled in it's cozy object database while we sleep soundly at night. But I'm curious to see if it can be used for **more than code**. I've had a few apps in the back of my mind for a while now that would be really interesting if the data was stored in Git.

If only there was an easy way to read and write a Git repo from Ruby…

Toystore & adapter-git
----------------------

[Toystore](https://github.com/jnunemaker/toystore) is an ActiveModel-based object mapper for key-value data stores. The beauty of Toystore is that it doesn't care what the backend is. It uses [Adapter](https://github.com/jnunemaker/adapter) to abstract the connection to any data store that can set, get, and delete keys.

Well, **Git is a key-value store**; it supports set, get and delete on keys (a.k.a. paths). So I sat down with Scott Chacon's [Git Internals](https://github.com/pluralsight/git-internals-pdf) Peepcode PDF and put together [adapter-git](https://github.com/bkeepers/adapter-git), built on top of [Grit](https://github.com/mojombo/grit).

Now I can create pretty models that are stored in Git.

{% highlight ruby %}
class Item
  include Toy::Store

  store :git, Gaskit.repo,
    :branch => 'content',
    :path   => 'items'

  attribute :title,       String
  attribute :description, String
  attribute :created_at   Time, :default => lambda { Time.now }
end
{% endhighlight %}

Toystore uses conventions that will be familiar to anyone that has used Active Record or MongoMapper.

{% highlight ruby %}
item = Item.create!(:title => 'Git: the NoSQL database')
item.update_attributes(:description => "OMG this is awesome!")
{% endhighlight %}

The biggest difference is that you can't "find" records. The data stored in a key-value store is opaque, so all you can do is **get** it by key.

{% highlight ruby %}
item = Item.get!('3FB053FA-0A3B-4903-9CE0-2A8A964E0F37')
{% endhighlight %}

Caveats
-------

I have no idea if Git will work as a data backend for an application. I'm sure GitHub has solved many of the problems with concurrency and scaling a filesystem.

There is still a lot of room for improvement on adapter-git too. Here are just a few things I'd like to add soon:

-   **Locking** - I wouldn't want to use adapter-git for anything with a lot of concurrency at the moment. Git commits are atomic; you'll never corrupt a repo by a failed commit, but if you have a lot of concurrent access, you might loose commits.
-   **Custom commit messages** - Currently adapter-git just uses the ID of the key being set in the commit message. In the app I'm experimenting with, I've already had a desire to set custom commit messages.
-   **Update working copy** - adapter-git currently just works against the git repo itself. It doesn't update your working copy. So \`git status\` will currently tell you that you've deleted files from your working copy after you update records with adapter-git.
-   **Merge conflicts** - I'm looking forward to being able to programmatically resolve merge conflicts. Riak has a cool pattern for resolving conflicts on read, so I'd love to see if I can build something into adapter-git and toystore to work in a similar fashion.

Check out [adapter-git](https://github.com/bkeepers/adapter-git) on GitHub and try building an app backed by Git!

*Update: Check out the [video](http://vimeo.com/44458223) and slides for a talk I gave on this topic.*

<script async class="speakerdeck-embed" data-id="4f926da9cb4cd0001f00a1ff" data-ratio="1.3333333333333333" src="//speakerdeck.com/assets/embed.js">
</script>
