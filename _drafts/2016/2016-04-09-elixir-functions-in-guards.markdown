---
layout: "post"
title: "elixir-functions-in-guards"
date: "2016-04-09 20:29"
---

A common question amongst new Elixir-ists is, "Why can I only use certain functions in guards?". This is an understandable question. At first glance it may not make sense why there are a handful of "blessed" functions and using anything else gets you a compiler error. In case you want to stop reading now heres the short version:

TL;DR - Functions in Elixir aren't guaranteed to be pure so you can't use them as guards.

This answer isn't *wrong*, but its also not complete. To fully understand the *reason* behind this decision we need to understand how Elixir implements pattern matching, functional purity, and a bit of the history of Erlang.

## Purity and Side effects

Purity is a term thats used a lot in functional programming circles. Without getting too rigorous a function is "pure" if it:

1) Always returns the same result given the same argument
2) Has no side effects.

The first clause is easy enough to understand so lets focus on the second clause. Side effects in this case refer to modifying state or interacting with the outside world. This could involve reading some state from an ETS table, talking to another process, or reading some data off the disk.

Elixir, much like Erlang, isn't a "pure" language in the sense that we're describing here. Thats OK. We rely heavily on being able to perform IO, send messages between processes, etc.

However, it does mean that we can't **rely** on all Elixir functions being pure.

## How does pattern matching even work?

Elixir relies on Erlang and BEAM whenever possible. In fact one of Elixir's core design tenants is to never re-invent a solution to a problem that Erlang has already solved. Pattern matching is no exception.

Rather then recreate a pattern matching system, Elixir uses Erlang's. Because of this Elixir can take advantage of the optimizations and efficiencies built into Erlang's pattern matching. But it also means that it has to obey the same rules. In this case it means that it has to follow the same rules about guards.

## A long time ago...

The creators of Erlang knew that if you wanted to allow for functions inside a guard clause then you would have to ensure that those functions were:

1) fast
2) pure

Unfortunately there's no trivial way to verify this in Erlang. Instead a small set of fast and pure functions were created for use in guard clauses.

## Defining our own guards

We can't use any old function in guard clauses, however that doesn't mean that we're totally out of options. As long as we confine ourselves to using the built-in functions we can achieve what we want with macros!

Instead of defining `even?/1` as a function we can define it instead as a macro:

```elixir
defmodule MyGuards do
  defmacro even?(x) do
    quote do: rem(unquote(x), 2) == 0
  end
end

defmodule Matcher do
  import MyGuards

  def match(x) when even?(x), do: IO.puts "Its a match!!!"
  def match(_), do: IO.puts "Its not a match"
end
```

When this code is compiled the `even?(x)` will be replaced with `rem(x, 2) == 0` which is valid in a guard clause.

## Conclusion

Programming is a discipline of tradeoffs. Understanding those tradeoffs is crucial to understanding the solutions in a language. Erlang made a tradeoff and decided that programmers should embrace communication over functional purity. That one decision has ripple effects through the entire language.

Personally I think thats fascinating as hell.
