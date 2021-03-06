---
layout: post
title:  "DateTime.parse is not a validater"
date:   2013-06-25 20:51:13
categories: ruby
redirect_from:
  - /ruby/2013/06/25/DateTime-parse-validation
---

I am prototyping a rails API at work and one of my routes accepts a datetime parameter.  I assumed that I would be able to just use DateTime.parse and that would take care of any sanitization of the parameters.

```ruby
starting_date = DateTime.parse(params[:starting_date])
```

So of course the first thing that I threw at it was

```ruby
starting_date=' OR '1'='1
```

And...I got back every row in the database.  How does that work?  Let's dig in.

```ruby
require 'date'
  #=> true
DateTime.parse("' OR '1'='1")
  #=> #<DateTime: 2001-01-01T00:00:00+00:00 ((2451911j,0s,0n),+0s,2299161j)>
```

Ok, so it looks like I'm not getting back every row, just all of them since 2001 (which, coincidentally, is every row).  But why is this even parsing?  Predisposed to random experimentation I tried a few things:

```ruby
DateTime.parse("'1'='1")
  => #<DateTime: 2001-01-01T00:00:00+00:00 ((2451911j,0s,0n),+0s,2299161j)>
DateTime.parse("'1'")
  => #<DateTime: 2001-01-01T00:00:00+00:00 ((2451911j,0s,0n),+0s,2299161j)>
DateTime.parse("'2'")
  => #<DateTime: 2002-01-01T00:00:00+00:00 ((2452276j,0s,0n),+0s,2299161j)>
```

Turns out a quick glance at the [ruby 2.0 documentation][date-time-docs] might have saved me some time.  Keen eyed observers will note this important phrase, "This method does not function as a validator".  So as it turns out its working as intended.  I haven't asked this to anyone who would actually have an answer, but I guess that since the docs were changed in between 1.9.3 and 2.0 I'm not the first person to try this.

The super simple fix is to just use .strptime instead.

```ruby
DateTime.strptime('2001-02-03T04:05:06+07:00', '%Y-%m-%dT%H:%M:%S%z')
  #=> #<DateTime: 2001-02-03T04:05:06+07:00 ...>
```

This has the added benefit of actually enforcing a date format which is something that I should have been done from the beginning anyway.
In the end this isn't really an issue as much as it is informative about the way that ruby handles date time parsing.

[date-time-docs]: http://www.ruby-doc.org/stdlib-2.0/libdoc/date/rdoc/DateTime.html#method-c-parse
