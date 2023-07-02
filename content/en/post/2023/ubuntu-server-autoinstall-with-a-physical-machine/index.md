---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Ubuntu Server autoinstall with a physical machine"
slug: ubuntu-server-autoinstall-with-a-physical-machine
subtitle: ""
summary: ""
authors: []

tags: [planet-ubuntu]
categories: [Ubuntu]
keywords: []

reading_time: false
show_related: true

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

There is a great set of articles in the Ubuntu Server guide about how to achieve an unattended installation of Ubuntu Server:

- [Automated Server installation](https://ubuntu.com/server/docs/install/autoinstall)
- [Automated Server install quickstart](https://ubuntu.com/server/docs/install/autoinstall-quickstart)
- [Automated Server installer config file reference](https://ubuntu.com/server/docs/install/autoinstall-reference)

And I followed those to test my minimal autoinstall file as follows.

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

It works flawlessly with the `kvm` command with:
```bash
-append 'autoinstall ds=nocloud-net;s=http://_gateway:3003/'
```
as per the document. However, when it comes to a physical machine installation with the vanilla 22.04 LTS ISO, the installer just stopped at the initial language selection step.

In the end, it was a subtle difference. [Without escaping the semicolon](https://www.gnu.org/software/grub/manual/grub/grub.html#Simple-commands), the URL to the autoinstall file is ignored. One missing backslash cost me more than an hour. I [provided feedback to the document](https://discourse.ubuntu.com/t/automated-server-install-quickstart/16614/50) and I hope it's a time saver for somebody.

{{< figure src="featured.png" caption="The semicolon must be escaped in GRUB" >}}
