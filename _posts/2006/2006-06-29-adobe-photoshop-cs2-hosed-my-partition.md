---
layout: post
title: "Adobe Photoshop CS2 hosed my partition"
id: 4cf5b683dabe9d2c61000703
updated: 2010-11-30T21:44:19-05:00
date: 2006-06-29T12:14:00-04:00
redirect_from: /blog/archives/2006/06/29/adobe-photoshop-cs2-hosed-my-partition/
---

<p>
I'm reinstalling OS X right now. 2 hours and 7 minutes remaining.

</p>
<p>
My (new) MacBook Pro started acting up this afternoon, so I did the only thing I know how to do on the Mac—I rebooted it. Bad idea! It wouldn't start back up. It just sat there taunting me with the apple logo and the little spinner, trying to make me think it was actually doing something. I gave it a hard reset and started it in verbose mode (command+v). After a bunch of startup messages, I was greeted by the following message scrolling up the screen...repeatedly...indefinitely.

</p>
    hsf_swap_BTNode: invalid forward link (0x08003003)
    node=220 fileID=4 volume=Macintosh HD Device=/dev/disk0s2

<p>
I don't exactly know what it means, but can't be a good thing. The only reference to any part of that error is from a C source file <a href="http://darwinsource.opendarwin.org/Current/diskdev_cmds-332.11.1/fsck_hfs.tproj/dfalib/hfs_endian.c"><code>hsf\_endian.c</code></a>, which appears to be some low-level filesystem driver.

</p>
<p>
After many failed attempts with the Disk Utility reporting "Underlying task reported failure", I discovered fsck\_hfs from the <a href="http://docs.info.apple.com/article.html?artnum=106214">bottom of this article</a>. I ran it with -r to rebuild the catalog, and many runs later, it finally reported no more errors. I rebooted again, only to find that OS X still wouldn't boot up.

</p>
<p>
I then came across <a href="http://docs.info.apple.com/article.html?artnum=302411">this article</a> about the Disk Utility error I was getting, with this notice:

</p>
<blockquote>
If you have Adobe Photoshop CS2 or Adobe Illustrator CS2 installed, try removing the following files:

<ul>
<li>
/Applications/Adobe Photoshop CS2/Legal.localized/Tiếng Việt.html

</li>
<li>
/Applications/Adobe Illustrator CS2/Legal.localized/Tiếng Việt.html

</li>
</ul>
</blockquote>
<p>
Ironically, I was running Photoshop CS 2 when I first started having problems. A little more searching revealed &lt;a href="http://www.macfixitforums.com/showflat.php?Board=micromat&Number=689222"&gt;this forum discussion about Photoshop CS causing filesystem errors</a>. Most the people there seemed to have more luck than I did, because there systems at least still booted. Fortunately, I just did a full backup of my home directory two days ago, and I was able to copy new files from the last two days from the console on the Installer.

</p>
<p>
<b>update:</b> Since OS X makes it so stinking easy to install over the top of an existing installation, I'm back up in no time (well, two and a half hours, but you get the point). Now, do I install Photoshop CS2 again?

</p>
