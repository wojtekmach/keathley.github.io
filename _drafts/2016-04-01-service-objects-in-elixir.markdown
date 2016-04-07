---
layout: "post"
title: "service-objects-in-elixir"
date: "2016-04-01 09:42"
---
I'm definitely not an FP expert but from my point of view there's nothing necessarily _wrong_ with this pattern.

I tend to create functions that interact with one external source at a time (database, external service, etc.) and then connect them together in the controller action.

If I was going to change anything here it would be to move the non-database specific code into its own module. For instance I would remove the `notify_team/1` function from this module and call `notify_team` directly in the controller or put it in a module and call the function on that module. Moving the notify call out of this module removes hidden side effects from your `run/1` function. Your `run/1` function is now simpler, easier to test in isolation and will be easier to re-use. It also means that you can re-use `notify_team/1` in other parts of your application.

The other benefit that this has is that the failure cases for these two actions are no longer coupled together. If you need to handle failures more explicitly then you can do so in the controller action.

Thats the pattern that I use but I'm sure there are others with even better ideas then mine.
