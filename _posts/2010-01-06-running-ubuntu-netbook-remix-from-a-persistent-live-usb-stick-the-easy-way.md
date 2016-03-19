---
layout: post
title: Running Ubuntu Netbook Remix from a persistent live-USB stick the easy way
created: 1262790372
categories:
- !ruby/string:Sequel::SQL::Blob linux
- !ruby/string:Sequel::SQL::Blob ubuntu
---
I run Ubuntu Netbook Remix 9.10 on my Acer Aspire One netbook. A couple of days ago my SSD drive died and until the replacement drive arrives I would like to continue using the netbook, running the operating system directly from a USB stick.

I always use <a href="http://unetbootin.sourceforge.net/">UNetbootin</a> to put downloaded ISO images from various distros on a bootable USB drive. So I installed the <a href="http://www.ubuntu.com/getubuntu/download-netbook">UNR 9.10 ISO</a> on my stick with UNetbootin and booted it up. Everything seemed to work fine, but software updates and files in my /home folder were gone after rebooting. These were stored temporarily in memory rather than on the USB drive itself. I figured it would be easy enough to change this by creating a few extra directories and changing mount points. But it turned out not to be so easy after all.

Fortunately I came across a post mentioning that this could be done automatically by using a little program from Canonical called <a href="https://launchpad.net/usb-creator">usb-creator</a>. With this tool you can choose to make a persistent USB image by simply selecting an option. Pick your ISO image and USB stick from drop down lists and it sets up everything fully automatic. Great! The downside is that it only works with Ubuntu and derivatives.

But still it is a remarkable program. Thanks to it I can continue working on the netbook until the new SSD arrives, and even run my OS on whatever computer I come across, with all my settings and documents right there.

references:
https://bugs.launchpad.net/netbook-remix/+bug/372252
http://en.wikipedia.org/wiki/Ubuntu_Live_USB_creator
