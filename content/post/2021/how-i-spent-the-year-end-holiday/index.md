---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "How I spent year-end holidays"
slug: how-i-spent-year-end-holidays
subtitle: ""
summary: ""
authors: []

tags: []
categories: [Miscellaneous]

year: 2021
date: 2021-01-06T10:09:02+09:00
lastmod: 2021-01-06T10:09:02+09:00

featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
image:
  preview_only: true
---

I intentionally spent the holidays by keeping myself busy with some technical stuff because I knew I would regret it if nothing had been done since there was no trip planned during the period. Those were not that creative, but to follow some tutorials, for example, where I didn't have experience with or to address some day-to-day pains.

## Immediate wins

### Tailscale

After moving to a new place last year, I lost the ability to assign a global IP address to my router. And it became impossible to access my testbed machine from outside with direct SSH forwarded by a router. There are multiple workarounds, including setting up a VPN like OpenVPN, Wireguard, etc., with a server on the Internet as a hub in the middle, or using a reverse tunnel. But it's still nice if I can have some sort of direct and peer-to-peer connection.

I tried both [ZeroTier](https://www.zerotier.com/) and [Tailscale](https://tailscale.com/), which offer NAT traversal with VPN. I chose Tailscale in the end for simplicity, and I like their stance trying to explain [everything happening inside](https://tailscale.com/blog/how-tailscale-works/) and [related technologies](https://tailscale.com/blog/how-nat-traversal-works/).

Not every component of it is open-source, but it's convenient since everything is well managed. There were some other options to set up an [equivalent](https://www.jordanwhited.com/posts/wireguard-endpoint-discovery-nat-traversal/) or [compatible](https://github.com/juanfont/headscale) server though.

{{< figure src="featured.png" title="Tailscale eases a headache of NAT" >}}

Not only for the remote access from my laptop on the go to the testbed machine (the arrow "A"), I enabled Tailscale for accessing my Grafana dashboard running inside a cloud instance ("B") which is not necessary to be exposed to the Internet. I could just use SSH port forwarding or `sshuttle` but having a Wireguard-based connection without having a VPN endpoint exposed is like magic. As a bonus, I can reliably access a Raspberry-Pi running somewhere behind three NAT instances ("C"). I no longer have to worry about the SSH reverse tunnel for it and its retry logic I set up some time ago.

### Excalidraw

As you can see above, the diagram was made with [Excalidraw](https://excalidraw.com/). It's just amazing as turning my ugly diagram into a beautiful one. It's getting [more and more features](https://blog.excalidraw.com/one-year-of-excalidraw/) these days. How wonderful it is.

I've been using [ASCIIFlow](http://asciiflow.com/) for some time since it was all text-based, which is convenient to have a diagram in Markdown text. However, I would use Excalidraw for most of the cases from now on for personal use.


## Random stuff

### Setting up this blog

I had some blogs so far on Blogger, Tumblr, and Medium, etc. But I wanted to have another one since Medium is getting annoying, especially when reading something on mobile. I believe this is the 5th one, and I'm using Hugo I've been interested in for some time and found a lovely template, [Wowchemy](https://wowchemy.com/). I published [the first post in two years]({{< ref "/post/2020/shortcut-key-to-mute-unmute-yourself-in-zoom-or-google-meet/index.md" >}}), and I tried to fix the template's corner-case issues. Then the author updated those on top of my [some](https://github.com/wowchemy/wowchemy-hugo-modules/pull/2056) pull [requests](https://github.com/wowchemy/wowchemy-hugo-modules/pull/2054). Outstanding customizations are:
- [showing both the original published date and the modified/updated date](https://github.com/wowchemy/wowchemy-hugo-modules/issues/2064)
- yearly archive of the posts [by defining a new taxonomy](https://discourse.gohugo.io/t/how-to-generate-chronological-blog-archives-in-hugo/13491/6) as "year"

What took time to figure out was about the snap package of Hugo. It's currently under strict confinement, but [it doesn't work with a template like Wowchemy with Go modules](https://github.com/gohugoio/hugoDocs/issues/1222). There is [a request to use classic confinement](https://forum.snapcraft.io/t/request-for-classic-confinement-for-hugo-snap/19892), but for now, I have `alias hugo=/snap/hugo/current/bin/hugo` as a workaround.

I also tried [Gihub Action for Hugo](https://github.com/marketplace/actions/hugo-setup) to automate the builds on the Github side. In the end, I prefer building it locally since it's just a matter of seconds. I've also learned that `prefers-color-scheme` in CSS [doesn't work with Chrome/Chromium on Linux](https://bugs.chromium.org/p/chromium/issues/detail?id=998903) while working with Firefox.


### Thinkpad battery

Previously, the charge percentage dropped suddenly from 30-40% to nearly zero. I thought the battery was broken. But it was clearly written as [a known issue](https://linrunner.de/tlp/faq/battery.html#erratic-battery-behavior-on-thinkpad-t420-s-t520-w520-x220-and-all-later-models) in the TLP documentation so it became sane after setting it back to `STOP_CHARGE_THRESH_BAT0=100`.

{{< figure src="thinkpad-battery.png" title="" >}}

### Wake-on-LAN

I haven't used Wake-on-LAN for some time because I relied on a smart plug to power on my testbed machine remotely. Nowadays, I'm on the same network with the machine as I have fewer chances to go out. So waking it up with a terminal command is handy without opening a mobile app. Enabling WOL wasn't as straightforward as it sounded. Googling told me that I had to specify the MAC address of the NIC explicitly for Netplan (indirectly for systemd-networkd). I wanted to know it earlier, so I opened [a pull request to add the caveat in the doc](https://github.com/CanonicalLtd/netplan/pull/180).

### Squid shutdown took time

I knew my laptop was taking time to shut down due to a LXD container running squid-deb-proxy. I thought it was inevitable to flush the content on memory to the disk, but [it](http://www.squid-cache.org/Doc/config/shutdown_lifetime/) was actually [configurable](http://squid-web-proxy-cache.1019090.n4.nabble.com/Question-about-shutdown-lifetime-behavior-tp4685365p4685366.html). Once setting `shutdown_lifetime 3 seconds`, the shutdown starts almost immediately now.

### Let's encrypt wildcard certs and DNS-01

I wanted to have a proper TLS certificate for services running behind a firewall and not exposed to the Internet. So far, I only used HTTP-01 challenges for my public web servers. Now that wildcard certs with DNS-01 is available with Let's encrypt, I migrated the nameservers for one of my domains to Cloudflare, which is in [the list of DNS-01 capable providers with certbot](https://community.letsencrypt.org/t/dns-providers-who-easily-integrate-with-lets-encrypt-dns-validation/86438).

One caveat was that API-token based authentication wasn't available with the version of `python3-cloudflare` in Ubuntu 20.04 LTS.

https://certbot-dns-cloudflare.readthedocs.io/en/stable/
> Using Cloudflare Tokens also requires at least version 2.3.1 of the cloudflare python module.

I tried to backport a newer one to 20.04, but it wasn't straightforward. In the end, I've migrated from the deb based certbot to the snap one as [the upstream recommends it](https://github.com/certbot/website/pull/613). It's basically with the three commands.

```bash
$ sudo snap install certbot --classic
$ sudo snap set certbot trust-plugin-with-root=ok
$ sudo snap install certbot-dns-cloudflare
```

I knew how to redirect access of port 80 with HTTP to 443 with HTTPS. I've learned that I can also redirect HTTP access to port 3000 of Grafana to HTTPS with the same port with the following [expression](https://nginx.org/en/docs/http/ngx_http_ssl_module.html#errors), which is neat.

```
error_page 497 =301 https://$host:$server_port$request_uri;
```


### Grafana + Prometheus + node-exporter

I don't need to monitor my personal-use servers closely, but I set up a standard set of Prometheus and Node exporter for starters. Nothing is interesting there, but I used a Let's encrypt cert with DNS-01 as the Grafana's hostname is not associated with a global IP address at all.

I have [a reverse proxy using Nginx](https://grafana.com/tutorials/run-grafana-behind-a-proxy/#2) not exposed to the Internet either, but once I get certs with DNS-01, I can use HTTPS properly.

```bash
$ sudo certbot certonly \
    -i nginx \
    --dns-cloudflare \
    --dns-cloudflare-credentials ~/.secrets/certbot/cloudflare.ini \
    --cert-name grafana-wildcard \
    -d '<DOMAIN (e.g., *.t.example.com)>'
```

```bash
$ curl -sv -o/dev/null https://grafana.t.example.com:3000/
*   Trying 10.0.0.100):3000...
* TCP_NODELAY set
* Connected to grafana.t.example.com (10.0.0.100) port 3000 (#0)
...
* Server certificate:
*  subject: CN=*.t.example.com
*  start date: Jan  5 06:24:57 2021 GMT
*  expire date: Apr  5 06:24:57 2021 GMT
*  subjectAltName: host "grafana.t.example.com" matched cert's "*.t.example.com"
*  issuer: C=US; O=Let's Encrypt; CN=R3
*  SSL certificate verify ok.
...
```

{{< figure src="grafana.png" title="[Prometheus Node Exporter Full](https://grafana.com/grafana/dashboards/1860) dashboard" >}}


## Closing

It was somehwat productive to spend the holidays in this way. I replaced Ubuntu 18.04 LTS with 20.04 LTS for the testbed machine, but didn't have time to upgrade my laptop from 20.04 to 20.10 or discontinue one 16.04 installation of a cloud instance in favor of a new instance with 20.04 already there.
