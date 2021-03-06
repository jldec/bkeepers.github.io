---
layout: post
title: "Tunneling to Production"
id: 4ed7f94ddabe9d0fdc02074f
updated: 2011-12-01T17:17:29-05:00
date: 2011-12-01T17:00:00-05:00
tags:
- code
redirect_from: /blog/archives/2011/12/01/tunneling-to-production/
---

One of the great features of SSH is its ability to tunnel any port to a remote server. I've used this SSH command often to connect to a production database:

{% highlight bash %}
$ ssh -N -L 27018:localhost:27017 example.com
{% endhighlight %}

That SSH command takes all traffic to port 27018 on your local host and tunnels it to 27017 on the remote server.

If you rarely use this command, it's easy to forget it. So why not create the tunnel directly in the scripts that need it using [net-ssh-gateway](https://github.com/jamis/net-ssh-gateway?) We've been using this on [Gaug.es](http://gaug.es) and it works great.

{% highlight ruby %}
require 'net/ssh/gateway'

gateway = Net::SSH::Gateway.new(host, username)
gateway.open('127.0.0.1', 27017, 27018)

db = Mongo::Connection.new('127.0.0.1', 27018).db('myapp_production')
# …
{% endhighlight %}
