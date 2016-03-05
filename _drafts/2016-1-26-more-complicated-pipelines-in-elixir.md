---
layout: post
title:  "Optimizing your elixir pipes"
date:   2016-01-26 1:12:00
categories: elixir
---

One of the best pieces of elixir is the `|>`. The humble pipeline operator.

The pipe operator is similar to `.` in haskell or `|>` F#.

A good part of programming, and functional programming in general is data transformation. We take some data as an input, transform it in independent steps, and return the fully transformed piece of data.

Lets look at what it does:

```elixir
"chris keathley"
|> String.split(" ")
|> Enum.map( &String.capitalize/1 )
|> Enum.join(" ")
# => "Chris Keathley"
```

In reality when this is just some syntax sugar. In the end what our code really looks like is this:

```elixir
"chris keathley"
Enum.join((Enum.map(String.split("chris keathley", " "), &String.capitalize/1 ), " ")
# => "Chris Keathley"
```

The pipe operator allows us to *compose* together these independent functions into a series of data transformations. But by doing this we don't lose any clarity in our code. In fact the pipe helps us to emphasize visually the transformation steps.

# Pipe is function composition
* Expressive code that still reads like imperative data transformation steps
* composition of pure functions

# Problems
* What happens when we need to supply more arguments?
* What about if we have functions that we can't pipe to?
* What about if we want to take different actions based on the response? (case statements)

## solution - Always start with data

We can make sure that this is very clear to everyone by always starting our pipeline with our data. This bit of expressive-ness is useful for those coming after us trying to determine what it is that we're doing. It also makes refactoring easier.

For instance if I decide that I want to change the type of data that I'm using I can just do that in one place and update any other call sites in order to compose functions together.

## Solution - Just use tuples

* Tuples allow us to wrap up our data
* Passing tuples we can easily pipe functions and then just use pattern matching to handle each case

## Solution - Protocols and Structs

* An even more powerful option is to use Structs. If your data is well defined then you can just create structs for them.
* Then we can create protocols for the specific functions that we care about
* Now if our data changes we can still use our pipeline of transformations and we'll use the correct implementation based on the data itself.

I wouldn't reach for this method until I was sure that I had well defined domains for my data. But if its something that you have then using protocols and structs is a fantastic solution.

## Problem - Having to care about errors down the line

## Solution - Just don't do it

## Solution - Use bang methods

## Solution - Use a Maybe monad - haha Just kidding

## Solution - Use with
