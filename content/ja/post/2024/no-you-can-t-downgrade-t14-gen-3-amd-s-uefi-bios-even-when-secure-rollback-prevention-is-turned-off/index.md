---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "T14 Gen 3 AMDではSecure Rollback Preventionを無効化してもUEFI BIOSはダウングレードできない"
slug: no-you-can-t-downgrade-t14-gen-3-amd-s-uefi-bios-even-when-secure-rollback-prevention-is-turned-off
subtitle: ""
summary: ""
authors: []

aliases:
- 1714524492

tags: []
categories: []
keywords: ["T14"]

reading_time: false
show_related: true
share: false

year: 2024
date: 2024-05-02T12:45:39+09:00
lastmod: 2024-05-02T12:45:39+09:00

featured: false
draft: false

translationKey: post/2024/no-you-can-t-downgrade-t14-gen-3-amd-s-uefi-bios-even-when-secure-rollback-prevention-is-turned-off

# Featured image
# To use, add an image named "featured.jpg/png" to your page's folder.
image:
  preview_only: true
---

{{< figure src="featured.jpg" caption="UEFI BIOS上の\"Secure Rollback Prevention\"の項目" >}}

結論として、最近のAMDシステムでは "Secure Rollback Prevention" (BIOS rollback protection)とは別に "AMD Secure **Processor** Rollback protection" があり、これがベンダー側で有効にされている場合は一旦脆弱性対応を含むUEFI BIOSバージョンに上げてしまうとダウングレード（ロールバック）できなくなる。

https://fwupd.github.io/libfwupdplugin/hsi.html#org.fwupd.hsi.Amd.RollbackProtection
> This feature prevents an attacker from loading an older firmware onto the part after a security vulnerability has been fixed.  
> [...]  
> End users are not able to directly modify rollback protection, this is controlled by the manufacturer.

