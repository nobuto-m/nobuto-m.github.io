---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "How to prevent TrackPoint, touchpad or keyboard events from waking up ThinkPad T14 Gen 5 AMD from suspend"
slug: how-to-prevent-trackpoint-events-from-waking-up-thinkpad-t14-gen-5-amd-from-suspend
subtitle: ""
summary: ""
authors: []

tags: [planet-ubuntu, "ThinkPad T14 Gen 5 AMD", "ThinkPad T14 Gen 3 AMD"]
categories: [Ubuntu]
keywords: []

reading_time: false
show_related: true
share: true

year: 2025
date: 2025-01-02T22:36:00+09:00
lastmod: 2025-01-02T22:36:00+09:00

featured: false
draft: true

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
image:
  preview_only: true
---

## TL;DR

Try the following lines in your custom udev rules, e.g. `/etc/udev/rules.d/99-local-disable-wakeup-events-from-input-dev.rules`.

```shell
KERNEL=="i2c-ELAN0676:00", SUBSYSTEM=="i2c", DRIVERS=="i2c_hid_acpi", ATTR{power/wakeup}="disabled"
KERNEL=="PNP0C0E:00", SUBSYSTEM=="acpi", DRIVERS=="button", ATTRS{path}=="\_SB_.SLPB", ATTR{power/wakeup}="disabled"
```

{{< toc >}}

## The motivation

{{< figure src="featured.jpg" caption="Whenever something touched this red cap, the system woke up from suspend." >}}

I've used ThinkPad T14 Gen 3 AMD for 2 years, and I recently purchased T14 **Gen 5** AMD. The previous system as Gen 3 annoyed me so much because the laptop randomly woke up from suspend even inside a backpack on its own, heated up the confined air in it, and drained the battery pretty fast as a consequense. Basically Gen 3 was too sensitive to any events. Whenever a USB Type-C cable is plugged in as a power source or whenever something touches the TrackPoint even if a display on a closed lid slightly make contact with the red cap, the system wakes up from suspend. It was uncontrollable.

I was hoping that Gen 5 would make a difference and it did when it comes to the power source event. However, frequent wakeup due to the trackpoint event remained the same so I started to dig in.


## Disabling touchpad as a wakeup source on T14 Gen 5 AMD

Disabling touchpad events as a wakeup source is straightforward. You should be able to find the touchpad device, `ELAN0676:00 04F3:3195 Touchpad`, in the udev device tree as follows.

```bash
$ udevadm info --tree
...

 └─input/input12
   ┆ P: /devices/platform/AMDI0010:01/i2c-1/i2c-ELAN0676:00/0018:04F3:3195.0001/input/input12
   ┆ M: input12
   ┆ R: 12
   ┆ U: input
   ┆ E: DEVPATH=/devices/platform/AMDI0010:01/i2c-1/i2c-ELAN0676:00/0018:04F3:3195.0001/input/input12
   ┆ E: SUBSYSTEM=input
   ┆ E: PRODUCT=18/4f3/3195/100
   ┆ E: NAME="ELAN0676:00 04F3:3195 Touchpad"
   ┆ E: PHYS="i2c-ELAN0676:00"
```

And you can get all attributes including the parent devices like the following.

```bash
$ udevadm info --attribute-walk -p /devices/platform/AMDI0010:01/i2c-1/i2c-ELAN0676:00/0018:04F3:3195.0001/input/input12
...

  looking at device '/devices/platform/AMDI0010:01/i2c-1/i2c-ELAN0676:00/0018:04F3:3195.0001/input/input12':
    KERNEL=="input12"
    SUBSYSTEM=="input"
    DRIVER==""
    ...
    ATTR{name}=="ELAN0676:00 04F3:3195 Touchpad"
    ATTR{phys}=="i2c-ELAN0676:00"

...

  looking at parent device '/devices/platform/AMDI0010:01/i2c-1/i2c-ELAN0676:00':
    KERNELS=="i2c-ELAN0676:00"
    SUBSYSTEMS=="i2c"
    DRIVERS=="i2c_hid_acpi"
    ATTRS{name}=="ELAN0676:00"
    ...
    ATTRS{power/wakeup}=="enabled"
```

