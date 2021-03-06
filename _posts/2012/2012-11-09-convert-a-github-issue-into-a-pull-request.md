---
layout: post
title: "Convert a GitHub Issue Into a Pull Request"
id: 509b2d59dabe9d6553001dd8
updated: 2012-11-09T11:26:36-05:00
date: 2012-11-09T09:00:00-05:00
tags:
- code
redirect_from: /blog/archives/2012/11/09/convert-a-github-issue-into-a-pull-request/
---

A little-known-feature of the GitHub API is the ability to attach changes to an issue, converting it into a pull request. The [hub](http://defunkt.io/hub/) command, a wrapper around `git` that makes it more GitHub aware, allows you to easily do this.

{% highlight bash %}
hub pull-request -i 384 -b bkeepers:master -h bkeepers:branch-name
{% endhighlight %}

-   `-i` is the GitHub issue number
-   `-b` is the base, or where you want to send the pull request, with `bkeepers` being the owner of the repository.
-   `-h` is the head, or the branch where your changes live.

*Update: in the comments, [George Hickman](http://ghickman.co.uk/) suggests: "as long as you've pushed your changes and your local branch is tracking your remote you can exclude the `-b` and `-h` options!". Note that you can set up tracking when pushing with `git push -u origin branch-name`.*

I put together a quick (crappy quality) video showing how this works.

<iframe src="http://player.vimeo.com/video/52962538" width="580" height="435" frameborder="0" webkitAllowFullScreen mozallowfullscreen allowFullScreen>
</iframe>
My workflow lately has been something like this:

1. Create a milestone in GitHub Issues for the mini-project I am working on
2. Create Issues for the features I can foresee and begin discussions
3. Work on the code for an issue in a branch
4. Push the branch and convert the issue into a pull request with the command above

This workflow is also great for handling bugs, where someone likely opened an issue to report it, you create the fix in a branch, attach it to the issue, and then close it by merging the pull request.

The biggest caveat with this approach is that it does not work well for experimental code. There is not undo button for converting a pull request back into a plain ol' issue, nor is there a way to replace the pull request with a different one. So if you convert an issue into a pull request, and then decided you don't like the implementation, you have to close the issue, burying the discussion around it.
