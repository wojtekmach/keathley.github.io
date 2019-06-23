---
layout: post
title:  "Phoenix: The difference between web and lib"
date:   2016-01-14 1:12:00
categories: elixir phoenix
---

In Phoenix we have 2 distinct places to put our code: the `web` directory and the `lib` directory. If you’re coming from another framework like Rails then it might be tempting to think of `web` as your `app` directory and `lib` as a junk drawer of miscellaneous modules and tasks.

But remember that Phoenix is just [OTP](http://elixir-lang.org/getting-started/mix-otp/introduction-to-mix.html)!

In fact the biggest difference between `web` and `lib` is that everything in the `web` directory is hot reloaded. This means that when you change a file in `web` the next time a web request is made that file will be recompiled.

What this means in practice is that we can organize our code based on its *state management*.

Since everything in `web` is reloaded for each request the `web` directory is the perfect place to put anything that needs to manage state only for the duration of that request. Conversely, `lib` is the perfect place to put anything that needs to manage state outside of the lifecycle of a request. For instance other Supervisors, Agents, or GenServers.

I think that this distinction is great because it emphasizes the fact that Phoenix is just an OTP application. We aren’t bound to the same request/response cycle present in other frameworks. We can build Phoenix applications the same way that we would build any other elixir or erlang application. The web components are just a piece of that application. Not the application itself.
