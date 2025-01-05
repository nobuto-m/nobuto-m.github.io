---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "No, you can't downgrade T14 Gen 3 AMD's UEFI BIOS even when Secure Rollback Prevention is turned off"
slug: no-you-can-t-downgrade-t14-gen-3-amd-s-uefi-bios-even-when-secure-rollback-prevention-is-turned-off
subtitle: ""
summary: ""
authors: []

tags: [planet-ubuntu, "ThinkPad T14 Gen 3 AMD"]
categories: []
keywords: []

reading_time: false
show_related: true

year: 2024
date: 2024-05-03T12:20:51+09:00
lastmod: 2024-05-03T12:20:51+09:00

featured: false
draft: false

translationKey: post/2024/no-you-can-t-downgrade-t14-gen-3-amd-s-uefi-bios-even-when-secure-rollback-prevention-is-turned-off

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
image:
  preview_only: true
---

{{< figure src="featured.jpg" caption="The \"Secure Rollback Prevention\" entry in the UEFI BIOS configuration" >}}

The bottom line is that there is a new configuration called "AMD Secure **Processor** Rollback protection" on recent AMD systems in addition to "Secure Rollback Prevention" (BIOS rollback protection). If it's enabled by a vendor, you cannot downgrade the UEFI BIOS revisions once you install a one with security vulnerability fixes.

https://fwupd.github.io/libfwupdplugin/hsi.html#org.fwupd.hsi.Amd.RollbackProtection
> This feature prevents an attacker from loading an older firmware onto the part after a security vulnerability has been fixed.  
> [...]  
> End users are not able to directly modify rollback protection, this is controlled by the manufacturer.

Previously I installed the revision 1.49 (R23ET73W) but it's gone from [Lenovo's official page](https://pcsupport.lenovo.com/us/en/products/laptops-and-netbooks/thinkpad-t-series-laptops/thinkpad-t14-gen-3-type-21cf-21cg/21cf/21cfcto1ww/downloads/driver-list/component?name=BIOS%2FUEFI) with the notice below. I've been annoyed by a symptom which is likely from a firmware so I wanted to try multiple revisions for bisecting, and also I thought I should downgrade it to the latest official revision as 1.40 (R23ET70W) since the withdrawal clearly indicates that there is something wrong with 1.49.

> This BIOS version R23UJ73W is reported Lenovo cloud not working issue, hence it has been withdrawn from support site.

First, I turned off `Secure Rollback Prevention` and tried downgrading it with `fwupdmgr` like the following. However, it failed to be applied with `Secure Flash Authentication Failed` when rebooted.

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

Next, I tried their ISO image `r23uj70wd.iso`, but no luck with another error.

> Error
> 
> The system program file is not correct for this system.

Also, Windows failed to apply it so I became convinced it was impossible. However, I didn't have a clear idea why at that point and bumped into a handy command in `fwupdmgr`.

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

As you can see, the `BIOS rollback protection` in the HSI-2 section is "Disabled" as intended. But `Processor rollback protection` in HSI-4 is "Enabled". I found a commit suggesting that there was a system with the config disabled and it was able to be enabled when `OS Optimized Defaults` is turned on.

https://github.com/fwupd/fwupd/commit/52d6c3cb78ab8ebfd432949995e5d4437569aaa6
> Update documentation to indicate that loading "OS Optimized Defaults"
> 
> may enable security processor rollback protection on Lenovo systems.

I hoped that `Processor rollback protection` might be disabled by turning off `OS Optimized Defaults` instead.

{{< figure src="os-optimized-defaults.jpg" caption="Tried OS Optimized Defaults turned off but no luck" >}}

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

Some configurations were overridden, but the `Processor rollback protection` stayed the same. It's confirmed that it's really impossible to downgrade the firmware with vulnerability fixes. I learned the hard way that there was a clear difference between "a vendor doesn't support downgrading" and "it can't be downgraded" as per the release notes.

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

I have to wait for a new and better firmware.
