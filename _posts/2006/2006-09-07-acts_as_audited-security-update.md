---
layout: post
title: "acts_as_audited security update"
id: 4cf5b680dabe9d2c610005d4
updated: 2010-11-30T21:44:16-05:00
date: 2006-09-07T17:11:00-04:00
tags:
- code
redirect_from: /blog/archives/2006/09/07/acts_as_audited-security-update/
---

<p>
Thanks to Michael Schuerig for pointing out that malicious users could unassociate your audit records due to the use of <code>has\_many</code> in <a href="http://opensoul.org/2006/07/21/acts_as_audited/">acts\_as\_audited</a>. <code>has\_many :audits</code> creates an attribute accessor called <code>audit\_ids</code> on the model objects that you declare acts\_as\_audited, which could allow users to pass an array of ids that would overwrite the actual audit records.

</p>
<p>
This has been fixed by adding <a href="http://api.rubyonrails.org/classes/ActiveRecord/Base.html#M000873"><code>attr\_protected :audit\_ids</code></a>, which protects it from mass assignment. If you're not using SVN externals, make sure you <a href="http://opensoul.org/2006/07/21/acts_as_audited/">get the latest version</a>.

</p>
