--
layout: post
title:  "Configuring Elixir apps: an opinionated approach"
date:   2020-01-09 08:53:00
categories: elixir configuration
---

After substantial changes, I recently published a new version of
[vapor](https://github.com/keathley/vapor).

I've been outspoken about my dislike of `config.exs` as a way to provide
runtime configuration. There are still elixir libraries that do this and
its a terrible pattern. My goal is to convince you of that fact.

I gave a [talk last year](https://keathley.io/talks/stacking.html) about
how to properly boot elixir applications. In the talk I showed how to load
configuration into an ets table on boot. This was the same pattern that
I originally used in Vapor. I now think that this is a bad idea.

The ideal way to configure all of your children processes looks like this:

```elixir
defmodule MyApp do
  use Application

  def start(_type, _args) do
    children = [
      {Database, [db_host: "host", db_name: "blog_posts"]}, 
      {Api, port: 4000},
      {Cache, name: MyCache},
    ]

    opts = [strategy: :one_for_one, name: Layserbeam.Supervisor]
    status = Supervisor.start_link(children, opts)
  end
end
```

All of the configuration values are passed down from the top level. This
means that all of our child processes are maximally reusable. Its trivial
to start each of these processes indepentently, which makes testing and
development easier.

If you followed my (bad) advice in the talk then you would have ended up
in a situation like this:

```elixir
def start(_type, _args) do
  children = [
    VaporConfig,
    Database,
    Api,
    {Cache, name: MyCache},
  ]

  opts = [strategy: :one_for_one, name: Layserbeam.Supervisor]
  status = Supervisor.start_link(children, opts)
end
```

The config won't be loaded until the `VaporConfig` process has started.
This delay means its not possible to pass configuration down as arguments
and each child needs to fetch configuration when it starts like so:

```elixir
defmodule Database do
  def init(args) do
    db_port = VaporConfig.get(:db_port)
    db_name = VaporConfig.get(:db_name)

    {:ok, [db_port: db_port, db_name: db_name]}
  end
end
```

This is assuming you even have control over the `init` callback for the
process you're trying to start. If you wrote the process then you probably
do. If the process came from a library then you probably don't. But even
if its possible to do this you really shouldn't. Fetching config in the
init callback couples the process to the configuration provider which, in
effect, couples the process to your specific boot process. None of this is
good.

A way around this would be to start the configuration process in your
application start:

```elixir
def start(_type, _args) do
  VaporConfig.start()

  children = [
    {Database, [db_host: VaporConfig.get(:db_host), db_name:
    VaporConfig.get(:db_name)]}, 
    {Api, port: VaporConfig.get(:web_port)},
    {Cache, name: MyCache},
  ]

  opts = [strategy: :one_for_one, name: Layserbeam.Supervisor]
  status = Supervisor.start_link(children, opts)
end
```

Starting the config process this way allows us to pass values to our
children, which is good, but now we've lost the ability to control the
lifecycle of the config process. We've lost our ability to restart or
recover from exceptions.

## Whats the goal?

We want to be able to




Library authors: If you're going to spawn processes let me pass arguments
to you. Don't use application config unless there is literally no other
option (there's always another option).

## Application config is global

## Config should be passed in from the top

## You should control how you store configuration

