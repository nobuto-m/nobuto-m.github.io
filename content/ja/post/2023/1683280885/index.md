---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "SanDisk SSD Plusシリーズは2TBの方が480GBより遅く見えちゃう"
slug: 1683280885
subtitle: ""
summary: ""
authors: []

tags: []
categories: []
keywords: ["SDSSDA-2T00-G26", "SDSSDA-480G-J26"]

reading_time: false
show_related: true
share: false

year: 2023
date: 2023-05-05T19:01:25+09:00
lastmod: 2023-05-05T19:01:25+09:00

featured: false
draft: false

# Featured image
# To use, add an image named "featured.jpg/png" to your page's folder.
image:
  preview_only: true
---

前回、[SanDisk SDSSDA-2T00-G26](https://www.westerndigital.com/ja-jp/products/internal-drives/sandisk-ssd-plus-sata-iii-ssd)が[REGZAの2番組同時録画と別番組再生に追いつけなかった話]({{< ref "../1683173373/index.md" >}})で興味が出たので、同じシリーズの480GB(SDSSDA-480G-J26)も持ってたので同じ条件で軽く計測してみた。古いSSD同士で何をいまさらって感じがあるけど、まあ見てみたかったので。

{{< figure src="sandisk_granular.png" title="SanDisk SSD Plus 2TB" >}}

{{< figure src="featured.png" caption="SanDisk SSD Plus 480GB" >}}

これを見ると480GBの方がガタガタではあるけれども、予測しやすい理屈に合った動きをしているように見えるし、平均書き込み速度で上回ってて、レイテンシーの面でも優秀。SSDは容量が大きい方がだいたい書き込み速度が上がる印象だけどこういうこともあるんだなと。ファームウェアのリビジョンによる違いなのかもしれないけど。


と思ったら普通にデータシートに1TBの方が他より遅いって書いてあった。2TBは最速っぽく書いてあるけど。

[Datasheet (PDF)](https://documents.westerndigital.com/content/dam/doc-library/en_us/assets/public/sandisk/product/internal-drives/ssd-plus-sata-iii-ssd/data-sheet-ssd-plus-sata-iii-ssd.pdf)
> * Read/write speeds of up to
>   545MB/s/450MBs** [2TB]
> * Read/write speeds of up to
>   535MB/s/350MBs** [1TB]
> * Read/write speeds of up to
>   535MB/s/445MBs** [480GB]
> * Read/write speeds of up to
>   530MB/s/440MBs** [240GB]


ちなみに実際のTBWはどちらも使いすぎってわけではなくて、大した値ではなかった。

[480GB]
```bash
$ sudo smartctl -A /dev/disk/by-id/ata-SanDisk_SDSSDA480G_XYZ -d sat
smartctl 7.2 2020-12-30 r5155 [x86_64-linux-6.1.0-1009-oem] (local build)
Copyright (C) 2002-20, Bruce Allen, Christian Franke, www.smartmontools.org

=== START OF READ SMART DATA SECTION ===
SMART Attributes Data Structure revision number: 1
Vendor Specific SMART Attributes with Thresholds:
ID# ATTRIBUTE_NAME          FLAG     VALUE WORST THRESH TYPE      UPDATED  WHEN_FAILED RAW_VALUE
  5 Retired_Block_Count     0x0032   100   100   000    Old_age   Always       -       0
  9 Power_On_Hours_and_Msec 0x0032   000   100   000    Old_age   Always       -       11604h+00m+00.000s
 12 Power_Cycle_Count       0x0032   100   100   000    Old_age   Always       -       1926
165 Unknown_Attribute       0x0032   100   100   000    Old_age   Always       -       7241469593843
166 Unknown_Attribute       0x0032   100   100   000    Old_age   Always       -       2
167 Unknown_Attribute       0x0032   100   100   000    Old_age   Always       -       0
168 Unknown_Attribute       0x0032   100   100   000    Old_age   Always       -       15
169 Unknown_Attribute       0x0032   100   100   000    Old_age   Always       -       0
170 Reserve_Block_Count     0x0032   100   100   000    Old_age   Always       -       0
171 Program_Fail_Count      0x0032   100   100   000    Old_age   Always       -       0
172 Erase_Fail_Count        0x0032   100   100   000    Old_age   Always       -       0
173 Unknown_SandForce_Attr  0x0032   100   100   000    Old_age   Always       -       8
174 Unexpect_Power_Loss_Ct  0x0032   100   100   000    Old_age   Always       -       120
187 Reported_Uncorrect      0x0032   100   100   000    Old_age   Always       -       0
188 Command_Timeout         0x0032   100   100   000    Old_age   Always       -       0
194 Temperature_Celsius     0x0022   061   022   000    Old_age   Always       -       39 (Min/Max 0/78)
199 SATA_CRC_Error_Count    0x0032   100   100   000    Old_age   Always       -       0
230 Life_Curve_Status       0x0032   100   100   000    Old_age   Always       -       3277067780859
232 Available_Reservd_Space 0x0033   100   100   004    Pre-fail  Always       -       100
233 SandForce_Internal      0x0032   100   100   000    Old_age   Always       -       3950
234 SandForce_Internal      0x0032   100   100   000    Old_age   Always       -       43857
241 Lifetime_Writes_GiB     0x0030   253   253   000    Old_age   Offline      -       13081
242 Lifetime_Reads_GiB      0x0030   253   253   000    Old_age   Offline      -       6245
244 Unknown_Attribute       0x0032   000   100   000    Old_age   Always       -       0
```
-> TBW: `~13 TB`

[2TB]
```
$ sudo smartctl -A /dev/disk/by-id/ata-SanDisk_SSD_PLUS_2000GB_XYZ -d sat
smartctl 7.2 2020-12-30 r5155 [x86_64-linux-6.1.0-1009-oem] (local build)
Copyright (C) 2002-20, Bruce Allen, Christian Franke, www.smartmontools.org

=== START OF READ SMART DATA SECTION ===
SMART Attributes Data Structure revision number: 1
Vendor Specific SMART Attributes with Thresholds:
ID# ATTRIBUTE_NAME          FLAG     VALUE WORST THRESH TYPE      UPDATED  WHEN_FAILED RAW_VALUE
  5 Reallocated_Sector_Ct   0x0032   100   100   000    Old_age   Always       -       0
  9 Power_On_Hours          0x0032   100   100   000    Old_age   Always       -       11767
 12 Power_Cycle_Count       0x0032   100   100   000    Old_age   Always       -       343
165 Unknown_Attribute       0x0032   100   100   000    Old_age   Always       -       1378
166 Unknown_Attribute       0x0032   100   100   ---    Old_age   Always       -       10
167 Unknown_Attribute       0x0032   100   100   ---    Old_age   Always       -       98
168 Unknown_Attribute       0x0032   100   100   ---    Old_age   Always       -       27
169 Unknown_Attribute       0x0032   100   100   ---    Old_age   Always       -       1623
170 Unknown_Attribute       0x0032   100   100   ---    Old_age   Always       -       0
171 Unknown_Attribute       0x0032   100   100   000    Old_age   Always       -       0
172 Unknown_Attribute       0x0032   100   100   000    Old_age   Always       -       0
173 Unknown_Attribute       0x0032   100   100   000    Old_age   Always       -       10
174 Unknown_Attribute       0x0032   100   100   000    Old_age   Always       -       239
184 End-to-End_Error        0x0032   100   100   ---    Old_age   Always       -       0
187 Reported_Uncorrect      0x0032   100   100   000    Old_age   Always       -       0
188 Command_Timeout         0x0032   100   100   ---    Old_age   Always       -       0
194 Temperature_Celsius     0x0022   058   071   000    Old_age   Always       -       42 (Min/Max 14/71)
199 UDMA_CRC_Error_Count    0x0032   100   100   ---    Old_age   Always       -       0
230 Unknown_SSD_Attribute   0x0032   100   100   000    Old_age   Always       -       3698000397149
232 Available_Reservd_Space 0x0033   100   100   005    Pre-fail  Always       -       100
233 Media_Wearout_Indicator 0x0032   100   100   ---    Old_age   Always       -       21640
234 Unknown_Attribute       0x0032   100   100   000    Old_age   Always       -       37214
241 Total_LBAs_Written      0x0030   100   100   000    Old_age   Offline      -       14543
242 Total_LBAs_Read         0x0030   100   100   000    Old_age   Offline      -       9730
244 Unknown_Attribute       0x0032   000   100   ---    Old_age   Always       -       0
```
-> TBW: `~14.5 TB`
