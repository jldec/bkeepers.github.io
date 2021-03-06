---
layout: post
title: "Using RedBox for modal dialogs"
id: 4cf5b67fdabe9d2c6100058f
updated: 2010-11-30T21:44:15-05:00
date: 2006-09-21T05:00:00-04:00
tags:
- code
redirect_from: /blog/archives/2006/09/21/using-redbox-for-modal-dialogs/
---

<p>
When I first saw <a href="http://www.huddletogether.com/projects/lightbox/">Lightbox</a>, my first thought (ok, second thought; my first was "This is awesome![](") was: "This would be a great technique for confirmation pages)" I never got around to using lightbox due to it's emphasis on images, but eventually other solutions started showing up, like <a href="http://orangoo.com/labs/GreyBox/">GreyBox</a> and <a href="http://codylindley.com/Javascript/257/thickbox-one-box-to-rule-them-all">ThickBox</a>, that allowed you to use other content.

</p>
<p>
A couple weeks ago, I came across <a href="http://blog.craigambrose.com/">Redbox</a>, a rails plugin based on ThickBox. It is a great little plugin that simply consists of a few Rails helpers and some Javascript.

</p>
<p>
As I experimented with it, I decided that I really like the feel of using modal dialog boxes for confirmation messages. It is more consistent with the desktop experience, but not as constrained as popups on the desktop.

</p>
<img src="/assets/2006/10/26/redbox_delete1.jpg" alt="Redbox Delete" height="356" width="450" />

<p>
As I was using Redbox, I developed a pattern that works really well. Instead of limiting <code>:destroy</code> requests to <code>POST</code>, I modified them to return a delete confirmation page when called with a <code>GET</code>, then perform the modification when called with a <code>POST</code>.

</p>
{% highlight ruby %}
def destroy
  @payment = Payment.find(params[:id])
  if request.get?
    respond_to do |wants|
      wants.html
      wants.js { render :layout => 'modal' }
    end
  else
    @payment.destroy
    flash[:notice] = "Payment for $#{@payment.amount} has been deleted."
    redirect_to :action => 'index'
  end
end
{% endhighlight %}

<p>
This creates a little extra code, but it also makes the modal windows degradable. If the client doesn't support JavaScript, or it is disabled, then when they click a destroy link, they will be taken to a separate confirmation page. I've also created a 'modal' layout that can have some formating, or maybe a close link.

</p>
<p>
The code to create the redbox link looks like:

</p>
{% highlight erb %}
<%= link_to_remote_redbox 'delete',
    {:url => {:action => 'destroy', :id => payment}, :method => :get},
    :href => url_for(:action => 'destroy', :id => payment) %>
{% endhighlight %}

<p>
<code>link\_to\_remote\_redbox</code> takes the same parameters as <code>link\_to\_remote</code>, minus the <code>:update</code> parameter that tells where to put the new content. Note the <code>:method =&gt; :get</code>. By default, Ajax requests use <code>POST</code>, but I don't want to <code>POST</code> to the destroy action because that will perform the modification. I'm also setting <code>:href</code>, which makes this link degradable.

</p>
<p>
As I used Redbox more and more for confirmation pages, it occurred to me that this method would also work really well for simple forms. So, with barely any modification&mdash;adding <code>respond\_to</code> in the action and replacing <code>link\_to</code> with <code>link\_to\_remote\_redbox</code>—I was able to change these small forms into modal dialogs. Currently, the forms submit a regular request, but I could refactor it a little more and use Ajax to submit the forms and update the page.

</p>
<img src="/assets/2006/10/26/redbox_enroll1.jpg" alt="Redbox Enroll" height="356" width="450" />

<p>
Overall, I really like the feel of these modal dialogs. They make the app feel snappier and less daunting. Users don't have to jump all over the app for simple modifications. They spend the majority of their time on one page, the most important page.

</p>
