---
layout: post
title: "Why another queuing library?"
id: 4e8930c4dabe9d4b0a00af5e
updated: 2011-10-19T22:06:26-04:00
date: 2011-10-19T21:41:00-04:00
tags:
- code
redirect_from: /blog/archives/2011/10/19/why-another-queuing-library/
---

Along with the release of [Speaker Deck](http://speakerdeck.com), we released [Qu](https://github.com/bkeepers/qu), a Ruby library for queuing and processing background jobs. Why another queuing library? I briefly explain why I wrote Qu in the [README](https://github.com/bkeepers/qu#readme), so read that first. But I wanted to give a little more of the backstory.

Reason \#1: Resque issues on Heroku
-----------------------------------

Speaker Deck makes heavy use of background processing to turn PDFs of slides into the gorgeous player that you see on the site. Speaker Deck was using Resque, but unfortunately there a few issues with Resque on Heroku ([\#368](https://github.com/resque/resque/issues/368), [\#319](https://github.com/resque/resque/issues/319)). The mishandling of signals described in \#368 was especially an issue for us, because any time we deploy or scale the workers up or down, we would lose jobs; they wouldn't show up as failed, they would just completely vanish.

So the logical approach would be to just fork resque, reverse the signals, and use that version, right? If that was my only issue with resque, then yes, but…

Reason \#2: Infrastructure dependencies
---------------------------------------

At [Ordered List](http://orderedlist.com), all of our products are built on top of [MongoDB](http://mongodb.org). Both [Harmony](http://get.harmonyapp.com) and [Gaug.es](http://get.gaug.es) already have infrastructure set up to properly scale, replicate, and back up MongoDB. Both apps currently use resque minimally. Gaug.es literally only uses it to send email in the background since everything else is done in real-time. Harmony uses it slightly more.

So to run resque, we have to administer 4 additional Redis instances (master and slave in both production and staging) for both products. If we were at a scale where we needed Redis, the dependency would be warranted. But mongo is currently more than capable of doing what we need.

And I know we're not alone. I've had many consulting clients in the past on shoe-string hosting budgets that didn't need the robustness of Redis. delayed\_job, were it more stable, was perfect for them.

Reason \#3: Cheap thrills
-------------------------

Having been the primary maintainer of a [popular fork of delayed\_job](https://github.com/collectiveidea/delayed_job) for a couple years, I feel like I formed a lot of ideas about how I would build it differently. I made a lot of mistakes with delayed\_job (let's not talk about YAML), but I also learned a lot of lessons.

Supporting multiple backends is one of the most important things I think Qu can provide. Resque has a thriving ecosystem that is all built around Redis. As I said in Reason \#2, Redis often makes sense, but there are times when it doesn't. I find it extremely disappointing that the flourishing Resque ecosystem can't be leveraged with another database backend. I think Qu can create an ecosystem with the benefit of being able to use it in every case.

One criticism of this approach is that databases have different features and strengths, so it will be impossible to make the backends behave the same. Each backend will certainly have it's quirks, but fundamentally, I think they can all behave the same. The current Redis and Mongo backends are perfect examples of this. Internally, they both operate very differently, but their API is the same and they both pass the [same specs](https://github.com/bkeepers/qu/blob/master/lib/qu/backend/spec.rb).

So far, I'm very pleased with how Qu is turning out. There is still a lot of work to be done, but I think I have a good start and have been pleased by the intrest in it already.
