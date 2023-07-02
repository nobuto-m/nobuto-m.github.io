---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "物理マシンでUbuntu Serverのautoinstall"
slug: ubuntu-server-autoinstall-with-a-physical-machine
subtitle: ""
summary: ""
authors: []

tags: []
categories: []
keywords: ["Ubuntu"]

reading_time: false
show_related: true
share: false

year: 2023
date: 2023-07-02T16:29:52+09:00
lastmod: 2023-07-02T16:29:52+09:00

featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
image:
  preview_only: true
---

Ubuntu Serverを自動インストールする方法については、Ubuntu Serverガイドにすでにちゃんとした記事がある。

- [Automated Server installation](https://ubuntu.com/server/docs/install/autoinstall)
- [Automated Server install quickstart](https://ubuntu.com/server/docs/install/autoinstall-quickstart)
- [Automated Server installer config file reference](https://ubuntu.com/server/docs/install/autoinstall-reference)

あとは[柴田さんのgihyo.jp記事](https://gihyo.jp/admin/serial/01/ubuntu-recipe/0615)もあるので日本語でも解説されている。

Ubuntu Serverガイドにしたがって、自分用に最小のautoinstallファイルを作ってやってみた。

```yaml
#cloud-config
autoinstall:
  version: 1
  identity:
    hostname: ubuntu-server
    username: ubuntu
    # password=ubuntu
    password: "$6$exDY1mhS4KUYCE/2$zmn9ToZwTKLhCw.b4/b.ZRTIZM30JZ4QrOQ2aOXJ8yk96xpcCof0kxKwuX1kqLG/ygbJ1f8wxED22bTL4F46P0"
  ssh:
    install-server: yes  # https://launchpad.net/bugs/1974483
    allow-pw: no
  apt:
    geoip: false
  kernel:
    flavor: generic  # or hwe
```

ドキュメントにあるように、`kvm`コマンドと下記カーネルコマンドラインパラメーターを使うと完璧に動作する。
```bash
-append 'autoinstall ds=nocloud-net;s=http://_gateway:3003/'
```
けれども、カスタマイズしてない普通の22.04 LTSのISOだとうまくいかなくて、最初の言語選択の画面で止まってしまう。

結局はすごく小さなところが問題で、[セミコロンをエスケープしておかないと](https://www.gnu.org/software/grub/manual/grub/grub.html#Simple-commands)、autoinstallファイルをインストールするためのURLが無視されて渡らない。これだけの話で1時間以上悩んだ。[ドキュメントへのフィードバックはしたので](https://discourse.ubuntu.com/t/automated-server-install-quickstart/16614/50)、将来的に誰かの役に立てればと。

{{< figure src="featured.png" caption="GRUBの時はセミコロンのエスケープが必須" >}}
