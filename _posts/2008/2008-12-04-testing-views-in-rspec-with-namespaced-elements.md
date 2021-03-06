---
layout: post
title: "Testing views in RSpec with namespaced elements"
id: 4cf5b659dabe9d2c610000f7
updated: 2010-11-30T21:43:37-05:00
date: 2008-12-04T18:33:00-05:00
tags:
- code
redirect_from: /blog/archives/2008/12/04/testing-views-in-rspec-with-namespaced-elements/
---

We've been working on a Facebook app (yeah, I know, I'm sorry) and have had all kinds of fun challenges. We wanted to test <a href="http://wiki.developers.facebook.com/index.php/FBML"><acronym title="Facebook Markup Language">FBML</acronym> tags</a> in the view, but the problem is that FBML uses namespaced elements.

It turns out, CSS selectors don't work with namespaced elements. There is a [CSS3 namespace module](http://www.w3.org/TR/css3-namespace/), but Rails' [\#assert\_select](http://api.rubyonrails.org/classes/ActionController/Assertions/SelectorAssertions.html#M000397) and RSpec's [\#have\_tag](http://rspec.rubyforge.org/rspec-rails/1.1.11/classes/Spec/Rails/Matchers.html#M000069) (built on `#assert_select`) don't support them. We were going to have to turn to XPath, but `#have_tag` only supports CSS selectors.

But [Hpricot](http://code.whytheluckystiff.net/hpricot/) supports XPath. A little searching revealed the [rspec\_hpricot\_matchers plugin](http://github.com/pd/rspec_hpricot_matchers), which replaces have\_tag with an Hpricot-backed version. The only problem is that the new version isn't backwards compatible. So we [forked it](http://github.com/collectiveidea/rspec_hpricot_matchers) and renamed `#have_tag` to `#match_element`.

### Usage

It's pretty straight forward. Install the [rspec\_hpricot\_matchers](http://github.com/collectiveidea/rspec_hpricot_matchers) plugin, and include the module in the RSpec config in `spec_helper.rb`.

{% highlight ruby %}
Spec::Runner.configure do |config|
  config.include RspecHpricotMatchers
end
{% endhighlight %}

Our view had something similar to:

{% highlight erb %}
<fb:multi-friend-input prefill_ids="<%= @friends.join(', ') %>" />
{% endhighlight %}

And in the view spec, we use \#match\_element with an XPath expression.

{% highlight ruby %}
describe "things/edit.fbml.erb" do
  it "should prefill selected friends" do
    render "things/edit.fbml.erb"
    response.should match_element("//fb:multi-friend-input[@prefill_ids='333,444,555']")
  end
end
{% endhighlight %}
