---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Experience with t gen amd on ubuntu linux"
slug: experience-with-t-gen-amd-on-ubuntu-linux
subtitle: ""
summary: ""
authors: []

tags: []
categories: []
keywords: []

reading_time: false
show_related: true

year: 2024
date: 2024-04-11T11:25:38+09:00
lastmod: 2024-04-11T11:25:38+09:00

featured: false
draft: true

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
image:
  preview_only: true
---

{{< figure src="featured.png" caption="" >}}

## Suspend

Suspend got broken in the middle of Ubuntu's v5.19 kernel lifecycle, and I had to opt-in for 6.1.
https://bugs.launchpad.net/ubuntu/+source/linux-hwe-5.19/+bug/2007718/comments/12
And it got broken again between v6.6 and v6.8
https://bugs.launchpad.net/ubuntu/+source/linux/+bug/2056387
It's not fixed yet so I went back to v6.6.

## Frequent wakeup

The laptop wakes up from suspend after any event, e.g. connecting a power cable, accidentally touching keyboard, touchpad, or trackpoint, etc. The only event which can be disabled is touchpad[1] but the laptop still wakes up frequently inside my backpack because of some vibrations, and it gets overheated on its own and kills itself due to no air intake inside the backpack obviously. I had to set the idle timeout for suspsend to 10 min so even if it frequently wakes up it gets suspended after 10 min until it wakes up again.

[1] https://wiki.archlinux.org/title/Lenovo_ThinkPad_T14_(AMD)_Gen_3#Disable_wakeup_from_sleep_on_touchpad_activity

## AMD's struggle

AMD is behind Intel when it comes to power management / laptop functions on Linux.

I don't feel AMD P-State EPP[2] is mature enough (well, it may be a firmware issue since the idea of EPP is offloading the power management from the OS) as I have a heating/cooling issue after switching to EPP (it's hard to explain but I think my trackpoint heavy drift issue is coming from a conservative cooling). And I see a random packet loss issue using WiFi (maybe due to missing WBRF for my model[3]) so I'm monitoring WiFi stat continuously and resetting the connection whenever I start seeing missing frames.

[2] https://www.phoronix.com/news/Linux-6.5-Power-Management
[3] https://www.phoronix.com/news/AMD-WBRF-For-Linux-6.8

## Firmware

There are quarterly-ish firmware updates, which is good. However, they retract firmware from time to time without mentioning what was a problem. For example I'm running 1.49, which disappeared from Lenovo's website and LVFS[4] without knowing why.

The initial set of firmware for the model was so terrible that I lost the trackpoint whenever I closed the lid[5] so it's a good thing to release new firmware actively though.

[4] https://fwupd.org/lvfs/devices/com.lenovo.ThinkPadR23ET.firmware
[5] https://www.reddit.com/r/thinkpad/comments/wju7d9/t14s_gen3_amd_trackpoint_stops_working_after/


## Battery

Draining even when the system status and the LED indicates it's charging.