The line we are looking for is `ATTRS{power/wakeup}`. And by using the identifiers of the parent device that has `ATTRS{power/wakeup}`, we can make sure that `/sys/devices/platform/AMDI0010:01/i2c-1/i2c-ELAN0676:00/power/wakeup` can be always `disabled` with the custom udev rule as follows.

```shell
KERNEL=="i2c-ELAN0676:00", SUBSYSTEM=="i2c", DRIVERS=="i2c_hid_acpi", ATTR{power/wakeup}="disabled"
```

## Disabling TrackPoint as a wakeup source on T14 Gen 5 AMD

We saw a pattern already with the touchpad above so we *should* be able to apply the same method. We see the TrackPoint device, `TPPS/2 Elan TrackPoint`, in the udev device tree.

```bash
$ udevadm info --tree
...

 └─input/input5
   ┆ P: /devices/platform/i8042/serio1/input/input5
   ┆ M: input5
   ┆ R: 5
   ┆ U: input
   ┆ E: DEVPATH=/devices/platform/i8042/serio1/input/input5
   ┆ E: SUBSYSTEM=input
   ┆ E: PRODUCT=11/2/a/63
   ┆ E: NAME="TPPS/2 Elan TrackPoint"
   ┆ E: PHYS="isa0060/serio1/input0"
```

And the information of parent devices too.

```bash
$ udevadm info --attribute-walk -p /devices/platform/i8042/serio1/input/input5
...

  looking at device '/devices/platform/i8042/serio1/input/input5':
    KERNEL=="input5"
    SUBSYSTEM=="input"
    DRIVER==""
    ...
    ATTR{name}=="TPPS/2 Elan TrackPoint"
    ATTR{phys}=="isa0060/serio1/input0"

...

  looking at parent device '/devices/platform/i8042/serio1':
    KERNELS=="serio1"
    SUBSYSTEMS=="serio"
    DRIVERS=="psmouse"
    ATTRS{bind_mode}=="auto"
    ATTRS{description}=="i8042 AUX port"
    ATTRS{drvctl}=="(not readable)"
    ATTRS{firmware_id}=="PNP: LEN0321 PNP0f13"
    ...
    ATTRS{power/wakeup}=="disabled"
```

I hit the wall here. `ATTRS{power/wakeup}=="disabled"` was already set but the TrackPoint still wakes up the system from suspend. And I had to do bisecting for all remaining wakeup sources.

{{< spoiler text="The full list of the remaining wakeup sources" >}}

```bash
$ cat /proc/acpi/wakeup
Device	S-state	  Status   Sysfs node
GPP0	  S0	*disabled
GPP2	  S3	*disabled
GPP5	  S0	*enabled   pci:0000:00:02.1
GPP6	  S4	*enabled   pci:0000:00:02.2
GP11	  S4	*enabled   pci:0000:00:03.1
SWUS	  S4	*disabled
GP12	  S4	*enabled   pci:0000:00:04.1
SWUS	  S4	*disabled
XHC0	  S3	*enabled   pci:0000:c4:00.3
XHC1	  S4	*enabled   pci:0000:c4:00.4
XHC2	  S4	*disabled  pci:0000:c6:00.0
NHI0	  S3	*enabled   pci:0000:c6:00.5
XHC3	  S3	*enabled   pci:0000:c6:00.3
NHI1	  S4	*enabled   pci:0000:c6:00.6
XHC4	  S3	*enabled   pci:0000:c6:00.4
LID	  S4	*enabled   platform:PNP0C0D:00
SLPB	  S3	*enabled   platform:PNP0C0E:00
```