[以前BIOSバージョンを1.49に上げた]({{< ref "../upgrade-bios-of-thinkpad-t14-gen3-amd-to-r23et73w/index.md" >}})ものの、この`1.49(R23ET73W)`が[公式ページ](https://pcsupport.lenovo.com/jp/ja/products/laptops-and-netbooks/thinkpad-t-series-laptops/thinkpad-t14-gen-3-type-21cf-21cg/21cf/21cfcto1ww/downloads/driver-list/component?name=BIOS%2FUEFI)から取り下げられてしまっている。何かしらの不具合があったということなので現時点で最新である`1.40(R23ET70W)`に戻しておきたかったのと、ファームウェアが原因なんじゃないかと思われる症状に悩まされているので色々なバージョンに戻ってテストしたかったので試行錯誤するはめに。

まず`Secure Rollback Prevention`をオフにしてから、次のように`fwupdmgr`でダウングレードを選択するも再起動時に`Secure Flash Authentication Failed`で適用されず。

```bash
$ fwupdmgr downgrade
0.	Cancel
1.	b0fb0282929536060857f3bd5f80b319233340fd (Battery)
2.	6fd62cb954242863ea4a184c560eebd729c76101 (Embedded Controller)
3.	0d5d05911800242bb1f35287012cdcbd9b381148 (Prometheus)
4.	3743975ad7f64f8d6575a9ae49fb3a8856fe186f (SKHynix HFS256GDE9X081N)
5.	d77c38c163257a2c2b0c0b921b185f481d9c1e0c (System Firmware)
6.	6df01b2df47b1b08190f1acac54486deb0b4c645 (TPM)
7.	362301da643102b9f38477387e2193e57abaa590 (UEFI dbx)
Choose device [0-7]: 5
0.	Cancel
1.	0.1.46
2.	0.1.41
3.	0.1.38
4.	0.1.36
5.	0.1.23
Choose release [0-5]: 
```

次に`r23uj70wd.iso`をダウンロードしてきて起動するも別のエラーで適用できず。

> Error
> 
> The system program file is not correct for this system.

仕方なくWindowsからも適用を試みるがこれもダメ。このあたりで本当に無理な感じがしてきて、でもなんでダメなのかがよくわからず。そして`fwupdmgr`の便利コマンドを知る。

```bash
$ fwupdmgr security
Host Security ID: HSI:1! (v1.9.16)

HSI-1
✔ BIOS firmware updates:         Enabled
✔ Fused platform:                Locked
✔ Supported CPU:                 Valid
✔ TPM empty PCRs:                Valid
✔ TPM v2.0:                      Found
✔ UEFI bootservice variables:    Locked
✔ UEFI platform key:             Valid
✔ UEFI secure boot:              Enabled

HSI-2
✔ SPI write protection:          Enabled
✔ IOMMU:                         Enabled
✔ Platform debugging:            Locked
✔ TPM PCR0 reconstruction:       Valid
✘ BIOS rollback protection:      Disabled

HSI-3
✔ SPI replay protection:         Enabled
✔ CET Platform:                  Supported
✔ Pre-boot DMA protection:       Enabled
✔ Suspend-to-idle:               Enabled
✔ Suspend-to-ram:                Disabled

HSI-4
✔ Processor rollback protection: Enabled
✔ Encrypted RAM:                 Encrypted
✔ SMAP:                          Enabled

Runtime Suffix -!
✔ fwupd plugins:                 Untainted
✔ Linux kernel lockdown:         Enabled
✔ Linux kernel:                  Untainted
✘ CET OS Support:                Not supported
✘ Linux swap:                    Unencrypted

This system has HSI runtime issues.
 » https://fwupd.github.io/hsi.html#hsi-runtime-suffix

Host Security Events
  2024-05-01 15:06:29:  ✘ BIOS rollback protection changed: Enabled → Disabled
```

`HSI-2`の中で`BIOS rollback protection`は意図通りDisabledになっているものの、`HSI-4`セクションの`Processor rollback protection`はEnabledになっている。色々見てると、ここがDisabledのまま出荷されて、`OS Optimized Defaults`を読み込むとEnabledになるものもあるという記述を見つける。

https://github.com/fwupd/fwupd/commit/52d6c3cb78ab8ebfd432949995e5d4437569aaa6
> Update documentation to indicate that loading "OS Optimized Defaults"
> 
> may enable security processor rollback protection on Lenovo systems.

`OS Optimized Defaults`をオフにしたらもしかしたら`Processor rollback protection`もオフになってくれるかも、と試す。

{{< figure src="os-optimized-defaults.jpg" caption="OS Optimized Defaultsをオフにしてみるも変化なし" >}}

```bash
$ fwupdmgr security
Host Security ID: HSI:1! (v1.9.16)

...

✘ BIOS rollback protection:      Disabled

...

HSI-4
✔ Processor rollback protection: Enabled

...

Host Security Events
  2024-05-02 03:24:45:  ✘ Kernel lockdown disabled
  2024-05-02 03:24:45:  ✘ Secure Boot disabled
  2024-05-02 03:24:45:  ✘ Pre-boot DMA protection is disabled
  2024-05-02 03:24:45:  ✘ Encrypted RAM changed: Encrypted → Not supported
```

いくつかの項目が無効化されるけれども`Processor rollback protection`は変わらず、脆弱性対応が含まれていると本当にダウングレードできない。リリースノートにもしっかり書いてあるけど、よくある「ダウングレードはサポートされていないよ」じゃなくて「できないよ」の意味を身を持って理解した。

https://download.lenovo.com/pccbbs/mobiles/r23uj73wd.txt
> CHANGES IN THIS RELEASE
> 
> Version 1.49 (UEFI BIOS)
>         1.32 (ECP)
> 
> [Important updates]
> - Notice that BIOS can't be downgraded to older BIOS version after upgrade to r23uj73w(1.49).
> 
> [New functions or enhancements]
> - Enhancement to address security vulnerability, CVE-2023-5058,LEN-123535,LEN-128083,LEN-115697,LEN-123534,LEN-118373,LEN-119523,LEN-123536.
> - Change to permit fan rotation after fan error happen.


より完璧なファームウェアアップデートが来るまでこのまま待ちます。
