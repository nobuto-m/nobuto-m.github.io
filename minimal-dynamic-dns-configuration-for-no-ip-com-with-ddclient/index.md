---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Minimal Dynamic DNS configuration for No-IP.com with ddclient"
subtitle: ""
summary: ""
authors: []
tags: [planet-ubuntu]
categories: []
date: 2018-07-21T00:00:00+09:00
lastmod: 2020-12-24T22:39:16+09:00
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---

{{% callout note %}}
EDIT: I have migrated one of my domains to [Google Domains](https://domains.google/) so I'm using [`protocol=googledomains`](https://support.google.com/domains/answer/6147083) these days.
{{% /callout %}}

When I searched for a way to configure ddclient for [No-IP.com](https://www.noip.com/), some pages mention `protocol=dyndns2` with a custom URL. But actually, ddclient supports `protocol=noip` out of the box, so minimal steps would be something like:

```bash
$ sudo apt install ddclient

$ cat <<EOF | sudo tee /etc/ddclient.conf
use=web
ssl=yes

protocol=noip
login=<USERNAME>
password=<PASSWORD>
<YOUR_HOSTNAME>
EOF
```

And here we go.

```bash
$ echo 'run_daemon="true"' | sudo tee -a /etc/default/ddclient
## ^^^ this step may not be necessary any longer

$ sudo service ddclient restart

$ journalctl -u ddclient.service
...
systemd[1]: Started LSB: Update dynamic domain name service entries.
ddclient[24631]: SUCCESS:  updating MY_HOST.redirectme.net: good: IP address set to 118.X.Y.Z
```

For more details, please refer to `ddclient -help`.
