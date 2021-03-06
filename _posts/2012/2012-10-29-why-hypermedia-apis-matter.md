---
layout: post
title: "Why Hypermedia APIs Matter"
id: 508de1c0dabe9d04a500fc56
updated: 2012-10-29T09:36:16-04:00
date: 2012-10-29T09:00:00-04:00
tags:
- code
- commentary
redirect_from: /blog/archives/2012/10/29/why-hypermedia-apis-matter/
---

You have probably heard recent ramblings about *Hypermedia APIs*, or [HATEOAS](http://en.wikipedia.org/wiki/HATEOAS), and you completely ignored it because it doesn't seem to matter. Well, I am here to tell you why it matters. *Hypermedia APIs* are all about building APIs that allow clients to adapt to changes.

Say you build an amazing REST API for your awesome app. People read your API docs and start using it because, well, it's amazing. Then, your application evolves. You want to change things, but **you can't**, because clients are hard-coded to your API docs. You now have 3 options: 1) avoid changing your app, 2) change it and break existing clients, 3) fork your API and maintain two versions while you wait for old clients to update to the new version. All three of these options suck.

You thought you built a REST API by using HTTP verbs and real URLs. That's not REST. Read Roy Fielding's rant [REST APIs must be hypertext-driven](http://roy.gbiv.com/untangled/2008/rest-apis-must-be-hypertext-driven). Roy knows a thing or two about REST; he kinda [invented it](http://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm).

If you want to build APIs that can adapt along with your application, then you need to learn about Hypermedia APIs. Many people are writing great resources about what this hypermedia stuff is all about. I don't need to duplicate their effort. If you want to learn how to build adaptable APIs, then start with Steve Klabnik's [Hypermedia API reading list](http://blog.steveklabnik.com/posts/2012-02-27-hypermedia-api-reading-list).

There is currently a severe lack of example Hypermedia APIs. I am working on the [Speaker Deck](https://speakerdeck.com) API and hope to have at least one good example soon.
