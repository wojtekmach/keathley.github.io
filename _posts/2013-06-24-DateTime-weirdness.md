---
layout: post
title:  "DateTime.parse is broken or working as intended?"
date:   2013-06-24 20:51:13
categories: ruby
---

I am prototyping a rails API at work and one of my routes accepts a datetime parameter.  I assumed that I would be able to just use DateTime.parse and that would take care of any sanitization of the parameters.

{% highlight ruby %}
starting_date = DateTime.parse(params[:starting_date])
{% endhighlight %}

So of course the first thing that I threw at it was {% highlight ruby %}starting_date=' OR '1'='1{% endhighlight %}

And...I got back every row in the database.  How does that work?  Let's dig in.

{% highlight ruby %}
require 'date'
  #=> true 
DateTime.parse("' OR '1'='1")
  #=> #<DateTime: 2001-01-01T00:00:00+00:00 ((2451911j,0s,0n),+0s,2299161j)>
{% endhighlight %}

Ok, so it looks like I'm not getting back every row, just all of them since 2001 (which, coincidentally, is every row).  But why is this even parsing?  Predisposed to random experimentation I tried a few things:

{% highlight ruby %}
DateTime.parse("'1'='1")
  => #<DateTime: 2001-01-01T00:00:00+00:00 ((2451911j,0s,0n),+0s,2299161j)>
DateTime.parse("'1'")
  => #<DateTime: 2001-01-01T00:00:00+00:00 ((2451911j,0s,0n),+0s,2299161j)> 
DateTime.parse("'2'")
  => #<DateTime: 2002-01-01T00:00:00+00:00 ((2452276j,0s,0n),+0s,2299161j)> 
{% endhighlight %}

Turns out a quick glance at the [ruby 2.0 documentation][date-time-docs] might have saved me some time.  Keen eyed observers will note this important phrase, "This method does not function as a validator".  So as it turns out its working as intended.  I haven't asked this to anyone who would actually have an answer, but I guess that since the docs were changed in between 1.9.3 and 2.0 I'm not the first person to try this.

The super simple fix is to just use .strptime instead.

{% highlight ruby %}
DateTime.strptime('2001-02-03T04:05:06+07:00', '%Y-%m-%dT%H:%M:%S%z')
  #=> #<DateTime: 2001-02-03T04:05:06+07:00 ...>
{% endhighlight %}

This has the added benefit of actually enforcing a date format which is something that I should have been done from the beginning anyway.  In the end this is really a non-issue since strptime does what we need.  It seems that DateTime.parse is working on as intended while being simultaneously pretty useless.

[date-time-docs]: http://www.ruby-doc.org/stdlib-2.0/libdoc/date/rdoc/DateTime.html#method-c-parse