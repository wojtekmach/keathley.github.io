---
layout: "post"
title: "elixir-functions-in-guards"
date: "2016-04-09 20:29"
---

Lets say that we have a user:

```elixir
defmodule User do
  defstruct age: 0
end
```

And we want to produce a greeting for that user depending on how old the user is:

```elixir
defmodule Greeting do
  def greet(%{age: age}) when 6 < age and age < 12, do: "Hiya"
  def greet(%{age: age}) when 12 < age and age < 18, do: "Whatever"
  def greet(%{age: age}) when 60 < age, do: "You kids get off my lawn"
  def greet(_), do: "Hello"
end
```

This certainly works, but it would be nice if we could encapsulate each of those guard clauses into a re-usable function:

```elixir
defmodule User do
  defstruct age: 0

  def kid?(user) do
    6 < user.age and user.age < 12
  end

  def teen?(user) do
    12 < user.age and user.age < 18
  end

  def elder?(user) do
    60 < user.age
  end
end

defmodule Greeting do
  def greet(user) when kid?(user), do: "Hiya"
  def greet(user) when teen?(user), do: "Whatever"
  def greet(user) when elder?(user), do: "You kids get off my lawn"
  def greet(_), do: "Hello"
end
```

However, if we try to run this we get:

```console
> elixir guards.exs
** (CompileError) guards.exs:18: cannot invoke local kid?/1 inside guard
    guards.exs:18: (module)
```

It turns out that Elixir only allows a subset of "blessed" built-in functions inside guard clauses. There are ways around this issue, but to fully understand the *reason* behind this decision we need to understand Elixir's pattern matching, functional purity, and a bit of the history of Erlang.

## Purity and Side effects

Purity is a term thats used a lot in functional programming circles. Without getting too rigorous a function is "pure" if it:

1) Always returns the same result given the same argument
2) Has no side effects.

The first clause is easy enough to understand so lets focus on the second clause. Side effects in this case refer to modifying state or interacting with the outside world. This could involve reading some state from an ETS table, talking to another process, or reading some data off the disk.

Elixir, much like Erlang, isn't a "pure" language in the sense that we're describing here. Thats OK. We rely heavily on being able to perform IO, send messages between processes, etc.

But it does mean that we can't **rely** on all Elixir functions being pure.

## How does pattern matching even work?

Elixir relies on Erlang and BEAM whenever possible. In fact one of Elixir's core design tenants is to never re-invent a solution to a problem that Erlang has already solved. Pattern matching is no exception.

Rather then recreate a pattern matching system, Elixir uses Erlang's. Because of this Elixir can take advantage of the optimizations and efficiencies built into Erlang's pattern matching. But it also means that it has to obey the same rules. In this case it means that it has to follow the same rules about guards.

## A long time ago...

The creators of Erlang knew that if you wanted to allow for functions inside a guard clause then you would have to ensure that those functions were:

1) fast
2) pure

There's no trivial way to verify this in Erlang. Instead it was decided to limit the functions that could be used in a guard clause to those functions that **could** be guaranteed to be both fast and pure.

## Defining our own guards

We can't use any old function in guard clauses, but that doesn't mean that we're totally out of options. As long as we confine ourselves to using the built-in functions we can achieve what we want with macros!

Instead of defining `kid?/1`, `teen?/1`, and `elder?/1` as a functions we can define them as macros:

```elixir
defmodule User do
  defstruct age: 0

  defmacro kid?(age) do
    quote do: 6 < unquote(age) and unquote(age) < 12
  end

  defmacro teen?(age) do
    quote do: 12 < unquote(age) and unquote(age) < 18
  end

  defmacro elder?(age) do
    quote do: 60 < unquote(age)
  end
end

defmodule Greeting do
  import User

  def greet(%{age: age}) when kid?(age), do: "Hiya"
  def greet(%{age: age}) when teen?(age), do: "Whatever"
  def greet(%{age: age}) when elder?(age), do: "You kids get off my lawn"
  def greet(_), do: "Hello"
end
```

When this code is compiled each of the macros will be replaced with the valid predicate. This avoids the compilation error ensures that we're still using pure functions in our guard clause and allows us to add some extra domain knowledge to our guard clause.

## Conclusion

Programming is a discipline of tradeoffs. Understanding those tradeoffs is crucial to understanding the solutions in a language. Erlang made a tradeoff and decided that programmers should embrace communication over functional purity. That one decision has ripple effects through the entire language.

Personally I think thats fascinating as hell.
