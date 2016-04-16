---
layout: "post"
title: "Why you can't use your own functions in Elixir guard clauses"
date: "2016-04-09 20:29"
---

Elixir, like other pattern matched languages, supports guard clauses. Inside a guard we can use a set of Kernel functions and other predicates. Using any other function in a guard clause results in an error. Here's an example to show what I mean.

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

  def kid?(age) do
    6 < age and age < 12
  end

  def teen?(age) do
    12 < age and age < 18
  end

  def elder?(age) do
    60 < age
  end
end

defmodule Greeting do
  def greet(%{age: age}) when kid?(age), do: "Hiya"
  def greet(%{age: age}) when teen?(age), do: "Whatever"
  def greet(%{age: age}) when elder?(age), do: "You kids get off my lawn"
  def greet(_), do: "Hello"
end
```

But if we try to run this code we get an error:

```console
> elixir guards.exs
** (CompileError) guards.exs:18: cannot invoke local kid?/1 inside guard
    guards.exs:18: (module)
```

What Elixir is telling us is that we can't use our functions inside of a guard clause.

This error causes quite a bit of confusion amongst new Elixirists so I thought I would try to break down why we can't use any old function in a guard clause.

## Purity and Side effects

Purity is a oft used term in functional programming circles. Without getting too rigorous a function is "pure" if it:

1. Always returns the same result given the same argument
2. Has no side effects.

The first clause is easy enough to understand so lets focus on the second.

Side effects in this case refer to modifying state or interacting with the outside world. This could involve reading some state from an ETS table, talking to another process, or reading some data off a disk. Elixir, much like Erlang, isn't a "pure" language in the sense that we're describing here. Thats OK. We rely heavily on performing IO, sending messages between processes, interacting with filesystems. Its a crucial part of Elixir and Erlang's usability.

This doesn't mean that Elixir functions **can't** be pure. In fact, most people (myself included) will tell you that you should write pure functions whenever possible. But it does mean that we can't **rely** on Elixir functions being pure.

## Elixir's pattern matching

Elixir relies on Erlang and BEAM whenever possible. In fact one of Elixir's core design tenants is to never re-invent a solution to a problem Erlang has already solved. Pattern matching is no exception.

Rather then recreate a pattern matching system Elixir uses Erlang's. Because of this Elixir gets the optimizations and efficiencies already built into Erlang's pattern matching. But it also means that it has to obey Erlang's rules.

Patterns are matched from the first definition to the last. The first function definition that matches is the function definition used. If those function definitions have guard clauses then the guard clause must be resolved before the next function definition can be tested.

For instance, if we had a function that could accept a list or a single element we might write some definitions like this:

```elixir
def feed_the_pets(cats) when is_list(cats), do: Enum.each(cats, &feed/1)
def feed_the_pets(cat), do: feed(cat)
```

Our first definition `def feed_the_pets(cats) when is_list(cats)` would have to wait until `is_list/1` resolves before `def feed_the_pets(cat)` can be tested.

## A long time ago...

The creators of Erlang knew that if you wanted to allow for functions inside a guard clause then you would have to ensure that those functions were:

1. Fast
2. Pure

Because each function definition is tested in order guard clauses have to execute fast. If a function used in a guard clause wasn't pure then it would be possible for that guard clause to be non-deterministic. Matching against a function definition would become, essentially, random.

There's no trivial way to verify functional purity in Erlang. Instead the creators decided to limit guard clauses to internal functions. That way they could ensure that these functions were both fast and pure.

This is why we get errors when we try to use our own functions in guard clauses. Erlang has no way of knowing if that function is safe. Instead it stops you from using the function at all.

## Defining our own guards - Macros FTW.

We can't use any old function in guard clauses, but that doesn't mean that we're totally out of options. As long as we confine ourselves to the built-in functions and predicates that Elixir provides then we can achieve what we want with macros!

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

Its easy to get frustrated when we encounter a confusing error message or some unexpected behavior. But its important to remember that a large part of progamming is making tradeoffs. Once we think through the constraints of the problem its easier to understand why the authors made the tradeoffs they did.

The creators of Erlang made a decision that programmers should embrace communication over functional purity and made the appropriate tradeoffs. That one decision has ripple effects through the entire language.
