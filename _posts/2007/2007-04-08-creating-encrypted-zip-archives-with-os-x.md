---
layout: post
title: "Creating encrypted Zip archives with OS X"
id: 4cf5b673dabe9d2c61000425
updated: 2010-12-01T02:35:08-05:00
date: 2007-04-08T22:16:00-04:00
tags:
- popular
- life
redirect_from: /blog/archives/2007/04/08/creating-encrypted-zip-archives-with-os-x/
---

Sadly, OS X *Tiger* doesn't come with a way to create or open encrypted Zip archives, even from the command line (~~hopefully this will change with Leopard~~ *if you have Leopard then you can skip the MacPorts part and just use the command at the end*). The man pages for zip say that it supports it, but when you pass the <code>-e</code> or <code>-P</code> option, it complains:

    zip error: Invalid command arguments (encryption not supported)

Fortunately, [MacPorts](http://www.macports.org/) (formerly DarwinPorts) to the rescue! There's a version of the command line zip utility in MacPorts that works as advertised.

[Install MacPorts](http://trac.macosforge.org/projects/macports/wiki/InstallingMacPorts), and install the new command line zip utility:

    sudo port install zip

Move the old /usr/bin/zip out of the way, and then create encrypted Zip archives to your hearts content.

    zip -re myarchive.zip mydir/
    Enter password: ********
