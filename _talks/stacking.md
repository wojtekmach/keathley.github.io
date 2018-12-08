---
layout: talk
title: "Building Resilient Systems with Stacking Theory"
date:   2018-10-26
---

When you're building large scale systems failure is inevitable. Whether its dropped network connections, misbehaving hardware, massive GC pauses, or AWS outages our services should be able to weather the storm. At Bleacher Report we need to handle large scale traffic while also handling whatever transient failures come our way. To achieve this goal we've been exploring a design technique called Stacking Theory.

This talk describes ways to build more resilient systems by walking through the seemingly mundane task of booting an elixir application. By going through this exercise we'll see some different ways to structure our applications that reduce our dependence on external systems.

## Slides
<script async class="speakerdeck-embed" data-id="ee1735a8f9bd42ff8d4938975c9827dc" data-ratio="1.77777777777778" src="//speakerdeck.com/assets/embed.js"></script>
