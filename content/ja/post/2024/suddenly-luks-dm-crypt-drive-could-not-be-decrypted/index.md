---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "突然LUKS(dm-crypt)起動ドライブの暗号化が解除できなくなった話"
slug: suddenly-luks-dm-crypt-drive-could-not-be-decrypted
subtitle: ""
summary: ""
authors: []

aliases:
- 1706798132

tags: []
categories: []
keywords: []

reading_time: false
show_related: true
share: false

year: 2024
date: 2024-02-01T23:35:32+09:00
lastmod: 2024-02-01T23:35:32+09:00

featured: false
draft: false

# Featured image
# To use, add an image named "featured.jpg/png" to your page's folder.
image:
  preview_only: true
---

{{< figure src="featured.jpg" caption="絶望の場面" >}}

仕事に使ってるメインのマシンでもnobleの開発版を使ってるんですが、月曜日の昼飯前に再起動かけたら暗号化ドライブの解除パスワードを何度入力しても

> cryptsetup: ERROR: dm_crypt-0: cryptsetup failed, bad password or options?

と言われてしまってデスクトップまでたどり着けない。何かしらのリグレッションで暗号化ドライブのヘッダが壊れたのではと思い、これは再インストールしてバックアップから切り戻すしかないのではと絶望。簡単にできるようにしてあるとは言え1時間以上はかかるので。

すぐには諦めきれなかったのでまずはUSBメモリでUbuntuのLive sessionを立ち上げると、暗号化ドライブがあっさりマウントできてしまった。ということはドライブ自体が破損しているわけではないので、パッケージのリグレッションで暗号化が解除できない状況に陥っているのでは、と`/var/log/apt/history.log`とにらめっこ。いかんせん開発版なので、前回正常に起動できたときからのアップデートはバイナリパッケージの単位で412個。それなのに、`cryptsetup`とかそれっぽいパッケージのアップデートはないし、カーネルを一個前のにしてもダメ。かろうじて`libgcrypt20`というinitramfsにも入る、かつcrypt関連のパッケージのアップデートがあったので、これをダウングレードしてみることに。

USB起動中に、元のマシンのinitramfsの再生成ってどうやるんだっけと調べつつ

```bash
$ sudo cryptsetup open /dev/nvme0n1p3 dm_crypt-0

$ sudo mount /dev/mapper/ubuntu--vg-ubuntu--lv /mnt
$ sudo mount /dev/nvme0n1p2 /mnt/boot
$ sudo mount /dev/nvme0n1p1 /mnt/boot/efi

$ for name in proc sys dev run; do
    sudo mount --bind /$name /mnt/$name
done

$ sudo chroot /mnt/
```

という感じで[Debian Wiki](https://wiki.debian.org/RescueLive)を見ながらchrootした。ここでの鍵はopenするときに`dm_crypt-0`という名前でなければ後々不都合があり、なぜならそれは`/etc/crypttab`に`dm_crypt-0`と書かれているから。ただ、`crypttab`はopenした後にしか読めないので名前を覚えてなくて何回かやり直した。

ここまでくればパッケージのダウンロードやダウングレードも簡単で、initramfsの再生成も
```bash
$ sudo update-initramfs -u -k all
```
とするだけ。

でも再起動しても問題は解決していない。。。`libgcrypt20`が違うんだったら412個全部のパッケージをダウングレードして切り分けするのもしんどいので諦めようかと思ったそのとき、ネット上でリカバリーモード（シングルユーザーモード）なら解除できる時あるよっていう情報を見かけて、そんなはずはないでしょと思ってリカバリーモードで起動したら解除できた！crypt関連のモジュール関係ないじゃん！Plymouthじゃん！そこは考えてなかったよ！

ということで`/etc/default/grub`から`splash`を除いてPlymouthなしで起動するようにして応急処置完了。

ただ、重要そうな問題のような気がしたので再現環境を作りたかったけど、シンプルなパスワードを設定すると100%解除に成功してしまって再現できない。何がトリガーなのかよくわからないまま、PlymouthをダウングレードしてPlymouthのリグレッションであることを確認したりしていた。

ふと、Yubikeyがコンマ何秒の間に30文字以上を一気にタイプするからではと思い、Yubikeyを再フォーマットしてクリーンな状態でやったら無事再現できた。ただここで自分のYubikeyの使い方に関して補足しておくと、LUKSのパスフレーズ打つときにYubikeyから出力される文字列だけで解除しているわけではなくて（それだとYubikeyとノートPCを強奪されたらだれでも解除できちゃうので）、自分でパスフレーズを手打ちしてそれを打ち終わったらYubikeyのボタンをタッチして続けて打ち込む、という一つのパスフレーズボックスでなんちゃって二要素認証っぽいことにしている。

```
自分で打った文字列 + Yubikeyから出力される文字列(30文字以上) + <ENTER>
```

もうちょっとちゃんとやるなら毎回同じ文字列をYubikeyから出すんじゃなくて、Challenge-response形式にする[yubikey-luks](https://launchpad.net/ubuntu/+source/yubikey-luks)とかを使うのもあるけど今のところは固定文字列で落ち着いてる。


とにかく再現手順がはっきりしたのでupstreamへ。

**"Typing passphrase pretty quickly using Yubikey fails to unlock a LUKS partition"**  
https://gitlab.freedesktop.org/plymouth/plymouth/-/issues/243

そこでありがたいことに[このコミット](https://gitlab.freedesktop.org/plymouth/plymouth/-/commit/6e411c7dcb77b69d7d4e897f6a5676f8bfb1c516)で直った問題じゃない？って教えてくれて、

> That leads to keystrokes getting eaten.
> 
> This commit makes the terminal more careful about setting the
> keyboard mode. It now only changes it when plymouth thinks its
> necessary to do so.

コミットメッセージ見てもキーストロークがどっかいっちゃうってのは納得。

カーネルパラメーターに`plymouth.debug`つけて`/var/log/plymouth-debug.log`を提供したら色々フィードバックをくれて、今のところの理解だと、

1. `splash`をつけなければPlymouthがそもそも使われないので回避される
1. `23.360.11`じゃなくて`24.004.60`なら今回の問題（リグレッション）が修正されているので問題なくなる
1. UbuntuとかDebianがまだinitramfsに入れてない`/usr/share/X11/xkb`を入れるようにすれば、今までのvt consoleじゃなくてxkb consoleを使うようになるからそれでも回避される

ちなみにdownstreamレポートはここ。  
https://bugs.launchpad.net/ubuntu/+source/plymouth/+bug/2051478

それにしてもPlymouth高機能になりすぎててすごい。
