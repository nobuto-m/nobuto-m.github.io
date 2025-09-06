---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "プレゼンテーション用リモコン Technologic レーザーポインター NEOを買った"
slug: purchased-presentation-remote-technologic-laserpointer-neo
subtitle: ""
summary: ""
authors: []

tags: []
categories: []
keywords: []

reading_time: false
show_related: true
share: false

year: 2025
date: 2025-09-06T18:29:11+09:00
lastmod: 2025-09-06T18:29:11+09:00

featured: false
draft: true

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
image:
  preview_only: true
---

{{< figure src="featured.png" caption="" >}}



https://technologic.jp/laserpointer_neo

https://www.amazon.co.jp/dp/B0CZHRM842



```dmesg
Sep 06 18:27:29 t14g5a kernel: usb 1-1: new full-speed USB device number 6 using xhci_hcd
Sep 06 18:27:29 t14g5a kernel: usb 1-1: New USB device found, idVendor=2312, idProduct=863d, bcdDevice= 0.02
Sep 06 18:27:29 t14g5a kernel: usb 1-1: New USB device strings: Mfr=1, Product=2, SerialNumber=3
Sep 06 18:27:29 t14g5a kernel: usb 1-1: Product: Wireless Device
Sep 06 18:27:29 t14g5a kernel: usb 1-1: Manufacturer: Smart
Sep 06 18:27:29 t14g5a kernel: usb 1-1: SerialNumber: b120300001
Sep 06 18:27:29 t14g5a kernel: input: Smart Wireless Device as /devices/pci0000:00/0000:00:08.1/0000:c4:00.3/usb1/1-1/1-1:1.0/0003:2312:863D.0005/input/input23
Sep 06 18:27:29 t14g5a kernel: hid-generic 0003:2312:863D.0005: input,hidraw1: USB HID v2.00 Keyboard [Smart Wireless Device] on usb-0000:c4:00.3-1/input0
Sep 06 18:27:29 t14g5a kernel: input: Smart Wireless Device Mouse as /devices/pci0000:00/0000:00:08.1/0000:c4:00.3/usb1/1-1/1-1:1.1/0003:2312:863D.0006/input/input24
Sep 06 18:27:29 t14g5a kernel: input: Smart Wireless Device System Control as /devices/pci0000:00/0000:00:08.1/0000:c4:00.3/usb1/1-1/1-1:1.1/0003:2312:863D.0006/input/input25
Sep 06 18:27:29 t14g5a kernel: input: Smart Wireless Device Consumer Control as /devices/pci0000:00/0000:00:08.1/0000:c4:00.3/usb1/1-1/1-1:1.1/0003:2312:863D.0006/input/input26
Sep 06 18:27:29 t14g5a kernel: hid-generic 0003:2312:863D.0006: input,hiddev0,hidraw2: USB HID v2.00 Mouse [Smart Wireless Device] on usb-0000:c4:00.3-1/input1
```

evemu-tools

```bash
$ sudo evemu-record 
Available devices:
/dev/input/event0:	Power Button
/dev/input/event1:	Lid Switch
/dev/input/event2:	Sleep Button
/dev/input/event3:	AT Translated Set 2 keyboard
/dev/input/event4:	Video Bus
/dev/input/event5:	ELAN0676:00 04F3:3195 Mouse
/dev/input/event6:	ELAN0676:00 04F3:3195 Touchpad
/dev/input/event7:	TPPS/2 Elan TrackPoint
/dev/input/event8:	Smart Wireless Device
/dev/input/event9:	ThinkPad Extra Buttons
/dev/input/event10:	HD-Audio Generic HDMI/DP,pcm=3
/dev/input/event11:	HD-Audio Generic HDMI/DP,pcm=7
/dev/input/event12:	HD-Audio Generic HDMI/DP,pcm=8
/dev/input/event13:	HD-Audio Generic Mic
/dev/input/event14:	HD-Audio Generic Headphone
/dev/input/event15:	py-evdev-uinput
/dev/input/event16:	Smart Wireless Device Mouse
/dev/input/event17:	Smart Wireless Device System Control
/dev/input/event18:	Smart Wireless Device Consumer Control
Select the device event number [0-18]: 8
```

[Square]

