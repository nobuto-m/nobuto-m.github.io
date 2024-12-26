---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "XG-100NEの新ファームウェアのテストが結局できなかった話"
slug: could-not-test-a-new-firmware-for-xg-100ne
subtitle: ""
summary: ""
authors: []

aliases:
- 1685705588

tags: []
categories: []
keywords: ["XG-100NE"]

reading_time: false
show_related: true
share: false

year: 2023
date: 2023-06-02T20:33:08+09:00
lastmod: 2023-06-02T20:33:08+09:00

featured: false
draft: false

# Featured image
# To use, add an image named "featured.jpg/png" to your page's folder.
image:
  preview_only: true
---

[光クロスのレンタルルーターであるXG-100NEの動作に耐えられなくて解約申請をして]({{< ref "post/2023/cancellation-of-xg-100ne-for-cross/index.md" >}})、そしたらその後にファームウェアのアップデートがリリースされた。返却前の週末に今使ってるルーターからレンタルルーターに切り戻して、まだ不具合が出るか出ないかだけでも確認して気持ちをはっきりさせたかったけど、結局単純な理由で確認できなかった。

手順としては単純で、[ダウンロードページ](https://web116.jp/ced/support/version/broadband/xg_100ne/)からファームウェア`ver.03.00.0002`を落としてルーターのWebUIから適用するだけ。で、そのまま使えばテストできたのに、何か魔が差したんですよね。不具合と言うからにはちゃんと初期状態で再現できるか確認しないと意味がないかな、と。そう思ったので、まずは使う前に工場出荷時の状態に戻すみたいな初期化ボタンを押したんです。で、再起動してIPv6はつながってIPv4がつながらなくなったときに、あっ、と。

XG-100NEはIPv4接続のためのMAP-Eバンドルというソフトウェアを[フレッツ・ジョイント](https://business.ntt-east.co.jp/service/joint/)の仕組みを通じて受け取って初めてMAP-Eで通信できるはずなので、初期化したことでこのすでに受け取り済みのソフトウェアも消えてしまった。で、解約申請を出してこのソフトウェア配信もちょうど止まったところだったので、このソフトウェアが再度ダウンロードされることはなく。はい、ここで手詰まり、テストできず。初期化しよう、とかさえ思わなければ。

{{< figure src="featured.png" caption="`http://ntt.setup:8888/t`にアクセスして、あっ、と思った瞬間。何もない。" >}}

おとなしくすぐに梱包して期限内にレンタルルーターを返送しました。これで我が家でXG-100NEについて脳みそを使う時間を割くのは完全に終了です。
