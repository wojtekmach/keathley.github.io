---
layout: post
title:  "Flashing Raspberry pi on Mac"
date:   2015-04-13 11:51:13
categories: raspberrypi
---

I was trying to follow this(https://www.raspberrypi.org/documentation/installation/installing-images/mac.md) document to flash an SD card for my new raspberry pi 2.  I was having real problems with the speed.  I let the device format for about an hour before I just gave up.

Pro-tip: You can bonk ctrl+t while the drive is formatting and get a status update from dd.

Apparently this speed issue is a known problem and is because the “disk2s1” name that OS X assigns to the sd card is incorrect.  The work around is pretty straight forward.  Simply replace `diskn` with `rdiskn`.

This will greatly increase your speeds.
