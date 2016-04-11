---
layout: "post"
title: "elixir-functions-in-guards"
date: "2016-04-09 20:29"
---

Elixir, like other pattern matched languages, supports guard clauses. Inside a guard we can use a set of Kernel functions and other predicates. If any other function is used in a guard clause it results in an error. Here's an example to show what I mean:

Lets say that we have a user and we want to produce a greeting for that user depending on how old the user is:

```elixir
defmodule User do
  defstruct age: 0
end

defmodule Greeting do
  def greet(%{age: age}) when 6 < age and age < 12, do: "Hiya"
  def greet(%{age: age}) when 12 < age and age < 18, do: "Whatever"
  def greet(%{age: age}) when 60 < age, do: "You kids get off my lawn"
  def greet(_), do: "Hello"
end
```

It would be nice if we could encapsulate each of those guard clauses into a re-usable function. Something like this:

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

But if we try to run this code we get an error:

```console
> elixir guards.exs
** (CompileError) guards.exs:18: cannot invoke local kid?/1 inside guard
    guards.exs:18: (module)
```

What Elixir is telling us is that we can't use the functions that we've defined inside of a guard clause. But does that make any sense? `is_list/1` and `is_binary/1` are functions and we can use those in guards. Why can't we use functions that we define?

To fully understand the reason behind this decision we need to understand functional purity, Elixir's pattern matching, and a bit of Erlang's history.

## Purity and Side effects

Purity is a oft used term in functional programming circles. Without getting too rigorous a function is "pure" if it:

1. Always returns the same result given the same argument
2. Has no side effects.

The first clause is easy enough to understand so lets focus on the second.

Side effects in this case refer to modifying state or interacting with the outside world. This could involve reading some state from an ETS table, talking to another process, or reading some data off a disk. Elixir, much like Erlang, isn't a "pure" language in the sense that we're describing here. Thats OK. We rely heavily on performing IO, sending messages between processes, interacting with filesystems. Its a crucial part of Elixir and Erlang's usability.

This doesn't mean that Elixir functions can't be pure. In fact, most people (myself included) will tell you that you should try to write pure functions whenever possible. But it does mean that we can't **rely** on Elixir functions being pure.

## Elixir's pattern matching

Elixir relies on Erlang and BEAM whenever possible. In fact one of Elixir's core design tenants is to never re-invent a solution to a problem Erlang has already solved. Pattern matching is no exception.

Rather then recreate a pattern matching system Elixir uses Erlang's. Because of this Elixir gets the optimizations and efficiencies already built into Erlang's pattern matching. But it also means that it has to obey the same rules. In this case it means that Elixir's guard clauses have to follow Erlang's rules about guard clauses.

## A long time ago...

The creators of Erlang knew that if you wanted to allow for functions inside a guard clause then you would have to ensure that those functions were:

1. Fast
2. Pure

There's no trivial way to verify functional purity in Erlang. So instead the creators decided to limit guard clauses to internal functions. That way they could ensure that these functions were both fast and pure.

This is why we get errors when we try to use our own functions in guard clauses. Erlang has no way of knowing if that function is safe. Instead it simply stops you from using the function at all.

## Defining our own guards - Macros FTW.

We can't use any old function in guard clauses, but that doesn't mean that we're totally out of options. As long as we confine ourselves to the built-in functions and predicates we can achieve what we want with macros!

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

When this code is compiled each of the macros will be replaced with the valid predicate. We can still encapsulate our domain logic and because we're using the available operators we don't have a compilation error.

## Conclusion

Programming is a discipline concerned with tradeoffs. Its easy to get frustrated when we encounter a confusing error message or some unexpected behaviour. Once we think through the tradeoffs though its often apparent why the solutions were chosen in the first place.

Erlang made a tradeoff and decided that programmers should embrace communication over functional purity. That one decision has ripple effects through the entire language.

Personally I think thats fascinating as hell.
