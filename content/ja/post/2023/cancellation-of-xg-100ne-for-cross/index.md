---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "光クロス対応レンタルルーターの解約申請をした"
slug: cancellation-of-xg-100ne-for-cross
subtitle: ""
summary: ""
authors: []

aliases:
- 1683091989

tags: []
categories: []
keywords: ["XG-100NE", "VR-U500X"]

reading_time: false
show_related: true
share: false

year: 2023
date: 2023-05-03T14:33:09+09:00
lastmod: 2023-05-03T14:33:09+09:00

featured: false
draft: false

# Featured image
# To use, add an image named "featured.jpg/png" to your page's folder.
image:
  preview_only: true
---

光クロスを契約した当初は10Gbps対応uplink/downlinkが1つずつ付いたルーターが⽉額550円で借りられるなんて激安だと思ったけど、この[XG-100NE](https://flets.com/cross/router.html)は独特の詰まり方をするので耐えられなくなってしまった。

https://gihyo.jp/admin/serial/01/ubuntu-recipe/0706
> 未解決の問題として、Androidスマートフォンの画面スリープからの復帰直後にICMP(IPv4)もTCP(IPv6)も問題なく疎通できるのにも関わらず、数分間だけTCP(IPv4)で再送が多発する場合があります。常に再現するわけではなく、しばらく待ったり、Android側でWi-Fiのオンオフ操作をすると即座に直ってしまうため

このように書くとMAP-Eのポートが足りなくなってるだけじゃないの、と読めるし、`セキュリティログ(IPv4)`というところに`利用可能なポート無し`とも出てくる。確定じゃんと思うけども、不思議なのはクライアント側から送られてくる通信がサーバー側から見えるのでポートの割り当て自体はできているように見えること。あと、クライアントの数が鍵な気がする(15-20台とか）。`利用可能なポート無し`は常に出ているので限界な状態をさらに超えると発生するのかも。

{{< tweet user="nobuto_m" id="1487425066338238465" >}}

で、発生した場合の通信をよく見てても特にポート枯渇してるようには見えず、`TCP Port numbers reused`と`TCP Retransmission`が観測されるのでなんとなくNAT/conntrackに相当するところが怪しいけれどもルーター側では詳しいログが出ないので詳細はわからず。

{{< tweet user="nobuto_m" id="1497959192237572096" >}}

現象が発生したらクライアント側から再接続しなきゃいけないのが面倒で、しかも再接続した後からは普通に使えてしまう謎。NEC製なので他のメーカーで切り分けしたくてまずは10Gbps対応していない5千円弱のBuffaloルーターを買ってきて取り替えたら現象がまったく発生しない！

もしかしたらNEC製XG-100NEの方が規格に則った正しい動きということがあるのかもしれないけど、やっぱり現象が発生しない方がいいのでNEC製じゃない10Gbps対応かつOCNバーチャルコネクトにしっかり対応したものを探して最終的には[BuffaloのVR-U500X](https://www.buffalo.jp/product/detail/vr-u500x.html)を購入。ある程度値段はするものの10Gbps対応ルーターとして長く動いてくれればそれでよし。無線LAN一体型ではないので、アクセスポイントがルーターとは別に選べることがメリットだと思い込むことにします。

肝心のレンタルルーター解約手続きはお電話のみの対応っぽかったので、オペレーターにつながるまで10分、その後15分ぐらいで手続きは完了。工事扱いになるので工事日にMAP-Eでつながらない時間ができないかだけ心配。
