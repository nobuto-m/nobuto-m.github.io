---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Hugo snapが fatal: unable to find remote helper for 'https' のエラーを出すようになった"
slug: 1688220202
subtitle: ""
summary: ""
authors: []

tags: []
categories: []
keywords: ["hugo"]

reading_time: false
show_related: true
share: false

year: 2023
date: 2023-07-01T23:03:22+09:00
lastmod: 2023-07-01T23:03:22+09:00

featured: false
draft: false

# Featured image
# To use, add an image named "featured.jpg/png" to your page's folder.
image:
  preview_only: true
---

一個新規記事ファイルを作って、Hugoのプレビューを開こうとしたらエラーになる。

```bash
$ hugo
go: github.com/wowchemy/wowchemy-hugo-themes/modules/wowchemy-plugin-netlify@v1.0.0: invalid version: git ls-remote -q origin in /tmp/hugo_cache_nobuto/modules/filecache/modules/pkg/mod/cache/vcs/0c88995f39c54fe356dd74460b6359b56b0fb376c52289c9656c86270b2c006d: exit status 128:
	fatal: unable to find remote helper for 'https'
go: github.com/wowchemy/wowchemy-hugo-themes/modules/wowchemy-plugin-netlify-cms@v1.0.0: invalid version: git ls-remote -q origin in /tmp/hugo_cache_nobuto/modules/filecache/modules/pkg/mod/cache/vcs/0c88995f39c54fe356dd74460b6359b56b0fb376c52289c9656c86270b2c006d: exit status 128:
	fatal: unable to find remote helper for 'https'
go: github.com/wowchemy/wowchemy-hugo-themes/modules/wowchemy-plugin-reveal@v1.0.0: invalid version: git ls-remote -q origin in /tmp/hugo_cache_nobuto/modules/filecache/modules/pkg/mod/cache/vcs/0c88995f39c54fe356dd74460b6359b56b0fb376c52289c9656c86270b2c006d: exit status 128:
	fatal: unable to find remote helper for 'https'
go: github.com/wowchemy/wowchemy-hugo-themes/modules/wowchemy/v5@v5.7.1-0.20230420205746-951c7b6f709d: invalid version: git ls-remote -q origin in /tmp/hugo_cache_nobuto/modules/filecache/modules/pkg/mod/cache/vcs/0c88995f39c54fe356dd74460b6359b56b0fb376c52289c9656c86270b2c006d: exit status 128:
	fatal: unable to find remote helper for 'https'
hugo: collected modules in 897 ms
Total in 906 ms
Error: failed to load modules: failed to download modules: failed to execute 'go [mod download]': failed to execute binary "go" with args [mod download]: go: github.com/wowchemy/wowchemy-hugo-themes/modules/wowchemy-plugin-netlify@v1.0.0: invalid version: git ls-remote -q origin in /tmp/hugo_cache_nobuto/modules/filecache/modules/pkg/mod/cache/vcs/0c88995f39c54fe356dd74460b6359b56b0fb376c52289c9656c86270b2c006d: exit status 128:
	fatal: unable to find remote helper for 'https'
go: github.com/wowchemy/wowchemy-hugo-themes/modules/wowchemy-plugin-netlify-cms@v1.0.0: invalid version: git ls-remote -q origin in /tmp/hugo_cache_nobuto/modules/filecache/modules/pkg/mod/cache/vcs/0c88995f39c54fe356dd74460b6359b56b0fb376c52289c9656c86270b2c006d: exit status 128:
	fatal: unable to find remote helper for 'https'
go: github.com/wowchemy/wowchemy-hugo-themes/modules/wowchemy-plugin-reveal@v1.0.0: invalid version: git ls-remote -q origin in /tmp/hugo_cache_nobuto/modules/filecache/modules/pkg/mod/cache/vcs/0c88995f39c54fe356dd74460b6359b56b0fb376c52289c9656c86270b2c006d: exit status 128:
	fatal: unable to find remote helper for 'https'
go: github.com/wowchemy/wowchemy-hugo-themes/modules/wowchemy/v5@v5.7.1-0.20230420205746-951c7b6f709d: invalid version: git ls-remote -q origin in /tmp/hugo_cache_nobuto/modules/filecache/modules/pkg/mod/cache/vcs/0c88995f39c54fe356dd74460b6359b56b0fb376c52289c9656c86270b2c006d: exit status 128:
	fatal: unable to find remote helper for 'https'
 *errors.errorString
```

snap版特有の問題っぽかったので、一応Hugoプロジェクトにバグ報告。

https://github.com/gohugoio/hugo/issues/11195

なぜsnap版なのにHugoプロジェクトかというと、[snapcraft.yaml](https://github.com/gohugoio/hugo/blob/master/snap/snapcraft.yaml)はHugoプロジェクト側でメンテされていて、これはこれですごくありがたい。

現状の回避策は、こないだまで動いていたrevisionを指定してインストールする形。

```bash
$ sudo snap refresh --channel extended/stable --revision 16307 hugo
```

~~パッと見はなんで名前解決がブロックされるのかよくわからず~~。`/snap/hugo/{16307,16457}`を比べてもバイナリ以外大きな差分はないし、リビジョンごとに生成されるAppArmorのプロファイルも特に差分は見えない。

追記: AppArmor上は新しいリビジョンでだけ2つ追加でブロックされたログが出るので名前解決かと思ったけど、名前解決まではできてるみたい。
```bash
[pid 95517] 15:50:23.307119 openat(AT_FDCWD, "/snap/core20/current/lib/x86_64-linux-gnu/libresolv.so.2", O_RDONLY|O_CLOEXEC) = -1 EACCES (Permission denied)

[pid 95517] 15:50:23.310260 openat(AT_FDCWD, "/snap/core20/current/lib/x86_64-linux-gnu/libnss_dns.so.2", O_RDONLY|O_CLOEXEC) = -1 EACCES (Permission denied)

[pid 95517] 15:50:23.311317 connect(3, {sa_family=AF_INET, sin_port=htons(53), sin_addr=inet_addr("127.0.0.53")}, 16) = 0

[pid 95517] 15:50:23.313023 connect(3, {sa_family=AF_INET, sin_port=htons(443), sin_addr=inet_addr("20.27.177.113")}, 16) = -1 EINPROGRESS (Operation now in progress)
```
