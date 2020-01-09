---
layout: post
title:  "Configuring Elixir apps: an opinionated approach"
date:   2020-01-09 08:53:00
categories: elixir configuration
---

After substantial changes, I recently published a new version of
[vapor](https://github.com/keathley/vapor).

I've been outspoken about my dislike of `config.exs` as a way to provide
runtime configuration. There are still elixir libraries that do this and
its a terrible pattern. My goal is to convince you that this is a bad
idea.

## Application config is global

## Config should be passed in from the top

## You should control how you store configuration

