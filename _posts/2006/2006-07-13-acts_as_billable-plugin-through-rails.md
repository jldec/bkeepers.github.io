---
layout: post
title: "acts_as_billable :plugin, :through = :rails"
id: 4cf5b682dabe9d2c610006e1
updated: 2010-11-30T21:44:18-05:00
date: 2006-07-13T18:59:00-04:00
tags:
- code
redirect_from: /blog/archives/2006/07/13/acts_as_billable-plugin-through-rails/
---

<p>
At <a href="http://collectiveidea.com">Collective Idea</a>, we're developing <a href="http://eventable.com">several</a> <a href="http://7dots.com">apps</a> that take credit card payments. (We've previously <a href="http://blog.7dots.com/articles/2006/05/24/credit-card-hell-merchant-accounts-payment-gateways-and-apis" title="Credit Card Hell: Merchant Accounts, Payment Gateways and APIs">blogged</a> <a href="http://blog.7dots.com/articles/2006/07/01/choosing-a-merchant-provider" title="Chosing a Merchant Provider">about</a> sorting through this whole mine-field of credit card processing.) One of the ideas we came up with early on was using the magic of Ruby and Rails plugins to extract the billing and credit card processing logic into something that is reusable.

</p>
<p>
We started with <a href="http://home.leetsoft.com/am/">ActiveMerchant</a>, a handy little library developed for <a href="http://shopify.com">Shopify</a> by JadedPixel. ActiveMerchant is great for abstracting the credit card processing API, but you still need to figure out what to charge for, how much, how often, and be able to pull up a history of payments.

</p>
<p>
Welcome to <code>acts\_as\_billable</code>, a plugin we're working on that we think will remove some of the complexity from online billing and payments. To illustrate how it will work, here is an example.

</p>
      class User < ActiveRecord::Base
        belongs_to :plan
        acts_as_billable :plan, :frequency => :monthly
      end

      class Plan < ActiveRecord::Base
        has_many :users
      end

<p>
When a user signs up for your super-duper-new-app, they can choose a plan. That plan determines what features they get, and how much they pay to use your app. So, the user should be billed (well charged, but chargable just doesn't sound as nice) each month for the plan.

</p>
<p>
Another example:

</p>
      class User < ActiveRecord::Base
        has_many :registrations
        acts_as_billable :event, :through => :registrations
      end

      class Registration < ActiveRecord::Base
        belongs_to :user
        belongs_to :event
      end

      class Event < ActiveRecord::Base
        has_many :registrations
      end

<p>
A user can register for events, which requires a payment. Users should then be billed for each event that they register for.

</p>
<p>
So what does <code>acts\_as\_billable</code> really do? Several things:

</p>
<ol>
<li>
It adds temporary fields for the credit card information and kicks off the processing through ActiveMerchant when a record is saved. So when an event is created, if the event costs money, it makes the API calls to process the credit card, returning errors if it fails.

</li>
<li>
It adds validations for the credit card information, and validates that the credit card processed successfully.

</li>
<li>
It adds polymorphic associations from the billable class (<code>User</code> in this case) to <code>Exchange</code> (transactions) and <code>Invoice</code>. This gives you access to a record of all the user's payments.

</li>
</ol>
<p>
We're pretty excited about what this plugin can do so far, and how easy it will make it for us to do more apps that require online payments. We intend to release this plugin as open source when we get it to a usable state. I'm sure there's a lot we could do with it that we haven't thought of, so we'd love some feedback.

</p>
