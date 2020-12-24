---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Knowing what services need restart with “needrestart”"
subtitle: ""
summary: ""
authors: []
tags: [planet-ubuntu]
categories: []
date: 2017-07-04T00:00:00+09:00
lastmod: 2020-12-24T17:51:09+09:00
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: true

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---

With [Livepatch support in Linux kernel](https://www.kernel.org/doc/Documentation/livepatch/livepatch.txt) and [Livepatch service for Ubuntu](https://www.ubuntu.com/server/livepatch), reboots are no longer necessary even when kernel vulnerabilities are found. You can avoid and postpone unplanned reboots to whenever convenient for you, such as scheduled maintenance windows (Well, there are some corner cases which still require reboots though).

What about services? After applying security updates of libraries, some services still grab old libraries which may have vulnerabilities. You can list up those processes with “lsof”.

```bash
$ sudo lsof / | egrep -w 'DEL|deleted'
...
apache2 2296 root DEL REG 252,0 260616 /lib/x86_64-linux-gnu/libcrypto.so.1.0.0
apache2 2296 root DEL REG 252,0 260711 /lib/x86_64-linux-gnu/libssl.so.1.0.0
apache2 2299 www-data DEL REG 252,0 260616 /lib/x86_64-linux-gnu/libcrypto.so.1.0.0
apache2 2299 www-data DEL REG 252,0 260711 /lib/x86_64-linux-gnu/libssl.so.1.0.0
...
```

With “lsof”, you have to determine which process belongs to which systemd unit and manually restart those units. There is a helper script called “needrestart”. It is inspired by checkrestart from the debian-goodies package according to their doc. It shows a handy dialogue at the end of `apt upgrade` and prompts service restarts.

![needrestart prompt](needrestart.png)

To use “needrestart”, you just need to install a package with the same name.

```bash
$ sudo apt install needrestart
```

You can also change the default behavior by editing `/etc/needrestart/needrestart.conf`. For example, you can enable automatic restart without asking.

```
# Restart services (l)ist only, (i)nteractive or (a)utomatically.
#$nrconf{restart} = 'i';
```

Furthermore, it has “nagios” plugin mode(`-p`) so that it can be integrated with monitoring systems. With this mode, you might want to skip an obsolete kernel check by adding `-l` flag if kernel patching is managed by Livepatch.

```bash
$ sudo needrestart -p -l
CRIT — Services: 4 (!), Containers: none, Sessions: 1 (!)|Services=4;;0;0 Containers=0;;0;0 Sessions=1;0;;0
```

Here is an example of Nagios status output.

![Nagios alert](nagios.png)
