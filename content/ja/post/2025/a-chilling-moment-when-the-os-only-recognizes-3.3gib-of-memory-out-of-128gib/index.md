---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "128GiBメモリにアップグレードしたのにOSから3.3GiBしか認識されなくて肝が冷えた話"
slug: a-chilling-moment-when-the-os-only-recognizes-3gib-of-memory-out-of-128gib
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
date: 2025-06-15T16:26:36+09:00
lastmod: 2025-06-15T16:26:36+09:00

featured: false
draft: true

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
image:
  preview_only: true
---

**結論: BIOSのアップデートで解決した**

{{< figure src="featured.jpg" caption="BIOSでは最初から4x 32GiBの合計128GiB認識されていたのに" >}}

昨今のノートPCは、8コア/16スレッドのCPUと32GiBのメモリであれば標準から少しアップグレードするだけでそこまで無理しなくても手に入るぐらい。対して自分が2018年から使っている検証用据え置きPCは8コア/16スレッドと64GiBメモリの組み合わせのままで物足りなさがあった。全体を買い替えるか悩んだ上でひとまずはメモリを増量して延命することにした。

今までは、DDR4の4スロットを16+16+16+16GiBの合計64GiBだったが、これを最終的には32+32+32+32GiBの合計128GiBにする計画。CPU([AMD Ryzen 7 2700](https://www.amd.com/ja/support/downloads/drivers.html/processors/ryzen/ryzen-2000-series/amd-ryzen-7-2700.html#amd_support_product_spec))と、マザーボード([B450M Pro4](https://www.asrock.com/mb/AMD/B450M%20Pro4/index.jp.asp))の部分では128GiB対応をうたっているので、まずは32GiBモジュールを2つ購入して、96GiB(32+32+16+16GiB)の構成へ。ここはまったく問題なく通過したので、安心して残りの2モジュールを注文。次の日到着してウキウキで128GiB(32+32+32+32GiB)の構成にしたら、BIOSからは128GiB認識されているのに、OSからは3.3GiBしか利用できないトラブルが発生。

