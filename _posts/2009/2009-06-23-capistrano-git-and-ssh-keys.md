---
layout: post
title: "Capistrano, Git and SSH keys"
id: 4cf5b654dabe9d2c6100005e
updated: 2010-11-30T21:43:32-05:00
date: 2009-06-23T20:45:00-04:00
tags:
- code
redirect_from: /blog/archives/2009/06/23/capistrano-git-and-ssh-keys/
---

This trick has been around for a while, but I've talked with several people that didn't know about it.

When deploying apps with Capistrano, your server needs to have access to the Git repository. Generating an SSH key for each server is a bit of a pain, but there's an easier way: SSH agent forwarding enables you to use any of your local SSH keys on the server. It's really easy to set up.

Enable SSH forwarding in `deploy.rb`:

{% highlight ruby %}
set :ssh_options, {:forward_agent => true}
{% endhighlight %}

The only other thing you need to do is tell the SSH agent about your key.

{% highlight bash %}
$ ssh-add -K
{% endhighlight %}

The `-K` option only works on OS X and it adds your key to your keychain so you don't have to run `ssh-add` after you reboot (and if you have a passphrase set, you don't have to type it every time). You can also pass it the path to an SSH private key in a different location.

Now the server can pull from any Git repository that you have access to.
