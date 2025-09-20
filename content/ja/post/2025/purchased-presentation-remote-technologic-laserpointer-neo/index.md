---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "プレゼンテーション用リモコン TechnologicレーザーポインターNEOを買った"
slug: purchased-presentation-remote-technologic-laserpointer-neo
subtitle: ""
summary: ""
authors: []

tags: []
categories: []
keywords: ["Ubuntu"]

reading_time: false
show_related: true
share: false

year: 2025
date: 2025-09-06T18:29:11+09:00
lastmod: 2025-09-06T18:29:11+09:00

featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
image:
  preview_only: true
---

購入価格2,533円。一番安そうな価格帯で技適シールがありそうなものとして選んだ。バッテリー充電用にUSB Type-Cポートがあるが、付属のUSB-A-to-Cケーブルでないと充電できない。

- 公式サイト: https://technologic.jp/laserpointer_neo
- Amazonリンク: https://www.amazon.co.jp/dp/B0CZHRM842

USBドングルを挿したときの出力は以下の通り。

```
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

技適マークがあるのと、ただのUSB入力デバイスとして動くのでOS側でキーの入れ替えとかもできて遊べそう。キーの割り当ては下記の表の通りで、それなりの数のイベントを出せるのでこの表がないと覚えられない。

| 本体キー | 説明書記載の動作 | 実際のキーイベント |
| --- | --- | --- |
| 四角ボタン | ハイパーリンク | `Tab` |
| 四角ボタン長押し | ウィンドウ切り替え | `Alt+Tab` |
| 四角ボタンダブルクリック | \[記載なし\] | `Enter` |
| 上ボタン | ページ戻し | `PageUp` |
| 上ボタン長押し | スライドショーまたはEsc | `Shift+F5`と`Esc`のトグル |
| 下ボタン | ページ送り | `PageDown` |
| 下ボタン長押し | \[記載なし\] | `B` |
