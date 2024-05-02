---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "T14 Gen 3 AMDではSecure Rollback Preventionを無効化してもUEFI BIOSはダウングレードできない"
slug: 1714524492
subtitle: ""
summary: ""
authors: []

tags: []
categories: []
keywords: ["T14"]

reading_time: false
show_related: true
share: false

year: 2024
date: 2024-05-01T09:48:12+09:00
lastmod: 2024-05-01T09:48:12+09:00

featured: false
draft: true

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

[以前BIOSバージョンを1.49に上げた]({{< ref "../1705134429/index.md" >}})ものの、この`1.49(R23ET73W)`が[公式ページ](https://pcsupport.lenovo.com/jp/ja/products/laptops-and-netbooks/thinkpad-t-series-laptops/thinkpad-t14-gen-3-type-21cf-21cg/21cf/21cfcto1ww/downloads/driver-list/component?name=BIOS%2FUEFI)から取り下げられてしまっていて何かしらの不具合があるはずなので現時点で最新である`1.40(R23ET70W)`に戻しておきたかったのと、ファームウェアが原因なんじゃないかと思われる症状に悩まされているので色々なバージョンに戻ってテストしたかったので試行錯誤するはめに。

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








{{< figure src="os-optimized-defaults.jpg" caption="" >}}

https://github.com/fwupd/fwupd/commit/52d6c3cb78ab8ebfd432949995e5d4437569aaa6
> Update documentation to indicate that loading "OS Optimized Defaults"
> 
> may enable security processor rollback protection on Lenovo systems.

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
✘ CET OS Support:                Not supported
✘ Linux swap:                    Unencrypted
✘ Linux kernel:                  Tainted
```

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

HSI-2
✔ SPI write protection:          Enabled
✔ IOMMU:                         Enabled
✔ Platform debugging:            Locked
✔ TPM PCR0 reconstruction:       Valid
✘ BIOS rollback protection:      Disabled

HSI-3
✔ SPI replay protection:         Enabled
✔ CET Platform:                  Supported
✔ Suspend-to-idle:               Enabled
✔ Suspend-to-ram:                Disabled
✘ Pre-boot DMA protection:       Disabled

HSI-4
✔ Processor rollback protection: Enabled
✔ SMAP:                          Enabled
✘ Encrypted RAM:                 Not supported

Runtime Suffix -!
✔ fwupd plugins:                 Untainted
✘ CET OS Support:                Not supported
✘ Linux kernel lockdown:         Disabled
✘ Linux swap:                    Unencrypted
✘ Linux kernel:                  Tainted
✘ UEFI secure boot:              Disabled
```


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
