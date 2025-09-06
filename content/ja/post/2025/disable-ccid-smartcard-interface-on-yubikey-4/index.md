---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "YubiKey 4のCCIDを無効化してGDM3でSmartcardと認識されないようにする"
slug: disable-ccid-smartcard-interface-on-yubikey-4
subtitle: ""
summary: ""
authors: []

tags: []
categories: []
keywords: []

reading_time: false
show_related: true
share: false

year: 2025
date: 2025-09-06T16:48:11+09:00
lastmod: 2025-09-06T16:48:11+09:00

featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
image:
  preview_only: true
---

家で使ってるYubiKey 4を挿したままUbuntuを起動するとログイン画面（GDM3）でユーザー名を選択する画面が出ずにスマートカードログイン専用のインターフェースに切り替わってしまって、パスワード認証や指紋認証が使えなくなる。YubiKey挿しっぱなしだったわと気づいてUSBポートから抜けばいつものユーザーリストの画面に戻るけどもやや不便な状態だった。ただ初期インストール状態ではそういう風な動きにはならないっぽかったのでちょっと調べて対策した。

そもそもスマートカードをきちんとデスクトップログイン時の認証に使うには、ユーザーとの紐づけだったりそれなりのステップを踏む必要があって（[チュートリアル例](https://ubuntu.com/tutorials/how-to-use-smart-card-authentication-in-ubuntu-desktop)）、なぜスマートカードモードに行くのかよくわからなかった。ただ[バグ報告は存在して](https://bugs.launchpad.net/ubuntu/+source/gdm3/+bug/1933027)、いろいろ見てみると`pcscd`パッケージの有無でスマートカードモードに行くかどうかの違いがあったので、なんでこのパッケージがインストールされたかを見ると一目瞭然。

```bash
$ sudo apt autoremove --purge pcscd
REMOVING:                       
  libccid*  libyubikey-udev*  pcscd*  python3-fido2*  python3-pyscard*  python3-ykman*  yubikey-manager*

Summary:
  Upgrading: 0, Installing: 0, Removing: 7, Not Upgrading: 2
  Freed space: 2,940 kB

Continue? [Y/n] 
```

YubiKeyを設定するために入れているパッケージのせいでスマートカード用のサービスも入る、と。ちなみに以前は[yubikey-personalization-gui](https://launchpad.net/ubuntu/+source/yubikey-personalization-gui)を使ってたけどnobleにはなかったので、[yubikey-manager-qt](https://launchpad.net/ubuntu/+source/yubikey-manager-qt)の方に移行したけど、これも[upstream的には2026年2月でEOL](https://www.yubico.com/support/download/yubikey-manager/)みたい。[結構バシバシEOLにして次はこれ使ってくださいね](https://www.yubico.com/support/terms-conditions/yubico-end-of-life-policy/eol-products/#h-software-products-includes-mobile-applications-client-software-and-client-libraries)、みたいな方針らしい。

で、どうするのがいいかなと考えたけども、一番手っ取り早いのはそもそもYubiKey 4をスマートカードとして使っていないので機能自体を無効化した。

デフォルトで`OTP+FIDO+CCID`となっているのを、

```bash
$ ykman list
YubiKey 4 (4.3.5) [OTP+FIDO+CCID] Serial: NNNNNNN
```

CCID(Chip Card Interface Device)を除いてあげる。

```bash
$ ykman config mode OTP+FIDO
Set mode of YubiKey to OTP+FIDO? [y/N]: y
Mode set! You must remove and re-insert your YubiKey for this change to take effect.
```

```bash
$ ykman info
Device type: YubiKey 4
Serial number: NNNNNNN
Firmware version: 4.3.5
Enabled USB interfaces: OTP, FIDO

Applications
Yubico OTP  	Enabled
FIDO U2F    	Enabled
FIDO2       	Not available
OATH        	Disabled
PIV         	Disabled
OpenPGP     	Disabled
YubiHSM Auth	Not available
```

これで`ykman`（`yubikey-manager`パッケージ）の依存関係として`pcscd`がインストールされていて、さらにYubiKey 4が挿さっていたとしてもGDM3はユーザー名のリストを出すようになった。
