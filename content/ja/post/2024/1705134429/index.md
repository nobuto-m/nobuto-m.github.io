---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "ThinkPad T14 Gen 3 AMDのBIOSを1.49/1.32(R23ET73W)に上げる"
slug: 1705134429
subtitle: ""
summary: ""
authors: []

tags: []
categories: []
keywords: []

reading_time: false
show_related: true
share: false

year: 2024
date: 2024-01-13T17:27:09+09:00
lastmod: 2024-01-13T17:27:09+09:00

featured: false
draft: true

# Featured image
# To use, add an image named "featured.jpg/png" to your page's folder.
image:
  preview_only: true
---


{{< figure src="featured.png" caption="" >}}

https://pcsupport.lenovo.com/jp/ja/products/laptops-and-netbooks/thinkpad-t-series-laptops/thinkpad-t14-gen-3-type-21cf-21cg/downloads/ds557681-bios-update-utility-bootable-cd-for-windows-10-64-bit-thinkpad-t14-gen-3-type-21cf-21cg-t16-gen-1-type-21ch-21cj-p16s-gen-1type-21ck-21cl

```bash
$ fwupdmgr update
...

firmware signature missing or not trusted; set OnlyTrusted=false in
/etc/fwupd/daemon.conf ONLY if you are a firmware developer
```

- https://forums.lenovo.com/t5/Ubuntu/Ubuntu-23-10-21CH004XGE-upgrade-to-r23ul73w-1-49-firmware-signature-missing-or-not-trusted/m-p/5281458
- https://github.com/fwupd/fwupd/issues/6646
- https://github.com/fwupd/fwupd/issues/6647

- https://fwupd.org/lvfs/devices/com.lenovo.ThinkPadR23ET.firmware


```bash
$ isoinfo -i r23uj73wd.iso -d
CD-ROM is in ISO 9660 format
System id: 
Volume id: R23UJ73WD
Volume set id: 
Publisher id: 
Data preparer id: 
Application id: NERO BURNING ROM VER 12,5,5,0
Copyright File id: 
Abstract File id: 
Bibliographic File id: 
Volume set size is: 1
Volume set sequence number is: 1
Logical block size is: 2048
Volume size is: 23734
El Torito VD version 1 found, boot catalog is in sector 20
Joliet with UCS level 3 found
NO Rock Ridge present
Eltorito validation header:
    Hid 1
    Arch 0 (x86)
    ID 'NERO BURNING ROM VER 12'
    Key 55 AA
    Eltorito defaultboot header:
        Bootid 88 (bootable)
        Boot media 4 (Hard Disk Emulation)
        Load segment 7C0
        Sys type 6
        Nsect 1
        Bootoff 1B 27
```

```bash
$ geteltorito -o r23uj73wd.raw r23uj73wd.iso 
Booting catalog starts at sector: 20 
Manufacturer of CD: NERO BURNING ROM VER 12
Image architecture: x86
Boot media type is: harddisk
El Torito image starts at sector 27 and has 94208 sector(s) of 512 Bytes

Image has been written to file "r23uj73wd.raw".
```

```
$ file r23uj73wd.raw 
r23uj73wd.raw: DOS/MBR boot sector; partition 1 : ID=0xb, active, start-CHS (0x0,1,1), end-CHS (0x2d,63,32), startsector 32, 94176 sectors
```

