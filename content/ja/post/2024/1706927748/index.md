---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "ELECOM_ESD-EMA"
slug: 1706927748
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
date: 2024-02-03T11:35:48+09:00
lastmod: 2024-02-03T11:35:48+09:00

featured: false
draft: true

# Featured image
# To use, add an image named "featured.jpg/png" to your page's folder.
image:
  preview_only: true
---

{{< figure src="featured.png" caption="" >}}

```bash
git clone https://github.com/louwrentius/fio-plot.git

cd fio-plot/
virtualenv venv
source venv/bin/activate

# "rich" package was missed
# https://github.com/louwrentius/fio-plot/commit/d92f942215cb5ff732e9c27343689bdb042a6c07
#pip3 install fio-plot
pip3 install .
```

/dev/disk/by-id doesn't work  
https://github.com/louwrentius/fio-plot/issues/136

```bash
$ systemd-inhibit bench-fio \
    --type device \
    --target "$(readlink -f /dev/disk/by-id/usb-ELECOM_ESD-EMA_13CF00101477-0\:0)" \
    --entire-device \
    --iodepth 1 --numjobs 1 \
    --mode write -b 4M \
    --output usb-ELECOM_ESD-EMA \
    --destructive

                       Bench-fio                       
┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┳━━━━━━━━━━━━━━━━━━━━┓
┃ Setting                        ┃ value              ┃
┡━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━╇━━━━━━━━━━━━━━━━━━━━┩
│ Estimated Duration             │                    │
│ Number of benchmarks           │ 1                  │
│ Test target(s)                 │ /dev/sda           │
│ Target type                    │ device             │
│ I/O Engine                     │ libaio             │
│ Test mode (read/write)         │ write              │
│ Specified test data size       │ 100%               │
│ Block size                     │ 4M                 │
│ IOdepth to be tested           │ 1                  │
│ NumJobs to be tested           │ 1                  │
│ Benchmark loops                │ 1                  │
│ Direct I/O                     │ 1                  │
│ Output folder                  │ usb-ELECOM_ESD-EMA │
│ Benchmark entire device        │ True               │
│ Log interval of perf data (ms) │ 1000               │
│ Invalidate buffer cache        │ 1                  │
│ Allow destructive writes       │ True               │
│ Check remote timeout (s)       │ 2                  │
└────────────────────────────────┴────────────────────┘
/dev/sda ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 100% 0:00:00
```

Trim for the whole block device (without a filesystem)

[ELECOM_ESD-EMA]
```bash
$ sudo blkdiscard -v /dev/disk/by-id/usb-ELECOM_ESD-EMA_13CF00101477-0\:0 
/dev/disk/by-id/usb-ELECOM_ESD-EMA_13CF00101477-0:0: Discarded 256060514304 bytes from the offset 0
```

[Realtek_RTL9210B-CG (USB-NVMe)]
```bash
$ sudo blkdiscard -v /dev/disk/by-id/usb-Realtek_RTL9210B-CG_012345679350-0\:0
blkdiscard: BLKDISCARD: /dev/disk/by-id/usb-Realtek_RTL9210B-CG_012345679350-0:0 ioctl failed: Operation not supported
```

[JetFlash_Transcend_32GB]
```bash
$ sudo blkdiscard -f /dev/disk/by-id/usb-JetFlash_Transcend_32GB_AAOJK4FQJKH0JUP9-0\:0
blkdiscard: Operation forced, data will be lost!
blkdiscard: BLKDISCARD: /dev/disk/by-id/usb-JetFlash_Transcend_32GB_AAOJK4FQJKH0JUP9-0:0 ioctl failed: Operation not supported
```

```bash
$ fio-plot -i \
    */sda/4M/ \
    -g -t bw -r write \
    -T 'Sequential write bandwidth over time' \
    -w 0.5 --xlabel-parent 2
```

## more testing

```
systemd-inhibit bench-fio \
    --type device \
    --target "$(readlink -f /dev/disk/by-id/usb-BUFFALO_SSD-PUTU3C_0040609440108864-0\:0)" \
    --entire-device \
    --iodepth 1 --numjobs 1 \
    --mode write -b 4M \
    --output ../fio-plot_output/usb-BUFFALO_SSD-PUTU3C \
    --destructive
```

```
systemd-inhibit bench-fio \
    --type device \
    --target "$(readlink -f /dev/disk/by-id/usb-ELECOM_ESD-EMA_13CF00101477-0\:0)" \
    --size 128G \
    --runtime 0 \
    --iodepth 1 --numjobs 1 \
    --mode write -b 4M \
    --output ../fio-plot_output/usb-ELECOM_ESD-EMA_128G \
    --destructive
```

```
$ fio-plot -i \
    */sda/4M/ \
    -g -t bw -r write \
    -T 'Sequential write bandwidth over time' \
    --xlabel-depth 2 --xlabel-parent 0 \
    --xlabel-single-column \
    -w 0.5
```
