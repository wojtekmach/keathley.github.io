---
layout: post
title:  "Building a Keyboard Part 1: BOM and ordering parts"
date:   2015-05-31 23:00:00
categories: Keyboards Atreus
---

I decided to build an Atreus keyboard a while ago to try to combat some of the RSI issues that I've been having.  Originally I had sent away for a kit, but it was going to be several weeks before it shipped so I decided to source all of it myself.  

It took a bit of work to get a list of everything that I was going to need together so I thought that I would share hte list that I came up with.  

I've recently decided to


I've always been obsessed with keyboards.  The first large purchase I made when I had a real job was a matte black Das Keyboard Ultimate.  That keyboard has been with me for years now and its served me well.  

Recently I've been starting to have more issues with RSI so I've been looking for more ergonomic replacements.  After doing some research (and seeing pics of Tenderlove's) I decided to check out the Atreus.  If you're familiar with the ergodox its, essentially, a portable version of that.  I sent an email in for a kit but it seemed like it was going to take a bit of time so I decided to source the parts and assemble the kit myself. The experience was really enjoyable so I thought that I would try to document it all here.

# Sourcing the parts

Finding an up to date list of all of the parts actually took a bit of work (and digging through many geekhack threads.) Here's what I ended up with:

TODO - add list of things here.


## Case

The case was the trickiest piece of the whole thing. The important thing to watch out for is that you need a minimum width of 3 mm for whatever material you choose.  Any less then that and you'll start to get some flex.  If you're planning on soldering everything point to


While you could theoretically get away with a thinner material, if you're going to be soldering everything point to point then you're going to have to be very adept at soldering.  I was very pleased with how my soldering came out and I had just enough room to make it all work.  I ordered everything from ponoko. It was a very painless process. Just drag your file into the system and pick out your material.

The recomended material is pine; its cheap, lightweight, easy to work with, and gives the keyboard a really unique look.  I decided to go with a clear acrylic case only because I preferred the look of it.  I still like the look of the finished product, but you can't hide any of your crimes so if you're worried about your hot glue or soldering skills then you might not want to use a transparent material. If I had to do it over again I would probably go with the wooden case just because I ended up making a bit of a mess with the hot glue and the acrylic just became a bit harder to work with.  YMMV.


## Switches

Keyboard switches are one of the great debates in the hacker world (right next to emacs vs. vim and "which one of us is more socially awkward").  I knew that I was going to be using this in an office setting and that my co workers were somewhat concerned with the noise from the keyboard.  My personal favorite switches are browns.  However for the sake of my coworkers I decided to go with mx cherry clears. I cannot emphasize how much I regret this decision.  Some people really like the stiffer feel.  I am not one of those people.  I personally find them so stiff that they start to hurt my hands after a while.  If I was building a second keyboard (which is a very real possibility) then I would definitely go with browns and just add o-rings.  Switches are totally subjective so you should try some out before you make a decision.  If you're new to all of this then I recommend that you go with browns (and you should still just try them out).

## Keycaps

There is some debate about the benefits of DCS vs DSA keycaps for the Atreus.  Since I hadn't used one before I decided to go with a DCS set since those are what are recomended.

## Microcontroller

You can either use the A* micro or the teensy.  While the A* is more supported I decided to go with the teensy since I had experience with them.


# Assembly











I have a experience soldering and assembling electronics so I wasn't concerned with that. However, it was somewhat tricky trying to put together an up to date list of all of the parts that I would need.

## Case

The case presents an interesting challenge.  If you get a kit then you get one
by default.  However, since I've decided to source everything myself I needed
to find a different solution.  Luckily there was a solution in this geekhack
thread.

You can upload the `ready-for-ponoko.svg` file to ponoko.  It's designed to
work with the 3.2mm 24x12 birch plywood material.  From what I can tell the
birch works really well and is easy to work with.  I liked the look of the
birch but in the end I decided to go with acrylic instead.  It was more inline
with my personal aesthetics.  I chose the clear 3mm 31x15 acrylic sheet.  That
may turn out to be a terrible decision.  YMMV
