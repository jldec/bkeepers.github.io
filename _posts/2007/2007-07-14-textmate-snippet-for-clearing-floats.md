---
layout: post
title: "TextMate snippet for clearing floats"
id: 4cf5b66ddabe9d2c6100031a
updated: 2010-11-30T21:43:57-05:00
date: 2007-07-14T18:51:00-04:00
tags:
- code
redirect_from: /blog/archives/2007/07/14/textmate-snippet-for-clearing-floats/
---

I often use positioniseverything.net's CSS method for [clearing floats without structural markup](http://www.positioniseverything.net/easyclearing.html). The code for this technique is not something that I've committed to memory yet, so I'm referencing the article all the time (I could just copy it from another project, but it's just so much quicker to type "css clear float" into google).

Well, positioniseverything.net is about to get several less page hits per week, because I finally threw the code into a TextMate snippet and assigned it the keyword "clearfix".

    ${1:.clearfix}:after {
      content: ".";
      display: block;
      height: 0;
      clear: both;
      visibility: hidden;
    }
    $1 {display: inline-block;}
    /* Hides from IE-mac \*/
    * html $1 {height: 1%;}
    $1 {display: block;}
    /* End hide from IE-mac */
    $0

**Update:** DRYed up based on [Jacob's comment](http://opensoul.org/2007/7/14/textmate-snippet-for-clearing-floats#comment-3230).
