---
layout: post
title:  "Building an Atreus"
date:   2015-05-31 23:00:00
categories: "Keyboards" "Atreus" "DIY"
---

# Building a Keyboard

I've always been obsessed with keyboards. The first large purchase I made when I had a "real job" was a matte black [Das Keyboard Ultimate](http://www.daskeyboard.com/model-s-ultimate/) with MX Cherry Blue switches. That keyboard has been with me for years now and its served me well.  

Recently I've been having issues with RSI so I've been upgrading my ergonomics. One of those upgrades was an ergonomic keyboard. After doing some research I decided to check out the Atreus. The Atreus was designed by Phil Halberg (aka. Technomancy) as a portable replacement for an ergodox. Its a 40% keyboard but features the same "columnar layout" that the ergodox does.

Phil offers a kit which is what I originally ordered. However, there was going
be some lead time before the kit came in. Since the Atreus is open source I
opted to source the parts and assemble a kit myself.

## Sourcing the parts

Finding an up to date list of all of the parts actually took a bit of work (and digging through many geekhack threads.) Here's what I ended up with:

Quantity | Item Name             | Cost   | Total Cost | Link
---------|-----------------------|--------|------------|-----
50 | MX cherry clear switches    |  $1.24 |    $62.00  | [digikey](http://www.digikey.com/product-search/en?x=0&y=0&lang=en&site=us&keywords=mx+cherry+clear)
50 | Diodes 1n4148               |  $0.05 |     $2.50  | [digikey](http://www.digikey.com/product-detail/en/1N4148TA/1N4148TACT-ND/1532747)
1	 | Base blank DSA keycap set   | $25.00 |    $25.00  | [Pimp My Keyboard](http://keyshop.pimpmykeyboard.com/products/full-keysets/dsa-blank-sets-1)
1	 | Teensy 2	                   | $16.00	|    $16.00  | [teensy](https://www.pjrc.com/store/teensy.html)
1	 | Case materials	             | $20.00 |	   $20.00  | [ponoko](https://www.ponoko.com/)
1	 | USB micro cable             |  $6.99 |     $6.99  | [Amazon](http://www.amazon.com/Cable-Matters%C2%AE-Premium-Hi-Speed-Micro-B/dp/B00IG9LSGM/ref=sr_1_1?s=electronics&ie=UTF8&qid=1433097350&sr=1-1&keywords=usb-cable-micro)
1	 | MX Red or MX Black switches |  $8.50 |     $8.50  | [mechanicalkeyboards.com](http://mechanicalkeyboards.com/shop/index.php?l=product_detail&p=103)
1	 | additional 1.5x DSA keycap  |  $6.00 |     $6.00  | [Pimp My Keyboard](http://keyshop.pimpmykeyboard.com/products/blank-key-packs/dcs-1-5-space)

Total Cost		$148.48

### Case

The case was the trickiest piece of the whole thing. The kit that Phil provides includes a wooden case. However, since I was sourcing all of mine I needed to find a way to get it fabricated. After searching around for a while I found a solution in this [geekhack thread]() TODO FIND THIS URL.  It turns out that there is a great service called ponoko which will laser cut materials for you.  In the [atreus repo](https://github.com/technomancy/atreus) there is a `ready-for-ponoko.svg`.  You simply upload that file to ponoko and select your material.

The ponoko file is designed to work with 3.2mm 24x12 birch plywood. The great thing about this material is that its cheap, lightweight, easy to work with, and gives the keyboard a really unique look. A wooden keyboard case was appealing but in the end I decided to go with a 3mm clear acrylic. It was a bit more expensive then the wooden option partly because you have to use a larger sheet and it (obviously) doesn't hide any crimes you might commit with a soldering iron or hot glue gun. But I was really pleased with the end result.

Whatever material you end up getting make sure that its at least 3mm thick. You could theoretically get away with a thinner material but if you're going to be soldering together everything point to point the way that I did then you'll have to be precise.  Otherwise you may have trouble getting everything to fit inside the case.

### Switches

Keyboard switches are one of the great debates in the hacker world. I knew that I was going to be using this in an office setting so while my personal favorite switches are browns I picked up some mx cherry clears for the sake of my co-workers.

I cannot emphasize how much I regret this decision.  

Some people really like the stiffer feel of the clears. I am not one of those people. I personally find them so stiff that they start to hurt my hands after a while. If I was building a second keyboard (which I have now done) then I would definitely go with browns and just add o-rings. Switches are totally subjective so you should pick up a [keytester](http://www.amazon.com/Max-Keyboard-Keycap-Cherry-Sampler/dp/B00N6DXTW4) and try some out before you make a decision.

### Keycaps

There is some debate about the benefits of DCS vs DSA keycaps for the Atreus. Since I hadn't used one before I decided to go with a DSA set since those are what are recommended. The flatter profile of the DSA caps works very well with Atreus layout. I kept things simple (and cheap) and just picked up the a matte black set from pimpmykeyboard. If you're feeling creative then you should play around with other colors on  http://www.keyboard-layout-editor.com/#/.

### Microcontroller

You can either use the A* micro or the teensy. While the A* is more supported I decided to go with the teensy since I had experience with them.

## Assembly

The assembly instructions are well documented on the Atreus site. I thought that I would just include a few photos from my experience (taken with my potato phone).

All told it took me the better part of a day to get my keyboard finished up. I have a lot of experience soldering so if you're just getting started then you'll want to take your time and test your connections with a multimeter as you go.

Once you have everything put together you can start hacking on your new keyboard.

## Flashing firmware.

In order to keep things simple I initially loaded up Phil's firmware which has its own [repository]() TODO - GET THIS LINK. The toolchain is standard avr tools and the repos README walks you through how to get it all set up. After playing around with Phil's firmware for a bit I decided to try out tmk.

TMK is, from what I can tell, the community choice for customizing keyboard firmware. Its very feature complete and has support for several useful macros like click vs. hold keys, macros, and layers. Technomancy has a fork of the tmk library with the atreus included and it works well with a teensy. Uploading it works the same way that the original firmware does. I cut my own fork off of Technomancies version and included my custom layout if you want to check it out.

I ended up tweaking the original layout quite a bit changing the original symbol pad/num pad layout to be a more standard number row and symbol row layout. I also take advantage of several click or hold macros.

My suggestion is to keep things simple at first and start to add customizations as you go. Don't go overboard with it initially because you won't remember half of what you programmed in there anyway.

## After thoughts

I've been using my Atreus at the office for a few weeks now and I'm really enjoying it. The columnar layout is quite comfortable and I love being able to add customizations and macros to optimize my workflow.  If I could go back I would most certainly go with mx cherry browns over the clears. However, despite that I'm happy with how it turned out.
