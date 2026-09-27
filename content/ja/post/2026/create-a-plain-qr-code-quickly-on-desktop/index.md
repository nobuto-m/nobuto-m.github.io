---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "なんの変哲もないQRコードをさくっと作ってくれるGNOME Decoder"
slug: create-a-plain-qr-code-quickly-on-desktop
subtitle: ""
summary: ""
authors: []

tags: []
categories: []
keywords: ["Ubuntu"]

reading_time: false
show_related: true
share: false

year: 2026
date: 2026-09-27T11:27:03+09:00
lastmod: 2026-09-27T11:27:03+09:00

featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
image:
  preview_only: true
---

QRコードを作る機会はあまりないけども、それでもスライドの中に埋め込む必要がたまに出てくる。Google SlidesはQRコードを生成する機能はデフォルトではないはずで、何かしらのアドオンを入れるか生成済みのものを画像として貼り付けることになる（ここでGoogle SlidesはSVGのインポートができないのもイマイチではある）。

Ubuntu上でQRコードを作るツールはいくつかあったけども、GUI付きのものは依存関係でいろいろ引っ張ってきたりするので、結局は次のような感じでコマンドラインで生成していた。

```bash
$ qrencode -s 16 \
    -o qrencode_qrcode-google.com.png \
    https://www.google.com/
```

{{< figure src="qrencode_qrcode-google.com.png" caption="`qrencode`コマンドで作成した結果" width="240">}}

URLをQRコードに変換するだけならWebブラウザの「共有」機能を使えばいいじゃないかというのはその通りなのだが、ブラウザごとにブランディングが付いてきてしまって、このブランディングをオフにする設定がなさそう。

{{< figure src="firefox_qrcode-google.com.png" caption="Firefoxで生成した結果" width="240">}}

{{< figure src="chromium_qrcode_www.google.com.png" caption="Chromiumで生成した結果" width="240">}}

そこで、最近[DecoderというGNOME用アプリ](https://apps.gnome.org/Decoder/)を知って試してみたら私の用途にぴったり。26.04 LTS以上であればuniverseにあるのでAPTでさくっと入る。

{{< figure src="featured.png" caption="DecoderでURLを含むQRコードを作成" >}}