```
E: 20.334155 0004 0004 458795	# EV_MSC / MSC_SCAN             458795
E: 20.334155 0001 000f 0001	# EV_KEY / KEY_TAB              1
E: 20.334155 0000 0000 0000	# ------------ SYN_REPORT (0) ---------- +5535ms
	E: 20.356105 0004 0004 458795	# EV_MSC / MSC_SCAN             458795
E: 20.356105 0001 000f 0000	# EV_KEY / KEY_TAB              0
E: 20.356105 0000 0000 0000	# ------------ SYN_REPORT (0) ---------- +22ms
```

Long press
```
E: 191.874521 0004 0004 458978	# EV_MSC / MSC_SCAN             458978
E: 191.874521 0001 0038 0001	# EV_KEY / KEY_LEFTALT          1
E: 191.874521 0004 0004 458795	# EV_MSC / MSC_SCAN             458795
E: 191.874521 0001 000f 0001	# EV_KEY / KEY_TAB              1
E: 191.874521 0000 0000 0000	# ------------ SYN_REPORT (0) ---------- +3562ms
E: 192.131171 0001 000f 0002	# EV_KEY / KEY_TAB              2
E: 192.131171 0000 0000 0001	# ------------ SYN_REPORT (1) ---------- +257ms
E: 192.165171 0001 000f 0002	# EV_KEY / KEY_TAB              2
E: 192.165171 0000 0000 0001	# ------------ SYN_REPORT (1) ---------- +34ms
E: 192.199205 0001 000f 0002	# EV_KEY / KEY_TAB              2
E: 192.199205 0000 0000 0001	# ------------ SYN_REPORT (1) ---------- +34ms
E: 192.233183 0001 000f 0002	# EV_KEY / KEY_TAB              2
E: 192.233183 0000 0000 0001	# ------------ SYN_REPORT (1) ---------- +34ms
E: 192.249801 0004 0004 458795	# EV_MSC / MSC_SCAN             458795
E: 192.249801 0001 000f 0000	# EV_KEY / KEY_TAB              0
E: 192.249801 0000 0000 0000	# ------------ SYN_REPORT (0) ---------- +16ms
E: 192.639844 0004 0004 458795	# EV_MSC / MSC_SCAN             458795
E: 192.639844 0001 000f 0001	# EV_KEY / KEY_TAB              1
E: 192.639844 0000 0000 0000	# ------------ SYN_REPORT (0) ---------- +390ms
E: 192.891171 0001 000f 0002	# EV_KEY / KEY_TAB              2
E: 192.891171 0000 0000 0001	# ------------ SYN_REPORT (1) ---------- +252ms
E: 192.925192 0001 000f 0002	# EV_KEY / KEY_TAB              2
E: 192.925192 0000 0000 0001	# ------------ SYN_REPORT (1) ---------- +34ms
E: 192.959204 0001 000f 0002	# EV_KEY / KEY_TAB              2
E: 192.959204 0000 0000 0001	# ------------ SYN_REPORT (1) ---------- +34ms
E: 192.993204 0001 000f 0002	# EV_KEY / KEY_TAB              2
E: 192.993204 0000 0000 0001	# ------------ SYN_REPORT (1) ---------- +34ms
E: 193.027204 0001 000f 0002	# EV_KEY / KEY_TAB              2
E: 193.027204 0000 0000 0001	# ------------ SYN_REPORT (1) ---------- +34ms
E: 193.038020 0004 0004 458795	# EV_MSC / MSC_SCAN             458795
E: 193.038020 0001 000f 0000	# EV_KEY / KEY_TAB              0
E: 193.038020 0000 0000 0000	# ------------ SYN_REPORT (0) ---------- +11ms
E: 193.235084 0004 0004 458978	# EV_MSC / MSC_SCAN             458978
E: 193.235084 0001 0038 0000	# EV_KEY / KEY_LEFTALT          0
E: 193.235084 0000 0000 0000	# ------------ SYN_REPORT (0) ---------- +197ms
```

