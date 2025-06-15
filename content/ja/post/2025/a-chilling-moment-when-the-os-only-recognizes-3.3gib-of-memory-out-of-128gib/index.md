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
date: 2025-06-15T23:36:47+09:00
lastmod: 2025-06-15T23:36:47+09:00

featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
image:
  preview_only: true
---

**結論: BIOSのアップデートで解決した**

{{< figure src="featured.jpg" caption="BIOSでは最初から合計128GiB(4x 32GiB)が認識されていたのに" >}}

昨今のノートPCは、8コア/16スレッドのCPUと32GiBのメモリであれば標準から少しアップグレードするだけでそこまで無理しなくても手に入る。対して自分が2018年から使っている検証用据え置きPCは8コア/16スレッドと64GiBメモリの組み合わせのままで物足りなさがあった。全体を買い替えるか悩んだ上でひとまずはメモリを増量して延命することにした。

DDR4の4スロットに、今までは16+16+16+16GiBの合計64GiBを取り付けていたが、これを32+32+32+32GiBの合計128GiBにする計画とした。CPU([AMD Ryzen 7 2700](https://www.amd.com/ja/support/downloads/drivers.html/processors/ryzen/ryzen-2000-series/amd-ryzen-7-2700.html#amd_support_product_spec))と、マザーボード([B450M Pro4](https://www.asrock.com/mb/AMD/B450M%20Pro4/index.jp.asp))の部分では128GiB対応をうたっていたので、まずは32GiBモジュールを先に2つ購入して、96GiB(32+32+16+16GiB)の構成へ。ここはまったく問題なく通過したので、安心して残りの2モジュールを注文。次の日到着してウキウキで128GiB(32+32+32+32GiB)の構成にしたら、OSからは3.3GiBしか利用できないトラブルが発生。

```bash
$ free -h
               total        used        free      shared  buff/cache   available
Mem:           3.3Gi       948Mi       1.0Gi       2.0Mi       1.4Gi       2.2Gi
Swap:           31Gi          0B        31Gi

$ free -b
               total        used        free      shared  buff/cache   available
Mem:      3580448768   994852864  1056645120     2134016  1528950784  2404077568
Swap:    34359734272           0 34359734272
```

不思議だったのは、3.3GiBしか使えない状態だけどBIOS画面と`dmidecode`ではしっかりと32GiBモジュールが4つ見えていたこと。

```bash
$ sudo dmidecode --type memory

# dmidecode 3.3
Getting SMBIOS data from sysfs.
SMBIOS 3.0.0 present.

Handle 0x000C, DMI type 16, 23 bytes
Physical Memory Array
        Location: System Board Or Motherboard
        Use: System Memory
        Error Correction Type: None
        Maximum Capacity: 256 GB
        Error Information Handle: 0x000B
        Number Of Devices: 4

Handle 0x0013, DMI type 17, 40 bytes
Memory Device
        Array Handle: 0x000C
        Error Information Handle: 0x0012
        Total Width: 64 bits
        Data Width: 64 bits
        Size: 32 GB
        Form Factor: DIMM
        Set: None
        Locator: DIMM 0
        Bank Locator: P0 CHANNEL A
        Type: DDR4
        Type Detail: Synchronous Unbuffered (Unregistered)
        Speed: 2666 MT/s
        Manufacturer: Micron Technology
        ...

Handle 0x0016, DMI type 17, 40 bytes
Memory Device
        Array Handle: 0x000C
        Error Information Handle: 0x0015
        Total Width: 64 bits
        Data Width: 64 bits
        Size: 32 GB
        ...

Handle 0x0019, DMI type 17, 40 bytes
Memory Device
        Array Handle: 0x000C
        Error Information Handle: 0x0018
        Total Width: 64 bits
        Data Width: 64 bits
        Size: 32 GB
        ...

Handle 0x001C, DMI type 17, 40 bytes
Memory Device
        Array Handle: 0x000C
        Error Information Handle: 0x001B
        Total Width: 64 bits
        Data Width: 64 bits
        Size: 32 GB
        ...
```

BIOSでは認識できているのにOSからは使えないということで、この時点ではBIOSアップデートをしたところで解消される自信はまったくなく、ただそれぐらいしか試せることがないのでBIOSアップデートの道に進んだ。[BIOSバージョン](https://www.asrock.com/mb/AMD/B450M%20Pro4/index.jp.asp#BIOS)も少し事情が入り組んでいて、3.60以降は、

> ASRock do NOT recommend updating this BIOS if Pinnacle, Raven or Summit Ridge CPU is being used on your system.

という注意書きがあり、Ryzen 7 2700(Pinnacle Ridge)では3.60以降を使うのはやめた方がよさそう。さらに、特定のバージョンに上げたら以前のバージョンには戻れないとの注意書きもあるので、現状の1.60から順番に上げていくものの、3.x系の3.10になっても128GiBが使えずこの時点で絶望。面倒になってきたので、間を飛ばしてPinnacle Ridgeがサポートされる最後のバージョンである3.50に一気に上げたらなぜか128GiBが使えるようになった。

- 1.60 - 3.3GiB
- 2.00 - 3.3GiB
- 3.10 - 3.3GiB
- 3.30 - ?
- 3.40 - ?
- 3.50 - 128GiB

具体的にどのバージョンで直ったかはわからないけども、それはそれでよしとする。BIOSアップデートで直る問題だとわかってしまった後であれば、冷静に起動時のログの差分を取ってBIOS-e820から133,667,749,887 Bytes分(`0x000000201f37ffff - 0x0000000100000000`)のメモリ情報が渡されていなかったとピンポイントでわかるけれども、BIOSアップデートの試行錯誤している間は肝が冷えました。

```diff
 kernel: BIOS-e820: [mem 0x00000000df000000-0x00000000dfffffff] reserved
 kernel: BIOS-e820: [mem 0x00000000f8000000-0x00000000fbffffff] reserved
 kernel: BIOS-e820: [mem 0x00000000fd000000-0x00000000ffffffff] reserved
+kernel: BIOS-e820: [mem 0x0000000100000000-0x000000201f37ffff] usable
 kernel: NX (Execute Disable) protection: active
 kernel: APIC: Static calls initialized

...

 kernel: SMBIOS 3.2.1 present.
-kernel: DMI: To Be Filled By O.E.M. To Be Filled By O.E.M./B450M Pro4, BIOS P3.10 03/07/2019
+kernel: DMI: To Be Filled By O.E.M. To Be Filled By O.E.M./B450M Pro4, BIOS P3.50 07/18/2019

...

-kernel: Memory: 3225936K/3611148K available (22528K kernel code, 4435K rwdata, 13952K rodata, 4988K init, 4728K bss, 384952K reserved, 0K cma-reserved)
+kernel: Memory: 131601868K/134146048K available (22528K kernel code, 4435K rwdata, 13952K rodata, 4988K init, 4728K bss, 2543920K reserved, 0K cma-reserved)
```

{{< figure src="memory.png" caption="64GiBから96GiBへは順調に行ったのに、128GiBになると思いきや3.3GiBに落ち込んでしまって生きた心地がしなかった数時間" >}}
