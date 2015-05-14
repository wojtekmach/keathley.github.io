---
layout: post
title:  "Flashing Raspberry Pi on Mac"
date:   2015-04-13 11:51:13
categories: raspberrypi
---

I just got a Raspberry Pi 2 for me and my daughter to hack around on.

Part of the process in setting up a Pi is that you need to flash a SD with one of a
number of custom linux images.  I was following the flashing instructions at (https://www.raspberrypi.org/documentation/installation/installing-images/mac.md) and I was having real problems with the speed.  I let the device format for about an hour before I just gave up.

Quick Pro-tip: You can bonk ctrl+t while the drive is formatting and get a status update from dd.

Searching around yielded very little help.  Most answers were something along the lines of "Your SD card reader is probably broken".  However, I finally discovered the culprit.

Apparently on OS X the `disk2s1` name is actually some sort of pointer or symlink to actual name of the drive.  The work around is pretty straight forward:

Simply replace `diskn` with `rdiskn`

`rdiskn` is the actual name of the disk which `diskn` points to.  I'm honestly not sure why this causes such a speed decrease (and it's definitely worth looking into).  Also if anyone knows how to submit a patch to the Raspberry Pi docs please let me know.