Double click
```
E: 13.739397 0004 0004 458792	# EV_MSC / MSC_SCAN             458792
E: 13.739397 0001 001c 0001	# EV_KEY / KEY_ENTER            1
E: 13.739397 0000 0000 0000	# ------------ SYN_REPORT (0) ---------- +4240ms

E: 13.761160 0004 0004 458792	# EV_MSC / MSC_SCAN             458792
E: 13.761160 0001 001c 0000	# EV_KEY / KEY_ENTER            0
E: 13.761160 0000 0000 0000	# ------------ SYN_REPORT (0) ---------- +22ms
```


[Up]

```
E: 0.000001 0004 0004 458827	# EV_MSC / MSC_SCAN             458827
E: 0.000001 0001 0068 0001	# EV_KEY / KEY_PAGEUP           1
E: 0.000001 0000 0000 0000	# ------------ SYN_REPORT (0) ---------- +0ms
^[[5~E: 0.021937 0004 0004 458827	# EV_MSC / MSC_SCAN             458827
E: 0.021937 0001 0068 0000	# EV_KEY / KEY_PAGEUP           0
E: 0.021937 0000 0000 0000	# ------------ SYN_REPORT (0) ---------- +21ms
```
Long press
```
E: 88.008640 0004 0004 458793	# EV_MSC / MSC_SCAN             458793
E: 88.008640 0001 0001 0001	# EV_KEY / KEY_ESC              1
E: 88.008640 0000 0000 0000	# ------------ SYN_REPORT (0) ---------- +7316ms
^[E: 88.030309 0004 0004 458793	# EV_MSC / MSC_SCAN             458793
E: 88.030309 0001 0001 0000	# EV_KEY / KEY_ESC              0
E: 88.030309 0000 0000 0000	# ------------ SYN_REPORT (0) ---------- +22ms
```

```
E: 88.030309 0000 0000 0000	# ------------ SYN_REPORT (0) ---------- +22ms
E: 136.519800 0004 0004 458977	# EV_MSC / MSC_SCAN             458977
E: 136.519800 0001 002a 0001	# EV_KEY / KEY_LEFTSHIFT        1
E: 136.519800 0004 0004 458814	# EV_MSC / MSC_SCAN             458814
E: 136.519800 0001 003f 0001	# EV_KEY / KEY_F5               1
E: 136.519800 0000 0000 0000	# ------------ SYN_REPORT (0) ---------- +48489ms
^[[15;2~E: 136.544788 0004 0004 458977	# EV_MSC / MSC_SCAN             458977
E: 136.544788 0001 002a 0000	# EV_KEY / KEY_LEFTSHIFT        0
E: 136.544788 0004 0004 458814	# EV_MSC / MSC_SCAN             458814
E: 136.544788 0001 003f 0000	# EV_KEY / KEY_F5               0
E: 136.544788 0000 0000 0000	# ------------ SYN_REPORT (0) ---------- +25ms
```

[Down]

```
E: 181.142345 0004 0004 458830	# EV_MSC / MSC_SCAN             458830
E: 181.142345 0001 006d 0001	# EV_KEY / KEY_PAGEDOWN         1
E: 181.142345 0000 0000 0000	# ------------ SYN_REPORT (0) ---------- +2053ms
^[[6~E: 181.164296 0004 0004 458830	# EV_MSC / MSC_SCAN             458830
E: 181.164296 0001 006d 0000	# EV_KEY / KEY_PAGEDOWN         0
E: 181.164296 0000 0000 0000	# ------------ SYN_REPORT (0) ---------- +22ms
```

Long press
```
E: 206.526857 0004 0004 458757	# EV_MSC / MSC_SCAN             458757
E: 206.526857 0001 0030 0001	# EV_KEY / KEY_B                1
E: 206.526857 0000 0000 0000	# ------------ SYN_REPORT (0) ---------- +25362ms
bE: 206.548827 0004 0004 458757	# EV_MSC / MSC_SCAN             458757
E: 206.548827 0001 0030 0000	# EV_KEY / KEY_B                0
E: 206.548827 0000 0000 0000	# ------------ SYN_REPORT (0) ---------- +22ms
```


- Square - Tab, Hyperlink(?)
- Square (long press) - Alt + Tab, Window switch
- Square (double click) - Enter, None
- Up - PageUp, Back
- Up (long press) - Esc / Shift + F5, Slideshow or Esc
- Down - PageDown, Next
- Down (long press) - B
