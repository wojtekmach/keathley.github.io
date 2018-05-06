---
layout: post
title:  Testing network disconnects in elixir applications
date:   2018-02-18 15:20:00
categories: Elixir Docker
---

We recently had a few elixir libraries that were causing issues during
network partitions. Both of these libraries made a classic assumption that
an external resource would be always available. Of course, networks being
what they are, resources will always become unavailable. When this
happened each of these libraries would crash eventualy taking down our
application. In order to fix these problems we wanted an easy way to
simulate network partitions locally.

There are plenty of libraries for testing network partitions; Jepsen, Blockade, and Comcast. But for our purposes we just needed to a quick and dirty way to partition our elixir application from the resource. It turns out that this was relatively easy to do with docker.

## Creating the network

In order to test our network partitions we're going to create a new docker network and connect our containers manually. To create a new docker network you can run:

```
$ docker network create flaky
```

## Start the containers

Now we need to start redis and our elixir container and connect the two of them.

Starting redis is straightforward:

```
$ docker run --name test-redis redis
$ docker network connect flaky test-redis
```

Starting the elixir dependency is a little more involved but not by much. All we're going to do is start an elixir container with our working directory mounted as a volume. That way we can start an iex session with all of our code.

```
$ docker run -it --name app --network flaky -v $(pwd):/app elixir "/bin/bash"
```

This command starts our application container and automatically connects it to oour test network. Inside our new container we can go ahead and fire up an iex session and connect to our redis container.

```
$ iex -S mix
iex> {:ok, conn} = Redix.start_link(host: "test-redis")
```

Now that our app is connected to redis we can replicate our issue by starting a MULTI and then create a network partition.
