---
layout: post
title: "Using breakpointer to debug rails tests"
id: 4cf5b682dabe9d2c610006c2
updated: 2010-11-30T21:44:18-05:00
date: 2006-07-19T04:50:00-04:00
tags:
- code
redirect_from: /blog/archives/2006/07/19/using-breakpointer-to-debug-rails-tests/
---

<p>
I played with <a href="http://wiki.rubyonrails.org/rails/pages/HowtoDebugWithBreakpoint">breakpointer</a> for a few minutes to check it out when I was first learning Rails, but I've never actually used it for serious debugging. But yesterday I had a unit test that was failing and I could not figure why, so I decided I would try it out. I discovered that breakpointer works really well for debugging tests! I had only used it in the context of debugging actions.

</p>
<p>
Using the breakpointer is really easy, just insert <code>breakpoint</code> at any point in your code. When you run your tests, it will open up an <code>irb</code> session whenever it encounters a <code>breakpoint</code> in your code.

</p>
    .....Executing break point at ./test/unit/event_test.rb:36 in `test_duration'
    irb(test_duration_with_hash=(EventTest)):001:0>
