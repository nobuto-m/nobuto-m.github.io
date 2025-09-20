---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "電源ボタンを押したときのシャットダウンを無効化する"
slug: how-to-disable-shutdown-by-pressing-the-power-button
subtitle: ""
summary: ""
authors: []

tags: []
categories: []
keywords: ["Ubuntu"]

reading_time: false
show_related: true
share: false

year: 2025
date: 2025-09-20T16:32:04+09:00
lastmod: 2025-09-20T16:32:04+09:00

featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
image:
  preview_only: true
---

{{< figure src="featured.jpg" caption="電源ボタンが天板の押しやすい位置にある" >}}

PCケースを買い替えた結果、電源ボタンが子どもが押したくなるような絶妙な位置にある。もし押されたとしてもシャットダウンしないようにする。

ヘッドレス（デスクトップ環境無し）で使っている場合のデフォルトでは、電源ボタンが押されると`systemctl poweroff`相当のシャットダウン処理が走ってしまう。`systemd-logind`の設定ファイルで`HandlePowerKey`をデフォルトの`poweroff`から`ignore`（何もしない）に変えるだけ。

```diff
diff --git a/systemd/logind.conf b/systemd/logind.conf
index 53d7563..d624b33 100644
--- a/systemd/logind.conf
+++ b/systemd/logind.conf
@@ -19,17 +19,17 @@
 [Login]
 #NAutoVTs=6
 #ReserveVT=6
 #KillUserProcesses=no
 #KillOnlyUsers=
 #KillExcludeUsers=root
 #InhibitDelayMaxSec=5
 #UserStopDelaySec=10
-#HandlePowerKey=poweroff
+HandlePowerKey=ignore
 #HandlePowerKeyLongPress=ignore
 #HandleRebootKey=reboot
 #HandleRebootKeyLongPress=poweroff
 #HandleSuspendKey=suspend
 #HandleSuspendKeyLongPress=hibernate
 #HandleHibernateKey=hibernate
 #HandleHibernateKeyLongPress=ignore
 #HandleLidSwitch=suspend
```

再起動を待たずに即時反映するために設定を読み込み直す。

```bash
sudo systemctl reload systemd-logind.service
```

すると、電源ボタンが押されたよってログに残るだけでシステムはそのまま動き続けるようになる。

```
Sep 20 01:32:40 darkbox systemd-logind[1227]: Power key pressed short.
```
