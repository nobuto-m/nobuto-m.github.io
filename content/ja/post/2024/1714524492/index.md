---
# Documentation: https://wowchemy.com/docs/managing-content/

title: ""
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

{{< figure src="featured.png" caption="" >}}


https://pcsupport.lenovo.com/jp/ja/products/laptops-and-netbooks/thinkpad-t-series-laptops/thinkpad-t14-gen-3-type-21cf-21cg/downloads/ds557681-bios-update-utility-bootable-cd-for-windows-10-64-bit-thinkpad-t14-gen-3-type-21cf-21cg-t16-gen-1-type-21ch-21cj-p16s-gen-1type-21ck-21cl


Secure Flash Authentication Failed
Secure Rollback Prevention

https://fwupd.org/lvfs/devices/com.lenovo.ThinkPadR23ET.firmware

https://fwupd.org/downloads/32496bd6343b6077ec308cef1b2a3fdcf212da968a49a66a8f82ede890598401-r23ul65w.cab

> "UpdateError" : "failed to run update on reboot: expected 0.1.41 and got 0.1.49"

```bash
$ fwupdmgr install --allow-older \
    ./32496bd6343b6077ec308cef1b2a3fdcf212da968a49a66a8f82ede890598401-r23ul65w.cab
```

https://fwupd.org/downloads/b7e43b2edb064e9be24fd4fd7ac17c66ba8d65fdc114be4c5645828b6ac225e1-r23ul70w.cab


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

> VERSION INFORMATION
> 
> The following versions of UEFI BIOS and ECP (Embedded Controller Program) have been released to date.
> 
>   Package  (ID)            UEFI BIOS  (BIOS ID)        ECP  (ECP ID)           Rev.       Issue Date
>   ----------------         --------------------        ----------------        ----       -------------
>   R23UJ73WD                 1.49  (R23ET73W)            1.32  (R23HT66W)        01         2024/01/05
>   R23UJ70WD                 1.40  (R23ET70W)            1.30  (R23HT64W)        01         2023/09/07
>   R23UJ66WD                 1.35  (R23ET65W)            1.28  (R23HT62W)        01         2023/07/20
>   R23UJ65WD                 1.35  (R23ET65W)            1.25  (R23HT59W)        01         2023/04/06
>   R23UJ64WD                 1.34  (R23ET64W)            1.25  (R23HT59W)        01         2023/03/06
>   R23UJ62WD                 1.32  (R23ET62W)            1.24  (R23HT58W)        02         2022/12/07
>   R23UJ62WD                 1.32  (R23ET62W)            1.24  (R23HT58W)        01         2022/12/01
>   R23UJ60WD_V2              1.30  (R23ET60W)            1.23  (R23HT57W)        02         2022/11/10
>   R23UJ60WD                 1.30  (R23ET60W)            1.21  (R23HT55W)        01         2022/10/13
>   R23UJ59WD                 1.29  (R23ET59W)            1.21  (R23HT55W)        01         2022/09/22
>   R23UJ47WD                 1.17  (R23ET47W)            1.11  (R23HT45W)        01         2022/06/17


https://download.lenovo.com/pccbbs/mobiles/r23uj73wd.txt

> CHANGES IN THIS RELEASE
> 
> Version 1.49 (UEFI BIOS)
>          1.32 (ECP)
> 
> [Important updates]
> - Notice that BIOS can't be downgraded to older BIOS version after upgrade to r23uj73w(1.49).
> 
> [New functions or enhancements]
> - Enhancement to address security vulnerability, CVE-2023-5058,LEN-123535,LEN-128083,LEN-115697,LEN-123534,LEN-118373,LEN-119523,LEN-123536.
> - Change to permit fan rotation after fan error happen.
> 
> [Problem fixes]
> - Fixed non-bootable issue when Absolute Persistence Module Activation is disabled permanently.
> - Fixed an issue that boot time is long when system is attached with DisplayLink Dock.


https://github.com/fwupd/fwupd/commit/52d6c3cb78ab8ebfd432949995e5d4437569aaa6
