---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Windows用に使ってるSSD-CK500N3G2/Nもテストしてみた"
slug: tested-ssd-ck500n3g2-n
subtitle: ""
summary: ""
authors: []

aliases:
- 1684645587

tags: []
categories: []
keywords: ["SSD-CK500N3G2/N"]

reading_time: false
show_related: true
share: false

year: 2023
date: 2023-05-27T16:31:14+09:00
lastmod: 2023-05-27T16:31:14+09:00

featured: false
draft: false

# Featured image
# To use, add an image named "featured.jpg/png" to your page's folder.
image:
  preview_only: true
---

基本はノートPCと検証機のどっちともUbuntuを常用してて、ごくごくたまーにWindowsが必要になることもあるので、[Rufus](https://rufus.ie/)で外付けSSDにWindowsをインストールしてある。で、この用途で使ってる[KIOXIA EXCERIA G2 SSD-CK500N3G2/N](https://www.buffalo.jp/product/detail/ssd-ck500n3g2_n.html)は値段で選んだので特に性能は気にしたことはなかったので、この機会に測ってみた。

{{< figure src="KIOXIA-EXCERIA_G2_SSD.png" caption="キャッシュ溢れみたいな変な落ち込みがない" >}}

シーケンシャルアクセスはNVMeのSSDをUSBにケースで変換して接続していることを考慮してまったく文句なし。最初は測定しようと思ったときに、あーWindowsが入っちゃってるから書き込みテストできないじゃん、書き込みテストした後にもう一回Windowsインストールしてもいいけど面倒だなと思ったところにGNOME Disksの説明を読んで驚いた。

{{< figure src="featured.png" caption="書き込みテストはすでにデータがあるドライブにも実行可" >}}

> Benchmarking the write-rate of a disk requires exclusive access to the
> disk (e.g. the disk or its partitions cannot be mounted or in use) and
> involves reading data and then writing it back. As a result, the
> contents of the disk is not changed.

GNOME Disksだけが独占的にドライブにアクセスしている前提で、読んだデータをそのまま書き直すのでベンチマークが完走する限りはデータ破壊がないので、確かに頭いいなぁと。読んだデータしか使えないので、しっかりとランダムなデータを生成して書き込むタイプのベンチマークに比べると色んなレイヤーの影響で変に良い値が出てしまう懸念があるけどまあ簡易ベンチマークとしては本当に頭がいい。

あとはUSB経由でつないでいても、ちゃんと色々な値が取れてすごいなぁと。

```
$ sudo smartctl -a /dev/sda
smartctl 7.2 2020-12-30 r5155 [x86_64-linux-6.1.0-1012-oem] (local build)
Copyright (C) 2002-20, Bruce Allen, Christian Franke, www.smartmontools.org

=== START OF INFORMATION SECTION ===
Model Number:                       KIOXIA-EXCERIA G2 SSD
Serial Number:                      XYZ
Firmware Version:                   ECFA17.1
PCI Vendor/Subsystem ID:            0x1e0f
IEEE OUI Identifier:                0x8ce38e
Total NVM Capacity:                 500,107,862,016 [500 GB]
Unallocated NVM Capacity:           0
Controller ID:                      1
NVMe Version:                       1.3
Number of Namespaces:               1
Namespace 1 Size/Capacity:          500,107,862,016 [500 GB]
Namespace 1 Formatted LBA Size:     512
Namespace 1 IEEE EUI-64:            8ce38e 0500bbcc25
Local Time is:                      Sun May 21 14:06:49 2023 JST
Firmware Updates (0x12):            1 Slot, no Reset required
Optional Admin Commands (0x0017):   Security Format Frmw_DL Self_Test
Optional NVM Commands (0x005f):     Comp Wr_Unc DS_Mngmt Wr_Zero Sav/Sel_Feat Timestmp
Log Page Attributes (0x0a):         Cmd_Eff_Lg Telmtry_Lg
Maximum Data Transfer Size:         512 Pages
Warning  Comp. Temp. Threshold:     72 Celsius
Critical Comp. Temp. Threshold:     90 Celsius

Supported Power States
St Op     Max   Active     Idle   RL RT WL WT  Ent_Lat  Ex_Lat
 0 +     7.69W       -        -    0  0  0  0        1       1
 1 +     6.18W       -        -    1  1  1  1        1       1
 2 +     5.42W       -        -    2  2  2  2        1       1
 3 -   0.0500W       -        -    3  3  3  3     7000    5000
 4 -   0.0050W       -        -    4  4  4  4    13000   36000

Supported LBA Sizes (NSID 0x1)
Id Fmt  Data  Metadt  Rel_Perf
 0 +     512       0         2
 1 -    4096       0         1

=== START OF SMART DATA SECTION ===
SMART overall-health self-assessment test result: PASSED

SMART/Health Information (NVMe Log 0x02)
Critical Warning:                   0x00
Temperature:                        34 Celsius
Available Spare:                    100%
Available Spare Threshold:          5%
Percentage Used:                    0%
Data Units Read:                    337,687 [172 GB]
Data Units Written:                 314,483 [161 GB]
Host Read Commands:                 12,348,912
Host Write Commands:                13,616,612
Controller Busy Time:               17
Power Cycles:                       26
Power On Hours:                     10
Unsafe Shutdowns:                   24
Media and Data Integrity Errors:    0
Error Information Log Entries:      0
Warning  Comp. Temperature Time:    0
Critical Comp. Temperature Time:    0

Warning: NVMe Get Log truncated to 0x200 bytes, 0x200 bytes zero filled
Error Information (NVMe Log 0x01, 16 of 63 entries)
No Errors Logged
```
