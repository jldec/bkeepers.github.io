---
layout: post
title: "What's your command?"
id: 4cf5b67fdabe9d2c6100057f
updated: 2010-11-30T21:44:15-05:00
date: 2006-09-27T01:35:00-04:00
tags:
- code
redirect_from: /blog/archives/2006/09/27/what-s-your-command/
---

<p>
Inspired by <a href="http://laundr.us/2006/09/24/#12">laundr.us</a>.

</p>
    brandon$ history | awk '{print $2}' | awk 'BEGIN {FS="|"} {print $1}' |  sort | uniq -c | sort -nr | head -10
      78 cd
      76 rake
      61 ls
      60 svn
      24 mongrel_rails
      21 ./script/generate
      19 ruby
      18 ./script/console
      17 rm
      12 ssh
