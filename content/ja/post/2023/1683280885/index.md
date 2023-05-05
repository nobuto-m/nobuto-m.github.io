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

前回、[SanDisk SDSSDA-2T00-G26](https://www.westerndigital.com/ja-jp/products/internal-drives/sandisk-ssd-plus-sata-iii-ssd)が[REGZAの2番組同時録画と別番組再生に追いつけなかった話]({{< ref "../1683173373/index.md" >}})で興味が出たので、同じシリーズの480GB(SDSSDA-480G-J26)も持ってたので同じ条件で軽く計測してみた。

{{< figure src="sandisk_granular.png" title="SanDisk SSD Plus 2TB" >}}

{{< figure src="featured.png" caption="SanDisk SSD Plus 480GB" >}}

これを見ると480GBの方がガタガタではあるけれども、予測しやすい理屈に合った動きをしているように見えるし、平均書き込み速度で上回ってて、レイテンシーの面でも優秀。SSDは容量が大きい方がだいたい書き込み速度が上がる印象だけどこういうこともあるんだなと。ファームウェアのリビジョンによる違いなのかもしれないけど。ちなみにTBWは大したことなくて、480GBの方が値が大きかった。
