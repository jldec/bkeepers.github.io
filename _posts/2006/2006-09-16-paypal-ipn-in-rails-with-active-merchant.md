---
layout: post
title: "Paypal IPN in Rails with Active Merchant"
id: 4cf5b680dabe9d2c610005c1
updated: 2010-12-01T02:33:26-05:00
date: 2006-09-16T16:49:00-04:00
tags:
- popular
- code
redirect_from: /blog/archives/2006/09/16/paypal-ipn-in-rails-with-active-merchant/
---

<p>
<a href="http://home.leetsoft.com/am/">Active Merchant</a> makes it extremely simple to use Paypal <abbr title="Instant Payment Notification">IPN</abbr>. Here is a simple guide for getting IPN up and running.

</p>
<h3>
Sign up for a <a href="https://developer.paypal.com/">Paypal sandbox account</a>

</h3>
<p>
Paypal provides a sandbox environment that mimics their production environment, with the exception that it doesn't actually process the transactions. This is extremely useful for development and testing. It allows you to create multiple fake accounts and generate bank accounts and credit cards. More information can be found on Paypal's <a href="http://www.paypal.com/cgi-bin/webscr?cmd=_ipn-test-about-outside">Testing Instant Payment Notification</a> page.

</p>
<p>
Unfortunately, I've signed up for two different developer accounts and I've had trouble logging in with both of them. I've tried resetting my password, but I still can't log in. Fortunately, I already have my sandbox accounts set up and don't really have a need for it (except to write this guide).

</p>
<h3>
Create a Personal account and add a credit card

</h3>
<p>
After you sign up for your developer account, create a personal sandbox account and add a credit card.

</p>
<h3>
Create a Business account and add a checking

</h3>
<p>
Next, create a business sandbox account and add a checking account.

</p>
<h3>
Install the money gem

</h3>
    sudo gem install money

<h3>
Install the Active Merchant plugin

</h3>
    script/plugin install http://activemerchant.googlecode.com/svn/trunk/active_merchant

<h3>
Create a form that submits to Paypal

</h3>
<p>
Include <code>ActiveMerchant::Billing::Integrations</code> in a controller to add Active Merchant's helpers.

</p>
{% highlight ruby %}
class PaymentsController < ApplicationController
  include ActiveMerchant::Billing::Integrations

  def create
    @enrollment = current_user.enrollments.find(params[:id])
  end
end
{% endhighlight %}

<p>
In the view, use <code>payment\_service\_for</code> to create a form that submits to Paypal to process the payment.

</p>
{% highlight erb %}
<% payment_service_for @enrollment.id, PAYPAL_ACCOUNT,
        :amount => @enrollment.course.deposit, :currency => 'USD',
        :service => :paypal do |service|

    service.customer :first_name => @enrollment.student.first_name,
        :last_name => @enrollment.student.last_name,
        :phone => @enrollment.student.phone,
        :email => @enrollment.student.email
    service.billing_address :city => @enrollment.student.city,
        :address1 => @enrollment.student.street,
        :state => @enrollment.student.state,
        :country => 'USA',
        :zip => @enrollment.student.zip
    service.item_name "#{@enrollment.course.program} Deposit"
    service.invoice @enrollment.invoice.id
    service.tax '0.00'

    service.notify_url url_for(:only_path => false, :action => 'notify')
    service.return_url url_for(:only_path => false,
        :controller => 'account', :action => 'show')
    service.cancel_return_url url_for(:only_path => false,
        :controller => 'account', :action => 'show') %>

    <!-- display payment summary here -->

    <%= submit_tag 'Make Payment' %>
<% end %>
{% endhighlight %}

<p>
The code above refers to the constant <code>PAYPAL\_ACCOUNT</code>, which I define in <code>environment.rb</code>. I also set Active Merchant to use test mode, which directs it to use Paypal's sandbox:

</p>
{% highlight ruby %}
unless RAILS_ENV == 'production'
  PAYPAL_ACCOUNT = 'sandboxaccount@example.com'
  ActiveMerchant::Billing::Base.mode = :test
else
  PAYPAL_ACCOUNT = 'paypalaccount@example.com'
end
{% endhighlight %}

<h3>
Create an action that processes the IPN

</h3>
<p>
After the above form submits to Paypal and the user makes a payment, Paypal will post data about the transaction to your server. Set up an action to receive the post:

</p>
{% highlight ruby %}
  def notify
    notify = Paypal::Notification.new(request.raw_post)
    enrollment = Enrollment.find(notify.item_id)

    if notify.acknowledge
      @payment = Payment.find_by_confirmation(notify.transaction_id) ||
        enrollment.invoice.payments.create(:amount => notify.amount,
          :payment_method => 'paypal', :confirmation => notify.transaction_id,
          :description => notify.params['item_name'], :status => notify.status,
          :test => notify.test?)
      begin
        if notify.complete?
          @payment.status = notify.status
        else
          logger.error("Failed to verify Paypal's notification, please investigate")
        end
      rescue => e
        @payment.status = 'Error'
        raise
      ensure
        @payment.save
      end
    end
    render :nothing => true
  end
{% endhighlight %}

<p>
Depending on the model for your application, this action will obviously look different. The important part is that you pass the raw post data from the request to <code>Paypal::Notification.new</code>, and call <code>notify.acknowledge</code> to connect back to Paypal to verify the data.

</p>
<h3>
Enable IPN

</h3>
<p>
Lastly, log into the business account that you created above, go to <em>"Instant Payment Notification Preferences"</em> in your profile, and set the URL that Paypal should post back to after payments. (Note: this needs to be a publicly accessible URL.)

</p>
