---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "NucBox3での運用一筋縄ではいかないかも"
slug: operating-nucbox3-may-be-tricky
subtitle: ""
summary: ""
authors: []

aliases:
- 1690287370

tags: []
categories: []
keywords: ["NucBox3"]

reading_time: false
show_related: true
share: false

year: 2023
date: 2023-07-25T21:16:10+09:00
lastmod: 2023-07-25T21:16:10+09:00

featured: false
draft: false

# Featured image
# To use, add an image named "featured.jpg/png" to your page's folder.
image:
  preview_only: true
---

[Raspberry Pi 2Bの代用として買ったNucBox3]({{< ref "../tried-truenas-scale-on-nucbox3/index.md" >}})を数週間運用してて、ある程度いい感じに動いてくれてると思ってたけど急に手がかかるようになった。マシンの上で動かしていたのは[n8nでの定期タスク]({{< ref "../pass-rss-new-item-to-webhook-with-n8n/index.md" >}})で、それ自体は負荷がかからないけどそれをわざわざsingle-node Kubernetesの上で動かしていたのでそれなりにディスクアクセスはあった。それでもまあ大したものじゃなかったけど、急にfilesystemがread-onlyに。

{{< figure src="featured.png" caption="~30 IO/sからの突然のread-only" >}}

こういうのが嫌だったので、SDカード運用のRaspberry Pi 2Bから離れたのに結局は同じ状況に。再起動してもすぐにread-onlyになってしまって、なぜかSSHも鍵認証で弾かれる。ただ、node-exporterだけはしっかり動いていて状況だけは詳細にわかる謎。

とりあえず、ファイルシステムの復旧か再インストールかを現地でして様子見するしかないけど、もしファンレスで温度が高めであることが原因だったらこのマシンという選択自体が間違いということになるので悲しいですね。

{{< figure src="temperature.png" caption="温度はむしろ最近落ち着いていた" >}}

一応、昼間は冷房のかかっている部屋に置いてあるのでそんなに想定温度とは違わないはずなんだけど。
