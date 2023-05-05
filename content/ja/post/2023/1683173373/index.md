---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "REGZAの録画用ドライブをSSD-SCT2.0U3BA/Nに交換した"
slug: 1683173373
subtitle: ""
summary: ""
authors: []

tags: []
categories: []
keywords: ["SSD-SCT2.0U3BA/N", "SDSSDA-2T00-G26"]

reading_time: false
show_related: true
share: false

year: 2023
date: 2023-05-04T13:09:33+09:00
lastmod: 2023-05-04T13:09:33+09:00

featured: false
draft: false

# Featured image
# To use, add an image named "featured.jpg/png" to your page's folder.
image:
  preview_only: true
---

[SSD-SCT2.0U3BA/N](https://www.buffalo.jp/product/detail/ssd-sct2.0u3ba_n.html)が15,980円と少し安くなっていたので思い切って買った。

{{< figure src="featured.jpg" title="" >}}

HDDのガリガリ音と振動の反響音がリビングで聞こえるのが嫌で、今までも2TBのSSDとして[SanDisk SDSSDA-2T00-G26](https://www.westerndigital.com/ja-jp/products/internal-drives/sandisk-ssd-plus-sata-iii-ssd)をUSB接続で使っていた。ただこれがREGZAと組み合わせると全然期待通りに動いてくれない。テレビの録画なんて2番組同時録画してもせいぜい2x24Mbps、で同時録画中に他の番組を再生しても24Mbpsなので、6MB/sの書き込みと3MB/sの読み出しというHDDですら楽々こなせるタスクなのに、なぜかこのSanDiskのやつだとテレビ自体がエラーを吐いたり、録画データが破損してブロックノイズが出たりする。熱の影響かと思ってケースから出して裸で運用したり、USBからの電源供給が足りないかと思って補助電源を追加してもダメ。

テレビが悪いのかSSDが悪いのか自信がないまま新しいSSDを買ったけど、結果として問題なく動いてくれてよかった。

REGZAはXFSを使ってるのでファイルシステムか中身をコピーしてってのは可能かもしれないけど、今回はさくっとまるごと移行。

```bash
$ lsblk -f /dev/disk/by-id/ata-SanDisk_SSD_PLUS_2000GB_XYZ
NAME   FSTYPE FSVER LABEL UUID                                 FSAVAIL FSUSE% MOUNTPOINTS
sda
└─sda1 xfs                d5ea386b-ad90-47fc-9105-383cb0b00eba  118.8G    94% /media/nobuto/d5ea386b-ad90-47fc-9105-383cb0b00eba
```

移行元と移行先のSSDのサイズはピッタリ同じなのでリサイズを考慮しなくていい。

```bash
$ lsblk \
    /dev/disk/by-id/ata-SanDisk_SSD_PLUS_2000GB_XYZ \
    /dev/disk/by-id/usb-BUFFALO_SSD-SCTU3A_XYZ-0\:0
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sda      8:0    0  1.8T  0 disk
...
sdb      8:16   0  1.8T  0 disk
...

$ lsblk -b \
    /dev/disk/by-id/ata-SanDisk_SSD_PLUS_2000GB_XYZ \
    /dev/disk/by-id/usb-BUFFALO_SSD-SCTU3A_XYZ-0\:0
NAME   MAJ:MIN RM           SIZE RO TYPE MOUNTPOINTS
sda      8:0    0  2000398934016  0 disk
...
sdb      8:16   0  2000398934016  0 disk
...
```

で、移行元のドライブも壊れてるわけではないので、`ddrescue`ではなくさくっと`dd`で。

```bash
$ sudo dd \
    if=/dev/disk/by-id/ata-SanDisk_SSD_PLUS_2000GB_XYZ \
    of=/dev/disk/by-id/usb-BUFFALO_SSD-SCTU3A_XYZ-0\:0 \
    bs=4M status=progress oflag=sync

2000398934016 bytes (2.0 TB, 1.8 TiB) copied, 18102.6 s, 111 MB/s
```

最初のうちは200-300MB/sとかでコピーされてたけれども徐々に100MB/sぐらいに落ち着いて結局5時間以上かかることに。


移行が終わってからSanDiskのSSDをテストすると、新しいSSDの方が全然安定してる。特にレイテンシー。

{{< figure src="sandisk.png" title="旧SSD、あんまり安定していない" >}}

{{< figure src="buffalo.png" title="新SSD、安定してる" >}}

条件を変えてもっと長く書き込みすると、なるほどテレビ録画といえどもこのSSDにはきつかったのかもしれない、という図が出せる。

{{< figure src="sandisk_long.png" title="旧SSD、長く書くと10MB/sぐらいまで落ち込むこともある" >}}

旧SSDの費用が無駄になった感じがするけど、テレビ自体は悪くなさそうというのがわかっただけでも良しとする。