```
 Wakeup sources:
 │  [/sys/devices/platform/USBC000:00/power_supply/ucsi-source-psy-USBC000:001/wakeup66]: enabled
 │  [/sys/devices/platform/USBC000:00/power_supply/ucsi-source-psy-USBC000:002/wakeup67]: enabled
 │ ACPI Battery [PNP0C0A:00]: enabled
 │ ACPI Lid Switch [PNP0C0D:00]: enabled
 │ ACPI Power Button [PNP0C0C:00]: enabled
 │ ACPI Sleep Button [PNP0C0E:00]: enabled
 │ AT Translated Set 2 keyboard [serio0]: enabled
 │ Advanced Micro Devices, Inc. [AMD] ISA bridge [0000:00:14.3]: enabled
 │ Advanced Micro Devices, Inc. [AMD] Multimedia controller [0000:c4:00.5]: enabled
 │ Advanced Micro Devices, Inc. [AMD] PCI bridge [0000:00:02.1]: enabled
 │ Advanced Micro Devices, Inc. [AMD] PCI bridge [0000:00:02.2]: enabled
 │ Advanced Micro Devices, Inc. [AMD] PCI bridge [0000:00:03.1]: enabled
 │ Advanced Micro Devices, Inc. [AMD] PCI bridge [0000:00:04.1]: enabled
 │ Advanced Micro Devices, Inc. [AMD] USB controller [0000:c4:00.3]: enabled
 │ Advanced Micro Devices, Inc. [AMD] USB controller [0000:c4:00.4]: enabled
 │ Advanced Micro Devices, Inc. [AMD] USB controller [0000:c6:00.3]: enabled
 │ Advanced Micro Devices, Inc. [AMD] USB controller [0000:c6:00.4]: enabled
 │ Advanced Micro Devices, Inc. [AMD] USB controller [0000:c6:00.5]: enabled
 │ Advanced Micro Devices, Inc. [AMD] USB controller [0000:c6:00.6]: enabled
 │ Mobile Broadband host interface [mhi0]: enabled
 │ Plug-n-play Real Time Clock [00:01]: enabled
 │ Real Time Clock alarm timer [rtc0]: enabled
 │ Thunderbolt domain [domain0]: enabled
 │ Thunderbolt domain [domain1]: enabled
 │ USB4 host controller [0-0]: enabled
 └─USB4 host controller [1-0]: enabled
```
{{< /spoiler >}}

And somehow, disabling SLPB(ACPI Sleep Button) stopped undesired wakeups by TrackPoint or keyboard events.

```bash
  looking at parent device '/devices/LNXSYSTM:00/LNXSYBUS:00/PNP0C0E:00':
    KERNELS=="PNP0C0E:00"
    SUBSYSTEMS=="acpi"
    DRIVERS=="button"
    ATTRS{hid}=="PNP0C0E"
    ATTRS{path}=="\_SB_.SLPB"
    ...
    ATTRS{power/wakeup}=="enabled"
```

The final udev rule is the following. It also disables wakeup events from the keyboard as a side effect, but the lid open event or pressing the power botton can wake up the system so it works for me.

```shell
KERNEL=="PNP0C0E:00", SUBSYSTEM=="acpi", DRIVERS=="button", ATTRS{path}=="\_SB_.SLPB", ATTR{power/wakeup}="disabled"
```

## ThinkPad T14 Gen 3 AMD case

```shell
KERNEL=="i2c-ELAN0678:00", SUBSYSTEM=="i2c", DRIVERS=="i2c_hid_acpi", ATTR{power/wakeup}="enabled"
```

[/etc/default/grub.d/local-ignore-interrupt.cfg]
```shell
GRUB_CMDLINE_LINUX_DEFAULT="$GRUB_CMDLINE_LINUX_DEFAULT gpiolib_acpi.ignore_wake=AMDI0030:00@0"
```

## S0i3/s2idle analysis script for AMD systems

https://gitlab.freedesktop.org/drm/amd/-/blob/master/scripts/amd_s2idle.py

