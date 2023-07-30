---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "TailscaleのACLを設定した"
slug: 1690694899
subtitle: ""
summary: ""
authors: []

tags: []
categories: []
keywords: []

reading_time: false
show_related: true
share: false

year: 2023
date: 2023-07-30T14:28:19+09:00
lastmod: 2023-07-30T14:28:19+09:00

featured: false
draft: false

# Featured image
# To use, add an image named "featured.jpg/png" to your page's folder.
image:
  preview_only: true
---

[TailscaleのACL](https://tailscale.com/kb/1018/acls/)は簡単に設定できることは知ってたけど、自分のデバイス間は全許可というデフォルトのポリシーのまま使っていた。

```json
{
  "acls": [
    // Allow all connections.
    // Comment this section out if you want to define specific restrictions.
    {"action": "accept", "src": ["*"], "dst": ["*:*"]},
  ],
}
```

一応手元のデバイスだけは`tailscale up --shields-up`でどこからもアクセスを受け付けないようにしてたし、仮にポートが空いていても認証が必須にはなっていた。ただ、人に[Tailscaleのデバイス共有機能](https://tailscale.com/kb/1084/sharing/)と別途SSHの鍵認証でログインを許可することがあり、ログインするとそのデバイスが紐付いているTailscaleアカウント（私のアカウント）内の全デバイスに（ネットワーク的に）アクセスできてしまう。もちろん信頼できる人にしかログインさせていないけれども、これを機にACLを設定した。特筆することはなくドキュメント通り。

- [Syntax reference](https://tailscale.com/kb/1018/acls/#tailscale-policy-syntax)
- [Tags reference](https://tailscale.com/kb/1068/acl-tags/#defining-a-tag)
- [Tests reference](https://tailscale.com/kb/1018/acls/#tests)
- [Examples](https://tailscale.com/kb/1192/acl-samples/)

まずはデバイスを3つタグにグループ分け。

- `tag:client` - 手元のノートPC
- `tag:server` - 検証用マシンなど
- `tag:scraper` - Prometheusを動かしているマシン

```json
{
  // Define the tags which can be applied to devices and by which users.
  "tagOwners": {
    "tag:client":  ["autogroup:admin"],
    "tag:server":  ["autogroup:admin"],
    "tag:scraper": ["autogroup:admin"],
  },
}
```

ACLは初期ルールをコメントアウトするとデフォルトが全拒否になるので、必要なルールだけ追加。この場合は、手元のノートPCからは全マシンに全許可、Prometheusからは`server`タグが付いたマシンのNode exporter用`9100`ポートのみ許可。`server`タグ用のルールは存在しないのでこのタグのマシンはどこへもアクセスできない。


```json
{
  // Define access control lists for users, groups, autogroups, tags,
  // Tailscale IP addresses, and subnet ranges.
  "acls": [
    // Allow all connections.
    // Comment this section out if you want to define specific restrictions.
    //{"action": "accept", "src": ["*"], "dst": ["*:*"]},
    {"action": "accept", "src": ["tag:client"], "dst": ["*:*"]},
    {"action": "accept", "src": ["tag:scraper"], "dst": ["tag:server:9100"]},
  ],
}
```

{{< figure src="featured.png" caption="`tag:scraper`のプレビュー" >}}

ルール数が最小なので、テストを書くのも簡単。

```json
{
  // Test access rules every time they're saved.
  "tests": [
    {
      // clients can access anywhere
      "src":    "tag:client",
      "accept": ["tag:server:22", "tag:client:22", "tag:scraper:22"],
    },
    {
      // scraper can only access to servers with port 9100
      "src":    "tag:scraper",
      "accept": ["tag:server:9100"],
      "deny":   ["tag:client:22", "tag:scraper:22", "tag:server:22"],
    },
    {
      // servers cannot access anywhere
      "src":  "tag:server",
      "deny": ["tag:client:22", "tag:server:22", "tag:scraper:22"],
    },
  ],
}
```

これでデバイスを共有してログインしてもらったとしても、アカウント内の全デバイスへのネットワークアクセスまで許可することはなくなった。
