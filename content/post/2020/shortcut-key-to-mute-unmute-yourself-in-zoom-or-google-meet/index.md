---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Shortcut key to mute/unmute yourself in Zoom or Google Meet"
slug: shortcut-key-to-mute-unmute-yourself-in-zoom-or-google-meet
subtitle: ""
summary: ""
authors: []

tags: [planet-ubuntu]
categories: [Ubuntu]

show_related: true

year: 2020
date: 2020-12-31T03:49:53+09:00
lastmod: 2020-12-31T03:49:53+09:00

featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  # change it to true if it's actually used inside the post
  preview_only: true

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---

{{< figure src="featured.png" title="A custom and global shortcut key to mute / unmute yourself in Zoom or Google Meet" >}}

Just like everyone else, 2020 was the year of having more and more video-conference calls. How many times did we struggle to find the meeting window during a call, and say "Sorry, I was on mute"? I tried to address the pain and ended up with the following setup.

## xdotool

`xdotool` is a great automation tool for X, and it can search a window, activate it, and simulate keyboard input. That's a perfect match for the use case here.

Here is an example command for Google Meet.

```bash
$ xdotool search --name '^Meet - .+ - Chromium$' \
    windowactivate --sync \
    key ctrl+d
```

In the chained commands, it does:
1. search windows named like `Meet - <MEETING_ID> - Chromium`
1. activate the first window passed by the previous line and wait until it gets active (`--sync`)
1. send a keystroke as `Ctrl+D`, which is the default shortcut in Meet

By the way, my main browser is Firefox, but I have to use Chromium to join Meet calls since it tends to have less CPU utilization.

You can do something similar for Zoom with `Alt+A`.

```bash
$ xdotool search --name '^Zoom Meeting$' \
    windowactivate --sync \
    key alt+a
```

Microsoft Teams should work with xdotool and `Ctrl+Shift+M` at least for the web version.


## GNOME keyboard shortcuts

The commands above can be mapped to a shortcut key with GNOME.

{{< figure src="gnome-control-center-keyboard.png" >}}

It's pretty simple, but some tricks may be required. As far as I see, `gsd-media-keys` will invoke a command when a shortcut key is pressed, not released. In my case, I use `Ctrl+space` as the shortcut key, so Meet may recognize keys pressed as `Ctrl+space` + `Ctrl+D` = `Ctrl+space+D` which doesn't trigger the mute/unmute behavior actually. Keys can be canceled with `keyup`, so the key command was turned into `keyup space key ctrl+d` in the end.

Also, I wanted to use the same shortcut key for multiple services, and I have the following line which tries Google Meet first, then Zoom if no Meet window is found. It should work most of the cases unless you join multiple meetings at the same time.

```bash
sh -c "
    xdotool search --name '^Meet - .+ - Chromium$'
        windowactivate --sync
        keyup space key ctrl+d
    || xdotool search --name '^Zoom Meeting$'
        windowactivate --sync
        keyup ctrl+space key alt+a
"
```

`--clearmodifiers` can be used to simplify the whole commands, but when I tried, it sometimes left the Ctrl key pressed depending on the timing I released the key.


## Hardware mute/unmute button

Going further, I wanted to have a dedicated button to mute/unmute myself especially for some relaxed meetings where I don't have to keep my hands on the keyboard all the time.

Back in October, I bought a USB volume controller, which is recognized as "STMicroelectronics USB Volume Control" from the OS. It was around 15 USD.

{{< figure src="usb-volume-controller.jpg" >}}

It emits expected events as `KEY_VOLUMEUP` and `KEY_VOLUMEDOWN` with the dial, and `KEY_MUTE` when the knob is pressed.

I created a "[hwdb](https://www.freedesktop.org/software/systemd/man/hwdb.html)" file to remap the mute key to something else as follows in `/etc/udev/hwdb.d/99-local-remap-usb-volume-control.hwdb`.

```
# STMicroelectronics USB Volume Control
# Remap the click (Mute) to XF86Launch
evdev:input:b0003v0483p572D*
 KEYBOARD_KEY_c00e2=prog4
```

Once the hardware database is updated with `systemd-hwdb update` and the device is unplugged and plugged again (if without udevadm commands), I was able to map `Launch4`(prog4) to the xdotool commands in GNOME successfully.

It looks like everyone had the same idea. There are fancier buttons out there :-)

{{< tweet 1341866233600217088 >}}
