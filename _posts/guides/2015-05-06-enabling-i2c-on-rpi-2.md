---
layout: post
title: "Enabling i2c on Raspberry Pi 2"
date:  2015-05-06
category: guides
tags: 
- guides
- raspberry pi
- arch linux
- embedded
---

## Introduction

I'm currently taking a course on my university in embedded systems. 

I needed an i2c interface, however, all guides for either Raspbian or Arch Linux
(I'm using Arch) failed. After extensive googleing (I found out that searching
for "Raspberry Pi 2" is hard) I finally found that [a recent update to Raspberry
Pi kernels and firmware] [rpi-update] changed how the Raspberry Pi manages it's
hardware. (Irritating, but it's for the greater good. :) )


## How to:

*Please note that the instructions are for __Arch Linux__. However, step __1__ should
be identical regardless of OS. For instructions on other OSes, read the
documentation. ;)*

1. Edit "/boot/config.txt", append the line `dtparam=i2c_arm=on`
2. Edit "/etc/modules-load.d/raspberrypi.conf", append the line `12c-dev`
3. Install *i2c-tools* and *lm_sensors* with Pacman
4. Reboot the Raspberry Pi.
5. List i2c interfaces with `ls /dev/i2c*`
6. The i2c interface can later be used with other tools, such as i2cdetect. The
   `ls` in step **5** will specify what bus number to be used with i2cdetect,
   typically 1 or 0

Refer to other guides on how to use i2c interface.

Have fun with i2c!

[rpi-update]: https://www.raspberrypi.org/documentation/configuration/device-tree.md