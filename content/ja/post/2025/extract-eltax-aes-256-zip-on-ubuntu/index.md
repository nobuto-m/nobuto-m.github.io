---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Ubuntu上でeLTAX発行の住民税特別徴収税額通知書ZIPファイルを解凍する"
slug: extract-eltax-aes-256-zip-on-ubuntu
subtitle: ""
summary: ""
authors: []

tags: []
categories: []
keywords: ['Ubuntu']

reading_time: false
show_related: true
share: false

year: 2025
date: 2025-06-14T21:37:52+09:00
lastmod: 2025-06-14T21:37:52+09:00

featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
image:
  preview_only: true
---

**結論: ダブルクリックするだけ**

なのだけど、こういった作業は年に一回しか発生しなくて、今回もエラーメッセージで検索するはめになったのでメモ。

{{< figure src="featured.png" caption="勤め先から送られてくるパスワード確認方法のご案内" >}}

住民税決定通知書がいつからか電子化されていて（2024年から？）、

1. パスワード確認方法の通知書を受け取る
2. そこに記載されているeLTAX特徴税通パスワード確認サイトURLにアクセス（`https://www.psw.eltax.lta.go.jp/tzspwd.<TOKEN>`の形式）
3. パスワードを表示させる
4. そのパスワードを使って別途送られたZIPファイルを解凍

というPPAP方式の進化版みたいな感じ。

説明サイトにも書かれているように、このZIPファイルがOS標準では開けない場合がある。

https://www.kakunin.eltax.lta.go.jp/first
> 本通知書には機微な情報が含まれているため、情報セキュリティの観点から暗号化方式にはAES-256を採用しております。
> ZIP解凍ソフトはAES-256に対応している必要があります。
> 動作確認済のソフトウェアはマニュアルをご確認ください。
> 
>   ※Windows標準のエクスプローラーでは解凍できません。

Ubuntu上でも例えば`unzip`では対応していないので開けない。

```bash
$ file '[...]特徴税額通知(通知書)[...].zip'
[...]特徴税額通知(通知書)[...].zip: Zip archive data,
at least v5.1 to extract, compression method=AES Encrypted


$ unzip '[...]特徴税額通知(通知書)[...].zip'
Archive:  [...]特徴税額通知(通知書)[...].zip
   skipping: [...]特徴税額通知(通知書).pdf  need PK compat. v5.1 (can do v4.6)
```

標準インストールでは入っていない`7zip`パッケージ内の`7z`とか、`libarchive-tools`パッケージ内の`bsdtar`/`bsdunzip`とかであれば問題ない。

```bash
$ 7z e '[...]特徴税額通知[...].zip'

7-Zip 24.08 (x64) : Copyright (c) 1999-2024 Igor Pavlov : 2024-08-11
 64-bit locale=en_US.UTF-8 Threads:16 OPEN_MAX:1024

Scanning the drive for archives:
1 file, 292259 bytes (286 KiB)

Extracting archive: [...]特徴税額通知(通知書)[...].zip
--
Path = [...]特徴税額通知(通知書)[...].zip
Type = zip
Physical Size = 292259

    
Enter password (will not be echoed):
Everything is Ok

Size:       295082
Compressed: 292259
```

```bash
$ bsdunzip '[...]特徴税額通知(通知書)[...].zip'
Archive:  [...]特徴税額通知(通知書)[...].zip
 extracting: [...]特徴税額通知(通知書).pdf |
Enter password: 
```

とここまで書いて、いや`libarchive`は標準でインストールされていてデフォルトのNautilusファイルマネージャで使われているはずでは、とZIPファイルをダブルクリックしたら問題なく解凍できた。いったいなぜこんな遠回りを。ファイル名の文字コードはUTF-8と明記されているので、文字コードが指定できなくて文字化けする心配もない。
