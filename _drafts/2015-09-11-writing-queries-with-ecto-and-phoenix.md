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

Lets look at an example of this same problem in Ecto and see how we would go about solving it.

## Creating the test application

First lets create a new application to play with:

    $ mix new query_test --sup

We'll also need to add ecto as a dependency. We can do that by adding 
