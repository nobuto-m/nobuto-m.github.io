---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "How to prevent TrackPoint or touchpad events from waking up ThinkPad T14 Gen 5 AMD from suspend"
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
date: 2025-01-09T23:50:38+09:00
lastmod: 2025-01-09T23:50:38+09:00

featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
image:
  preview_only: true
---

## TL;DR

Try the following lines in your custom udev rules, e.g.  
`/etc/udev/rules.d/99-local-disable-wakeup-events.rules`

```shell
KERNEL=="i2c-ELAN0676:00", SUBSYSTEM=="i2c", DRIVERS=="i2c_hid_acpi", ATTR{power/wakeup}="disabled"
KERNEL=="PNP0C0E:00", SUBSYSTEM=="acpi", DRIVERS=="button", ATTRS{path}=="\_SB_.SLPB", ATTR{power/wakeup}="disabled"
```

{{< toc >}}

## The motivation

{{< figure src="featured.jpg" caption="Whenever something touches the red cap, the system wakes up from suspend/s2idle." >}}

I've used ThinkPad T14 Gen 3 AMD for 2 years, and I recently purchased T14 **Gen 5** AMD. The previous system as Gen 3 annoyed me so much because the laptop randomly woke up from suspend even inside a backpack on its own, heated up the confined air in it, and drained the battery pretty fast as a consequence. Basically it's too sensitive to any events. For example, whenever a USB Type-C cable is plugged in as a power source or whenever something touches the TrackPoint **even if a display on a closed lid slightly makes contact with the red cap, the system wakes up from suspend**. It was uncontrollable.

I was hoping that Gen 5 would make a difference, and it did when it comes to the power source event. However, frequent wakeups due to the TrackPoint event remained the same so I started to dig in.


## Disabling touchpad as a wakeup source on T14 Gen 5 AMD

Disabling touchpad events as a wakeup source is straightforward. The touchpad device, `ELAN0676:00 04F3:3195 Touchpad`, can be found in the udev device tree as follows.

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

And you can get all attributes including parent devices like the following.

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

The line I'm looking for is `ATTRS{power/wakeup}=="enabled"`. By using the identifiers of the parent device that has `ATTRS{power/wakeup}`, I can make sure that `/sys/devices/platform/AMDI0010:01/i2c-1/i2c-ELAN0676:00/power/wakeup` is always `disabled` with the custom udev rule as follows.

```shell
KERNEL=="i2c-ELAN0676:00", SUBSYSTEM=="i2c", DRIVERS=="i2c_hid_acpi", ATTR{power/wakeup}="disabled"
```

## Disabling TrackPoint as a wakeup source on T14 Gen 5 AMD

I've seen a pattern already as above so I *should* be able to apply the same method. The TrackPoint device, `TPPS/2 Elan TrackPoint`, can be found in the udev device tree.

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

I hit the wall here. `ATTRS{power/wakeup}=="disabled"` for the `i8042 AUX port` is already there but the TrackPoint still wakes up the system from suspend. I had to do bisecting for all remaining wakeup sources.

{{< spoiler text="The list of the remaining wakeup sources" >}}

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

Somehow, disabling `SLPB` "ACPI Sleep Button" stopped undesired wakeups by the TrackPoint.

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

The final udev rule is the following. It also disables wakeup events from the keyboard as a side effect, but opening the lid or pressing the power button can still wake up the system so it works for me.

```shell
KERNEL=="PNP0C0E:00", SUBSYSTEM=="acpi", DRIVERS=="button", ATTRS{path}=="\_SB_.SLPB", ATTR{power/wakeup}="disabled"
```

## In the case of ThinkPad T14 Gen 3 AMD

After solving the headache of frequent wakeups for T14 Gen5 AMD. I was curious if I could apply the same to Gen 3 AMD retrospectively. Gen 3 has the following wakeup sources active out of the box.

```
 Wakeup sources:
 │ ACPI Battery [PNP0C0A:00]: enabled
 │ ACPI Lid Switch [PNP0C0D:00]: enabled
 │ ACPI Power Button [LNXPWRBN:00]: enabled
 │ ACPI Power Button [PNP0C0C:00]: enabled
 │ ACPI Sleep Button [PNP0C0E:00]: enabled
 │ AT Translated Set 2 keyboard [serio0]: enabled
 │ Advanced Micro Devices, Inc. [AMD] ISA bridge [0000:00:14.3]: enabled
 │ Advanced Micro Devices, Inc. [AMD] PCI bridge [0000:00:02.1]: enabled
 │ Advanced Micro Devices, Inc. [AMD] PCI bridge [0000:00:02.2]: enabled
 │ Advanced Micro Devices, Inc. [AMD] USB controller [0000:04:00.3]: enabled
 │ Advanced Micro Devices, Inc. [AMD] USB controller [0000:04:00.4]: enabled
 │ Advanced Micro Devices, Inc. [AMD] USB controller [0000:05:00.0]: enabled
 │ Advanced Micro Devices, Inc. [AMD] USB controller [0000:05:00.3]: enabled
 │ Advanced Micro Devices, Inc. [AMD] USB controller [0000:05:00.4]: enabled
 │ ELAN0678:00 04F3:3195 Mouse [i2c-ELAN0678:00]: enabled
 │ Mobile Broadband host interface [mhi0]: enabled
 │ Plug-n-play Real Time Clock [00:01]: enabled
 └─Real Time Clock alarm timer [rtc0]: enabled
```

