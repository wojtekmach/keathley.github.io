---
layout: post
title:  "Soft deletion with Ecto"
date:   2019-01-10 04:07:00
categories: elixir ecto
---

A common need in web applications is to "undo" a deletion event. This is referred
to as a soft-deletion. The record still exists but its hidden from the user.
Soft deleting allows the user to restore that data in the event that they need it in
the future.

Implementing this behaviour is a question that comes up a lot with ecto and I wanted to show my strategy for handling these sorts of situations. We're going to use
postgres as our database of choice but these concepts should translate pretty
well to other systems.

## The Solution

If you want to just see all the code together the full source for these examples
is [here](https://github.com/keathley/soft_delete).

I'm assuming you have a working elixir application with Ecto already setup.
With that assumption in mind lets work on creating some tables. For our
purposes we're going to allow people to soft-delete widgets from the system. So
we need a widgets table:

```elixir
defmodule SoftDelete.Repo.Migrations.CreateWidgets do
  use Ecto.Migration

  def change do
    create table(:widgets) do
      add :name, :string
      add :deleted, :boolean, default: false
    end

    create index(:widgets, [:deleted])
  end
end
```

We're adding a new table with a `deleted` column that defaults to false. The
schema looks similar:

```elixir
defmodule SoftDelete.Widget do
  use Ecto.Schema

  import Ecto.Query, only: [from: 2]

  schema "widgets" do
    field :name, :string
    field :deleted, :boolean, default: false
  end
end
```

When we want to "delete" a schema a convenient way is to use a dedicated changeset:

```elixir
defmodule SoftDelete.Widget do
  def mark_for_deletion(widget) do
    widget
    |> change(%{deleted: true})
  end
end
```

Which can be used like so:

```
iex(1)> w = SoftDelete.Repo.get(SoftDelete.Widget, 1)

16:52:46.496 [debug] QUERY OK source="widgets" db=1.1ms decode=1.2ms queue=1.2ms
SELECT w0."id", w0."name", w0."deleted" FROM "widgets" AS w0 WHERE (w0."id" = $1) [1]
%SoftDelete.Widget{
  __meta__: #Ecto.Schema.Metadata<:loaded, "widgets">,
  deleted: false,
  id: 1,
  name: "foo"
}
iex(2)> w |> SoftDelete.Widget.mark_for_deletion() |> SoftDelete.Repo.update

16:53:11.386 [debug] QUERY OK db=1.7ms queue=0.6ms
UPDATE "widgets" SET "deleted" = $1 WHERE "id" = $2 [true, 1]
{:ok,
 %SoftDelete.Widget{
   __meta__: #Ecto.Schema.Metadata<:loaded, "widgets">,
   deleted: true,
   id: 1,
   name: "foo"
 }}
```

Now that we can mark widgets as deleted we need some way to scope our queries
as well. A good first approach is to use queries:

```elixir
defmodule SoftDelete.Widget do
  def alive(query) do
    from w in query,
      where: w.deleted == false
  end
end
```

Now we can compose this function with other queries in order to only fetch
"alive" widgets from the database:

```elixir
iex(2)> Widget |> Widget.alive |> Repo.all

17:00:06.985 [debug] QUERY OK source="widgets" db=1.6ms decode=2.0ms queue=0.8ms
SELECT w0."id", w0."name", w0."deleted" FROM "widgets" AS w0 WHERE (w0."deleted" = FALSE) []
[
  %SoftDelete.Widget{
    __meta__: #Ecto.Schema.Metadata<:loaded, "widgets">,
    deleted: false,
    id: 2,
    name: "bar"
  },
  %SoftDelete.Widget{
    __meta__: #Ecto.Schema.Metadata<:loaded, "widgets">,
    deleted: false,
    id: 3,
    name: "foo"
  },
  %SoftDelete.Widget{
    __meta__: #Ecto.Schema.Metadata<:loaded, "widgets">,
    deleted: false,
    id: 4,
    name: "bar"
  }
]
```

Perfect! We can now soft-delete widgets.

## An improvement

It can be tedious to include a function every time we need to fetch widgets from
the database. Arguably it shouldn't take additional steps to do the thing we always
want to do. Plus if you're using ecto associations then query composition can become...
tricky. While there are a few ways to improve this situation my personal
preference is to use views.

In order to put our view solution into place we'll need a new migration and a new
schema:

```elixir
defmodule SoftDelete.Repo.Migrations.CreateAliveWidgets do
  use Ecto.Migration

  @up "CREATE VIEW alive_widgets AS select id, name from widgets where not deleted;"
  @down "DROP VIEW IF EXISTS alive_widgets;"

  def change do
    execute(@up, @down)
  end
end

defmodule SoftDelete.AliveWidget do
  use Ecto.Schema

  schema "alive_widgets" do
    field :name, :string
  end
end
```

The view itself is easy to manage and update. Admittedly we're potentially taking
a performance hit here. In that case it would probably be better to use a
materialized view. But that involves setting up triggers and some other concepts.
Our basic solution should work well for most cases. If you have so much data
that this is causing you performance problems then you probably already
understand your use case and I'm sure you can re-implement this with
materialized views.

Huzzah for building your own solutions.

Now when we need to select our widgets we can use the `AliveWidget` schema.

```elixir
iex(3)> Repo.all(AliveWidget)
[
  %SoftDelete.AliveWidget{
    __meta__: #Ecto.Schema.Metadata<:loaded, "alive_widgets">,
    id: 2,
    name: "bar"
  },
  %SoftDelete.AliveWidget{
    __meta__: #Ecto.Schema.Metadata<:loaded, "alive_widgets">,
    id: 3,
    name: "foo"
  },
  %SoftDelete.AliveWidget{
    __meta__: #Ecto.Schema.Metadata<:loaded, "alive_widgets">,
    id: 4,
    name: "bar"
  }
]

17:09:50.343 [debug] QUERY OK source="alive_widgets" db=1.4ms queue=2.2ms
SELECT a0."id", a0."name" FROM "alive_widgets" AS a0 []
```

This provides us a nice read only interface and allows us to easily compose our schema 
with the rest of ecto.

## Potential Improvements

There are a few other improvements we could make to this solution. As I mentioned
above, if you're craving read performance and have a relatively low number of
writes (and you're on postgres) you could look into materialized views.

Another improvement would be to substitute the boolean column for a timestamp.
Many people opt to do this because it provides an easy way to ask, "When was
this deleted". A general implementation would be to allow the column to be null
and add a timestamp when the widget is deleted. Our view would need to change
to select only rows that have a null `deleted_at` column. I leave this as an
exercise for the reader.

More then anything I hope that this post shows how to think through these kinds
of problems and encourages you to build these solutions on your own instead of
reaching for a library. Libraries certainly have their place. But when it comes
to data management I'm always inclined to do it on my own.
