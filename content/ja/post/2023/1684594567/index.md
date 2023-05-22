---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "ネットワーク配線の整理をした"
slug: 1684594567
subtitle: ""
summary: ""
authors: []

tags: []
categories: []
keywords: ["VR-U500X", "MFC-L2750DW"]

reading_time: false
show_related: true
share: false

year: 2023
date: 2023-05-20T23:56:07+09:00
lastmod: 2023-05-20T23:56:07+09:00

featured: false
draft: false

# Featured image
# To use, add an image named "featured.jpg/png" to your page's folder.
image:
  preview_only: true
---

ONU、ルーターなどのネットワーク機器はクローゼットというか物置きというか、というところに置いてあるのだが、そのままだとケーブル類がぐちゃぐちゃになってしまうので整理してみた。本格的な19インチラックは導入せず、あくまで家庭用っぽくする方針で。

{{< figure src="before.jpg" caption="整理前" >}}

3月に複合機を買ったときに同時にIKEAのテーブルを買っておいて、テーブルの上を複合機、下をネットワーク機器とまずは空間を分けておいた。必要になった段階でIKEAのテーブルを["LackRack"](https://wiki.eth0.nl/index.php/LackRack)として使って1Uだけ使うつもりだった。で、ルーターを乗せるためだけに棚板の値段を調べたりして、結構高いななんて思いながら4月に買って最上段に取り付けて完成。とはならずに、棚板の落下防止の返し部分があることで、ルーターが入らなかった。毎回ネジを緩めたりすれば出し入れできるけれどもそれは果たして家庭用なのかなと。

結局はルーター用の棚板を2段目にして空間を確保し、空いた一段目をそのままにしておくのはあれなので、特に必要のなかったケーブルオーガナイザーも買った。圧迫感を与えないように白色で探したので、配線が丸見えになる魚の骨タイプしかなかった。

{{< figure src="featured.jpg" caption="整理後" >}}

出来栄えとしては決して美しくはないけれども、当初の目的である床部分から配線を持ち上げる、ということは達成したのでよし。LackRackを初めてやってみた感想としては1U以上はやっぱり普通にメタルラック買った方がいいんじゃないでしょうか。使ったネジはM6の六角フランジタッピング。当然のことながらIKEAのテーブルは穴を開けられることを想定してないし、チップ材なのでドリルで穴開けると手応えなかったりネジ打ち込んでも最後の方空転したりする箇所がちらほらあるので。

{{< figure src="screw_hole.jpg" caption="ドリルで下穴も開けた" >}}

あと最近光クロスの上りが最速の計測値でも2.5Gbpsを超えなくなってきたので、ちょっとはONUの温度下がらないかなと思ってあえて速度を落とすように設定してみた。インターネット側は10Gbpsから5Gbpsへ、LAN側は検証機を10Gbpsから2.5Gbps、他のポートとの合計で4.5Gbpsになるように。でもこれをやるとなぜ10Gbpsを契約しているのだろうという気持ちになるので、まずは様子見。

と思ったけども、やっぱり2.5Gbpsだと10G対応のルーターを買った意味がないので落とすのは5Gbpsまでに。

{{< figure src="ports_5000base-t.png" caption="結局落とすのは5Gbpsまでにした" >}}
