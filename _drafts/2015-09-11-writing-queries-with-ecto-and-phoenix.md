---
layout: post
title:  "Composing queries with ecto"
date:   2015-09-11 14:10:00
categories: elixir ecto phoenix
---

Ecto is the defacto standard, query engine for Phoenix applications and its the preferred way to do database access in Elixir. It's based on the Repository pattern and provides a DSL for writing database queries.

I've been working with Rails and ActiveRecord specifically. While AR is far from perfect there were a few things that I needed to be able to do with Ecto.

The most important thing to realize is that Ecto is not an ORM. In fact its more similar to LINQ from the .NET world then an ORM. That can take some getting used to if you're used to the magic of ActiveRecord. However, it didn't take long for me to see the power in Ecto and normal queries.

Let's look at some of the common scenarios in Rails and see how we can implement them in Ecto.

One of the most common practices in Rails and active record is to create query scopes.

For instance if we have a `Post` object with a `published_at` field and we wanted to query for all of the published `posts` ordered by most recent then we could do something like this:

``` ruby
class Post < ActiveRecord::Base
  scope :published -> { where.not(published_at: nil) }
  scope :recent -> { order(published_at: :desc) }
end

Post.published.recent
```

This works fairly well despite any misgivings about the `where.not`. However, in my experience these scopes can quickly become complicated and difficult to maintain. While the ActiveRecord api provides flexibility it typically involves resorting to hand writing sql queries as strings or dropping down into Arel.

Let's look at how we would solve this same problem with elixir and ecto.

If we were to just write out everything in Ecto it would look something like this:

``` elixir
query = from p in Post,
  where: not is_nil(p.posted_at),
  order_by: [desc: p.posted_at]

Repo.all(query)
```

While the code for this query is self-evident, the domain logic is harder to understand. Luckily this is easy enough to do with Ecto.

We can create a Post model like so:

``` elixir
defmodule Post do
  use Ecto.Model

  import Ecto.Query

  schema "posts" do
    field :title
    field :content
    field :posted_at, Ecto.DateTime

    timestamps
  end

  def recent(query) do
    from p in query,
    order_by: [desc: p.posted_at]
  end

  def published(query) do
    query |> where([p], not is_nil(p.posted_at))
  end
end
```

something something easy to extend queries using the from syntax.

In order to compose queries we simply need a function that takes a query as an argument and returns a query. The `published/1` function on extends the query simple to show that this is possible. Its just as possible to use the `from/2` macro instead. In either case we can call our query like so:

``` elixir
EctoTest.Post
|> EctoTest.Post.recent
|> EctoTest.Post.published
|> Repo.all
```

Or if you prefer a bit more brevity you can import the Post module first:

``` elixir
import EctoTest.Post

EctoTest.Post
|> recent
|> published
|> EctoTest.Repo.all
```

This query syntax is very powerful. We've managed to encapsulate our domain logic while maintaining very reasonable queries.

Queries are easy enough to compose this way, but what happens when we have relationships between models?

Lets see what happens if we add a comment model.

``` elixir
```

``` elixir
```

## Creating the test application

First lets create a new application to play with:

    $ mix new query_test --sup

We'll also need to add ecto as a dependency. We can do that by adding both ecto and postgrex (the postgres adapter for elixir) to the mix.exs.

``` elixir
defmodule EctoQueries.Mixfile do
  use Mix.Project
  #...
  def application do
    [applications: [:logger, :ecto, :postgrex],
     mod: {EctoQueries, []}]
  end

  defp deps do
    [
      {:ecto, "~> 1.0"},
      {:postgrex, ">= 0.0.0"}
    ]
  end
end
```

Note that you'll also need to add ecto and postgrex to the applications for our project.

Now that we've done that let
