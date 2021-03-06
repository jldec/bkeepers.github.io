---
layout: post
title: "Tinder: Listening is just as important as speaking"
id: 4cf5b67bdabe9d2c610004f9
updated: 2010-11-30T21:44:11-05:00
date: 2007-01-27T01:28:00-05:00
tags:
- code
redirect_from: /blog/archives/2007/01/27/tinder-listening-is-just-as-important-as-speaking/
---

I just released an update to [Tinder](http://opensoul.org/2006/12/8/tinder-campfire-api), the [really unofficial](http://www.37signals.com/svn/archives2/marshmallow_the_unofficial_campfire_api_is_hot.php) Campfire API, that allows listening in on a room:

{% highlight ruby %}
campfire = Tinder::Campfire.new 'mysubdomain'
campfire.login 'myemail@example.com', 'mypassword'

room = campfire.find_room_by_name 'Room 1'

messages = room.listen
#=> [{:person=>"Brandon", :message=>"I'm getting very sleepy", :user_id=>"148583", :id=>"16434003"}]
{% endhighlight %}

<code>\#listen</code> takes an option block, which causes it to poll for new messages every 5 seconds and call the block for each message. It works great for heckling (ok, maybe that's not the intent):

{% highlight ruby %}
room.listen do |m|
  room.speak "You're dumb!" if m[:person] == 'Brandon'
end
{% endhighlight %}

Check out my [original post](http://opensoul.org/2006/12/8/tinder-campfire-api) for more information about Tinder. Feedback and patches are welcome!

Thanks to [Jesse Newland](http://soylentfoo.jnewland.com/articles/2006/12/07/updates-to-marshmallow-the-campfire-bot) for the listening code.