Disabling the touchpad event was straightforward. The only difference from Gen 5 was the ID of the device.

```shell
KERNEL=="i2c-ELAN0678:00", SUBSYSTEM=="i2c", DRIVERS=="i2c_hid_acpi", ATTR{power/wakeup}="disabled"
```

When it comes to the TrackPoint or power source event, nothing was able to stop it from waking up the system even after disabling all wakeup sources. I came across a hidden gem named `amd_s2idle.py`. The ["S0i3/s2idle analysis script for AMD systems"](https://gitlab.freedesktop.org/drm/amd/-/blob/master/scripts/amd_s2idle.py) is full with the domain knowledge of s2idle like where to look in `/proc` or `/sys` or how to enable debug and what part of the logs is important.

By running the script, I got the following output around the unexpected wakeup.

```
$ sudo python3 ./amd_s2idle.py --debug-ec --duration 30
Debugging script for s2idle on AMD systems
💻 LENOVO 21CF21CFT1 (ThinkPad T14 Gen 3) running BIOS 1.56 (R23ET80W (1.56 )) released 10/28/2024 and EC 1.32
🐧 Ubuntu 24.04.1 LTS
🐧 Kernel 6.11.0-12-generic
🔋 Battery BAT0 (Sunwoda ) is operating at 90.91% of design
Checking prerequisites for s2idle
✅ Logs are provided via systemd
✅ AMD Ryzen 7 PRO 6850U with Radeon Graphics (family 19 model 44)
...

Suspending system in 0:00:02
Suspending system in 0:00:01

Started at 2025-01-04 00:46:53.063495 (cycle finish expected @ 2025-01-04 00:47:27.063532)
Collecting data in 0:00:02
Collecting data in 0:00:01

Results from last s2idle cycle
💤 Suspend count: 1
💤 Hardware sleep cycle count: 1
○ GPIOs active: ['0']
🥱 Wakeup triggered from IRQ 9: ACPI SCI
🥱 Wakeup triggered from IRQ 7: GPIO Controller
🥱 Woke up from IRQ 7: GPIO Controller
❌ Userspace suspended for 0:00:14.031448 (< minimum expected 0:00:27)
💤 In a hardware sleep state for 0:00:10.566894 (75.31%)
🔋 Battery BAT0 lost 10000 µWh (0.02%) [Average rate 2.57W]
Explanations for your system
🚦 Userspace wasn't asleep at least 0:00:30
        The system was programmed to sleep for 0:00:30, but woke up prematurely.
        This typically happens when the system was woken up from a non-timer based source.

        If you didn't intentionally wake it up, then there may be a kernel or firmware bug
```

I compared all the logs generated between the events of power button, power source, TrackPoint, and touchpad. But except for the touchpad event, everything else was coming from GPIO pin #0 and there was no more information of how to distinguish those wakeup triggers. I ended up with a drastic approach of ignoring wakeup triggers from the GPIO pin #0 completely with the following kernel option.

```shell
gpiolib_acpi.ignore_wake=AMDI0030:00@0
```

And I get the line on each boot.

```bash
kernel: amd_gpio AMDI0030:00: Ignoring wakeup on pin 0
```

That comes with obvious downsides. The system doesn't wake up frequently any longer, that is good. However, nothing can wake it up after getting into suspend. Opening the lid, pressing the power button or any key is simply ignored since all are going to GPIO pin #0. In the end, I had to enable the touchpad back as a wakeup source explicitly so the system can wakeup by tapping the touchpad. It's far from ideal, but the touchpad is less sensitive than the TrackPoint so I will keep it that way.

```shell
KERNEL=="i2c-ELAN0678:00", SUBSYSTEM=="i2c", DRIVERS=="i2c_hid_acpi", ATTR{power/wakeup}="enabled"
```

I guess the limitation is coming from a firmware more or less, but at the same time I don't expect fixes for the few year old model.


## References

- [scripts/amd_s2idle.py · master · drm / amd · GitLab](https://gitlab.freedesktop.org/drm/amd/-/blob/master/scripts/amd_s2idle.py)
- [Cannot disable wakeup sources on Yellow Carp / Rembrandt 6850U w/ kernels 6.0 and 6.1 (#2405) · Issues · drm / amd · GitLab](https://gitlab.freedesktop.org/drm/amd/-/issues/2405)
- [ignore_wake, ignore_interrupt linux/drivers/gpio/gpiolib-acpi.c at v6.11 · torvalds/linux](https://github.com/torvalds/linux/blob/v6.11/drivers/gpio/gpiolib-acpi.c#L31-L41)
- [gpiolib_acpi_quirks linux/drivers/gpio/gpiolib-acpi.c at v6.11 · torvalds/linux](https://github.com/torvalds/linux/blob/v6.11/drivers/gpio/gpiolib-acpi.c#L1518-L1700)
- [Lenovo ThinkPad T14 (AMD) Gen 3 - ArchWiki](https://wiki.archlinux.org/title/Lenovo_ThinkPad_T14_(AMD)_Gen_3)
- [Introduce concept of suspend/resume with dark screen on wakeup · Issue #27077 · systemd/systemd](https://github.com/systemd/systemd/issues/27077)
