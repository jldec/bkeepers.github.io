---
layout: post
title: "How Rails has made me a better programmer"
id: 4cf5b67adabe9d2c610004de
updated: 2010-11-30T21:44:10-05:00
date: 2007-02-03T13:57:00-05:00
tags:
- code
redirect_from: /blog/archives/2007/02/03/how-rails-has-made-me-a-better-programmer/
---

A common Rails mantra is that **"Rails makes it easy to do the right thing, and difficult to do the wrong thing."** While [some might not completely agree](http://tech.rufy.com/2006/11/fatal-flaw-in-opinionated-software.html) with this opinionated software philosophy, it is this very aspect of Rails that allows developers to write better code in less time.

As good as we think we are, the truth is that we, as humans, inherently stink at what we do. We're lazy, inconsistent, unimaginative, undisciplined and we lack the cognitive ability to think through complex systems with any depth. The good news is that with some help, and luck, we can overcome these deficiencies.

We can make up for what we lack by using tools that help us do the right thing.

It is impossible to use Rails and not become a better developer because it makes you. Here are some two of the ways that Rails has made me a better developer.

### 1) Simple solutions that work

I can't recall the source, but there is a quote that goes something like this: "Any complex system that works evolved from a simple system that worked." Amen!

What makes Rails so great is that it takes the exact opposite approach from every other framework that I have used. It makes it incredibly easy to solve the simple problems, and leaves it as an exercise to developer to solve the difficult problems (that can't be solved by a framework anyway).

Rails assumes that you are going to take the simplest route to solve a problem and makes it easy for you to do so. That not only affects how you solve the simple problems that rails makes easier, but it bleeds over into how you solve the harder problems.

### 2) Database as part of the application

Despite Oracle's desperate attempts, databases are a terrible application development platform. They're designed to store and retrieve data, not do something meaningful with it.

Once upon a time, I believed in defining every possible constraint in the database. Foreign keys, field length, required fields...if SQL supported it, I used it. In the name of data integrity, I defended my position from nay-saying co-workers. I don't know if it was the result of being in the "enterprise", or just a pre-web-services attitude.

Working with Rails, I began to understand that my constraints were not really preserving data integrity, but enforcing a form of static typing. And as I came to realize through using a dynamic language, static typing is merely a simple from of testing&mdash;making assertions about the data type.

A database is just part of the application. I don't need to statically type it to preserve data integrity, just in case some other application might need to access the data. If another application needs access to my applications data, then it also needs access to the business rules, and therefor should go through some type of API.

(And since Rails 1.2 makes REST so dead simple, accessing the REST API almost becomes easier than trying to make sense of the raw database schema).

How has Rails made you a better developer?
