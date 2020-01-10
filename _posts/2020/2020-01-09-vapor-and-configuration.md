---
layout: post
title:  "Runtime Configuration in Elixir Apps"
date:   2020-01-09 08:53:00
categories: elixir configuration
---

I gave a [talk last year](https://keathley.io/talks/stacking.html) about
how to properly boot elixir applications. In the talk, I showed how to load configuration values into an ETS table on boot, and this was the same pattern that I used initially in Vapor. I now think that this is a bad idea.

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

    opts = [strategy: :one_for_one, name: MyApp.Supervisor]
    status = Supervisor.start_link(children, opts)
  end
end
```

The user passes all of the configuration values to each child process as arguments. The child processes are completely re-usable; I can choose to start as many of them as I want in whatever way I want.

If you followed my (bad) advice in the talk, then you would have ended up in a situation like this:

```elixir
def start(_type, _args) do
  children = [
    ConfigStore,
    Database,
    Api,
    {Cache, name: MyCache},
  ]

  opts = [strategy: :one_for_one, name: MyApp.Supervisor]
  status = Supervisor.start_link(children, opts)
end
```

The config won't be loaded until the `ConfigStore` process has started.
This delay means it's not possible to pass configuration down as arguments, and each child needs to fetch configuration when it starts like so:

```elixir
defmodule Database do
  def init(args) do
    db_port = ConfigStore.get(:db_port)
    db_name = ConfigStore.get(:db_name)

    {:ok, [db_port: db_port, db_name: db_name]}
  end
end
```

Fetching config in `init` only works if you have control over the modules `init` callback. If you wrote the module, then you can do what you want. If the process came from a library, then you're probably limited. But, even if you could override `init`, you shouldn't. Fetching config in the `init` callback couples the process to the configuration provider, which, in effect, couples the process to how you boot your application. None of this is good.

You could start your `ConfigStore`  in the application start, which would allow you to pass arguments again.

```elixir
def start(_type, _args) do
  ConfigStore.start()

  children = [
    {Database, [
      db_host: ConfigStore.get(:db_host),
      db_name: ConfigStore.get(:db_name)]},
    {Api, port: ConfigStore.get(:web_port)},
    {Cache, name: MyCache},
  ]

  opts = [strategy: :one_for_one, name: MyApp.Supervisor]
  status = Supervisor.start_link(children, opts)
end
```

But now we've lost the ability to control the lifecycle of the config process. We've lost our ability to restart or recover from exceptions. If we make the mistake of linking the `ConfigStore` to our application process, then we could crash the entire app.

## What's the goal?

All of our children processes should be configurable by passing arguments to them. We shouldn't couple them to any global configuration system (this includes Application env).

When loading configuration, we need to enforce that all of the required
config values are present. If anything is missing or doesn't conform
correctly, the user should be free to halt the boot process or trigger an
alarm. Those are the goals.

## Vapor

[Vapor](https://github.com/keathley/vapor) is a library that I've been
toying with to try to encapsulate these patterns. The latest version includes some breakages, but I think they're for the better. Like most design problems, the real solution was to do fewer things. With the latest version of Vapor you'll be able to do this:

```elixir
defmodule MyApp do
  use Application

  def config!() do
    providers = [
      %Env{bindings: [
        db_host: "DB_HOST",
        db_name: "DB_NAME",
        web_port: "PORT",
      ]
    ]

    Vapor.load!(providers)
  end

  def start(_type, _args) do
    config = config!()

    children = [
      {Database, [db_host: config.db_host, db_name: config.db_name]}, 
      {Api, port: config.web_port},
      {Cache, name: MyCache},
    ]

    opts = [strategy: :one_for_one, name: MyApp.Supervisor]
    status = Supervisor.start_link(children, opts)
  end
end
```

Vapor allows users to specify a list of providers, and the configuration
values the provider should return. It then "loads" the configuration from
each provider and returns a map. In the example above, if
any of the values are missing, an exception is thrown. If throwing
exceptions isn't your jam, there is also `load/2`, which returns the
standard ok-error-tuple. The user is free to do whatever they want
with the map. They can configure their processes once and throw it away, store it in ETS, `Application.put_env`, or whatever else.

There are a bunch of other features in Vapor so [check it
out](https://github.com/keathley/vapor) if it seems interesting to you.

## Conclusion

Even if you don't want to use Vapor, I hope that this at least showcases
some useful patterns. Avoid coupling your processes to any global configuration. If you're going to fetch application configuration at
runtime, then it should enforce the values that you've specified. Finally,
library authors: If you're going to spawn processes, let me pass arguments to you. Don't use application config unless there is no other option (side note: there's always another option). 

I have more to say about patterns for avoiding `Application.get_env` but
that deserves a separate post.
