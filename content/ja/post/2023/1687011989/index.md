---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "NucBox3でTrueNAS SCALEを試してみた"
slug: 1687011989
subtitle: ""
summary: ""
authors: []

tags: []
categories: []
keywords: ["NucBox3", "TrueNAS SCALE"]

reading_time: false
show_related: true
share: false

year: 2023
date: 2023-06-17T23:26:29+09:00
lastmod: 2023-06-17T23:26:29+09:00

featured: false
draft: false

# Featured image
# To use, add an image named "featured.jpg/png" to your page's folder.
image:
  preview_only: true
---

[NucBox3](https://www.gmktec.com/products/nucbox-3-mini-pc-windows-11-home-os)はファンレスで安いところが良さそうだと思って、Raspberry Piを置き換える用途でしばらく前に買ってあった。とにかく値段が安い（今日時点で実売12,383円）。それとは別に流行りのIntel N100と2.5G NICが搭載されてるもっと高めのものを家庭用NASを置き換える用途で買いたいなぁと思ってて、予行演習のためにまずはIntel Celeron J4125と1G NICのNucBox3に[TrueNAS SCALE](https://www.truenas.com/truenas-scale/)をインストールしてみた。

まず、NucBox3は最大限にコスパ重視って感じ。噂に聞く、全くuniqueではないUUID `03000200-0400-0500-0006-000700080009`は健在。

```shell
ubuntu
    description: Desktop Computer
    product: NucBox3
    vendor: GMKtec
    version: Default string
    serial: Default string
    width: 64 bits
    capabilities: smbios-3.2.0 dmi-3.2.0 smp vsyscall32
    configuration: boot=normal chassis=desktop family=Default string uuid=03000200-0400-0500-0006-000700080009
```

DDR4の8GBメモリもvendor `ABCD`のproduct `123456789012345678`、シリアル番号は`1234`。

```shell
     *-memory
          description: System Memory
          physical id: 23
          slot: System board or motherboard
          size: 8GiB
        *-bank:0
             description: DIMM DDR4 Synchronous 2400 MHz (0.4 ns)
             product: 123456789012345678
             vendor: ABCD
             physical id: 0
             serial: 1234
             slot: A1_DIMM0
             size: 8GiB
             width: 64 bits
             clock: 2400MHz (0.4ns)
```

全部こんな感じなんでしょと思いきや、M.2 SATAの128GB SSDは製品名は入ってなかったけどシリアル番号っぽいものは入ってる。ちなみに簡易ベンチマークはこんな感じ。


{{< figure src="gnome-disks.png" caption="SSDはそれなり。可もなく不可もなく。" >}}

{{< figure src="lstopo.png" caption="`lstopo`の出力" >}}

さくっと情報を確認したので、こっからはTrueNAS SCALEのインストールテスト。[SCALEの方はDebianベース](https://www.truenas.com/compare/)なのがいいなと思って選んだんですが、インストーラーもアプライアンスとしてカスタマイズされているので特に迷うところはなし。

{{< figure src="featured.png" caption="" >}}

Shellアクセスもあるけども、[アプライアンスとして使われるのを意図しているので](https://www.truenas.com/community/threads/no-apt-after-update-to-release.99579/#post-686801)aptやdpkgコマンドはPermission deniedが返る。どんな方法で制限してるんだろうと思ったけど、executableフラグを落としてあるだけだった。

```bash
admin@truenas[~]$ find /usr/bin ! -executable
/usr/bin/docker-compose
/usr/bin/dpkg
/usr/bin/apt-get
/usr/bin/apt-extracttemplates
/usr/bin/apt-mark
/usr/bin/apt-sortpkgs
/usr/bin/apt-key
/usr/bin/apt
/usr/bin/apt-cache
/usr/bin/apt-ftparchive
/usr/bin/apt-config
/usr/bin/apt-cdrom
```

まったくもって推奨されてないけど、ちょっとiperfでも実行してみるかと思って一時的にaptを使えるようにしたけど、iperf3はデフォルトで入っているというオチでした。TrueNAS SCALEのインストールしたバージョン`“Bluefin” 22.12`はDebian 11 bullseyeベースなんだけど、アプライアンスなので結構手が入ってる。opensslのバージョンとかも。

```bash
admin@truenas[~]$ apt policy openssl
openssl:
  Installed: 1.1.1t-001+deb11u4
  Candidate: 1.1.1n-0+deb11u4
  Version table:
 *** 1.1.1t-001+deb11u4 100
        100 /var/lib/dpkg/status
     1.1.1n-0+deb11u4 1000
        500 http://apt.tn.ixsystems.com/apt-direct/bluefin/22.12.3/debian-security bullseye-security/main amd64 Packages
     1.1.1n-0+deb11u3 900
        900 http://apt.tn.ixsystems.com/apt-direct/bluefin/22.12.3/debian bullseye/main amd64 Packages
```

NUCの形状からしてNASとして使う時のデータ置き場はUSB接続の外部HDDになる予定で、USB接続は推奨はされていないもののZFS用のデバイスとしてちゃんと使える。

{{< figure src="usb_not_recommended.png" caption="USBストレージはnot recommendedでwarning扱い" >}}

で、ここまでいろいろ見てきて、TrueNAS SCALEはうちの家庭用NASとしてピッタリじゃね、と。最初はDebianベースだからっていう理由で触り始めたけど、NASとしてこれこそが求めていたものでは。

ただ、[TrueNAS SCALEのアプリストア](https://www.truenas.com/apps/)を使う時に、SCALE自体がインストールされているドライブとは別のドライブを必要とする。ので、SCALE自体を内蔵SSDに入れるとして、アプリ（Kubernetes上で動く)が外付けHDDで動作するのもなんだかなと。一般にNASにアプリストアってあるあるだけど、んーでも結局そんなに使うことになるのかしら。将来のために、デフォルトで全領域を使う`boot-pool`を何とかサイズ指定したり後から縮小したりできるかを調べることからかな、と。

```
$ lsblk -f
NAME     FSTYPE     FSVER LABEL     UUID                                 FSAVAIL FSUSE% MOUNTPOINT
sda
├─sda1
├─sda2   vfat       FAT32 EFI       2D4A-EDB1
├─sda3   zfs_member 5000  boot-pool 572534033895956289
└─sda4
  └─sda4 swap       1               9a1c0394-e549-4ef5-a15b-dc69f9a39e41                [SWAP]
sdb
├─sdb1
└─sdb2   zfs_member 5000  external  9260921456295045295
```

Intel Celeron J4125と1G NICでテストしててさくさく動いてしまうので、N100と2.5Gとか要らないような気がしてくる。現状で十分というかアプリ、コンテナ、VMをそんなに動かすのかなと。ちょっと悩んだ後に、安いのは正義ということでNucBox3をまずはもう一個買いました。ので、これでしばらく運用してみて本当に必要だったらスペックを考え直すことにする。
