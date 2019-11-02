---
layout: post
title:  "Wallaby needs a new maintainer"
date:   2018-08-10 10:06:00
categories: elixir wallaby
excerpt: I don't have time to dedicate to Wallaby and I'm looking for someone to take over as the maintainer.
redirect_from:
  - /elixir/wallaby/2018/08/10/wallaby-is-looking-for-new-maintainers
---

> 2019-11-02 - Since I've written this post both Mitchell Hanberg and Michał Łępicki
> have stepped up to help maintain Wallaby. I'm going to leave this blog post up
> for posterity but in reality the project is now in very capable hands and is
> more active than its been in a long time.

## TL;DR

I don't have the time to maintain Wallaby in the way that it needs to be
maintained. If you're interested in taking over maintainership then please
let me know in a github issue. I'm hoping that I can transfer it to
someone who is looking for a way to break into the Elixir community.

If you wanna keep reading then buckle up because I'm about to write WAY
too many words about a silly piece of software.

## OMG why did you write this many words about a small open source project?

Wallaby is a really important project to me. It's dumb to be emotionally
attached to a piece of code, but it doesn't make it not true. I'm broken
inside and the specific fracture lines have made me sentimental about
bits and bytes in a git repo. Don't judge me.

Wallaby isn't all that magical. Its arguably not even that popular
as far as open source goes. But its important to me because it represents
an important part of my life.

Tommy was actually the person who wanted to build wallaby in the first
place. I just supplied the name. Ecto 2.0 was about to come out and it
could support concurrent feature tests. At the time we were porting an
internal app from ruby to Elixir, ostensibly as a proof of concept but
really because Tommy and I wanted to be doing Elixir. We needed to write
feature tests. Neither of us liked hound and it didn't support concurrent
tests so Tommy said, "Lets just build our own testing framework". He did
the initial proof of concept to make it work with the ecto 2.0-RC and
wrote the initial plug that would extract metadata from the browser in
order to pair up browser sessions with transactions. I spent most of my
time working on the api and integration with webdriver.

At the time I was working at Carbon Five. I had been lurking around the
Elixir community for a few years. All I wanted at the time was to be
working in Elixir. I'm not even sure what drove that level of obsession.
It was borderline religious. I had seen a better way to build systems and
I was ready to nail my copy of Joe Armstong's thesis to the Church of
Silicon Valley Early Stage Startup's front door. But Carbon Five makes it
a point to not dictate technology choices for their clients. And at the
time there weren't any Elixir contracts.

During that time my only connection to Elixir was Wallaby. It provided
a tangible link to this community I wanted so desperately to be in. And
luckily for me the effort eventually paid off. I got to give a [talk at
ElixirConf](https://www.youtube.com/watch?v=TjOXbDJ-yw8) about Wallaby. I got to
speak at erlang factory (back when it was still called erlang factory) and
ElixirDaze. Traveling to conferences enabled me to meet other Elixir folks
like my dear friend Lance Halvorsen. Not only did Lance introduce me to
Mission Chinese but he trusted me enough to help me get my first Elixir job.
I got to meet people like Ben Marx, Johnny Winn, James Fish, Dave Thomas,
Paul Lamb, Jeff Weiss, Amos King, Anna Neyzberg, Saša Jurić, Sonny
Scroggin, Greg Mefford, Paul Schoenfelder, Fred Hebert, Josh Adams and
a ton of other people that I'm rudely forgetting. These are people who
have had a profound impact on my life both professionally and personally.
I talk to many of these people daily and I'm honored to call them friends.

It's dumb, but I can directly trace a lot of events in the last 2 years to
a stupid joke; "Yeah, and we can call it Wallaby".

## So what now?

Two incredibly important people that I've been waiting to bring up are
Tobias Pfeiffer and Aaron Renner. They're the other part of the Wallaby
core team and have made some invaluable contributions to the project.
They've both taught me a lot about managing an open source project and
working with people over the internet. I owe them so much and can't say
"Thank You" enough times to both of them. Its up to them how much
involvement they want to have in the project going forward.

The first commit to wallaby was over 2 years ago. Since then the direction
of my career has changed substantially. I don't use Wallaby on a daily
basis and I think its suffered because of that. I continually have a list
of things that I'd like to do with it but I don't make time for. But this
is also a tool that people depend on as part of their business. Wallaby
needs a maintainer that can be more involved in its development. To that
end I'm hoping someone else is interested in taking over that role.

It might be that maintaining Wallaby just isn't interesting to anyone, in
which case Wallaby will probably largely stay as it is today. But if
you're looking for a way to get involved in the community or to start
a career in Elixir then maybe its for you. It certainly worked for me.
