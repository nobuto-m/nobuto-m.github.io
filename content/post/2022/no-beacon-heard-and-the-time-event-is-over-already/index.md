---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "No beacon heard and the time event is over already"
slug: no-beacon-heard-and-the-time-event-is-over-already
subtitle: ""
summary: ""
authors: []

tags: []
categories: []

year: 2022
date: 2022-08-21T14:20:36+09:00
lastmod: 2022-08-21T14:20:36+09:00

featured: false
draft: true

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
image:
  preview_only: true
---

{{< figure src="featured.png" title="" >}}


- no Wi-Fi 6 (11ax) capability on the card so it's about 11ac or 11n
- WPA2 with or without 802.11r / WPA3 - no much difference
- 2.4GHz / 5GHz - 2.4GHz is stable, but triggers a microcode error from time to time
- no dynamic channel selection
- pinning to a specific BSSID doesn't make much difference
- running a single Deco doesn't trigger the issue so it looks like mesh related

https://launchpad.net/~nobuto/+archive/ubuntu/experimental-packages?field.series_filter=jammy
https://launchpad.net/~nobuto/+archive/ubuntu/experimental-packages/+build/24301259/+files/wpasupplicant_2.10-6ubuntu2.1~ppa1_amd64.deb

https://w1.fi/cgit/hostap/commit/?id=8e0ac53660aaa9691e140156c47fddb7cd8c62b6


[2.4GHz]
```
Aug 18 07:36:38 t480 kernel: [  318.997341] wlp3s0: send auth to 6c:5a:b0:xx:yy:zz (try 1/3)
Aug 18 07:36:38 t480 kernel: [  319.007629] wlp3s0: authenticated
Aug 18 07:36:38 t480 kernel: [  319.009801] wlp3s0: associate with 6c:5a:b0:xx:yy:zz (try 1/3)
Aug 18 07:36:38 t480 kernel: [  319.026702] wlp3s0: RX AssocResp from 6c:5a:b0:xx:yy:zz (capab=0x1031 status=0 aid=2)
Aug 18 07:36:38 t480 kernel: [  319.028803] wlp3s0: associated
Aug 18 07:36:38 t480 kernel: [  319.087057] wlp3s0: Limiting TX power to 23 (23 - 0) dBm as advertised by 6c:5a:b0:xx:yy:zz
Aug 18 07:36:38 t480 kernel: [  319.114543] IPv6: ADDRCONF(NETDEV_CHANGE): wlp3s0: link becomes ready
Aug 18 07:36:52 t480 kernel: [  333.101871] iwlwifi 0000:03:00.0: Queue 11 is active on fifo 1 and stuck for 10000 ms. SW [54, 183] HW [54, 183] FH TRB=0x0c010b045
Aug 18 07:36:52 t480 kernel: [  333.103010] iwlwifi 0000:03:00.0: Microcode SW error detected.  Restarting 0x2000000.
Aug 18 07:36:52 t480 kernel: [  333.103163] iwlwifi 0000:03:00.0: Start IWL Error Log Dump:
Aug 18 07:36:52 t480 kernel: [  333.103167] iwlwifi 0000:03:00.0: Transport status: 0x0000004A, valid: 6
Aug 18 07:36:52 t480 kernel: [  333.103170] iwlwifi 0000:03:00.0: Loaded firmware version: 36.ca7b901d.0 8265-36.ucode
```


```
$ lspci -nnkv | sed -n '/Network/,/^$/p'
03:00.0 Network controller [0280]: Intel Corporation Wireless 8265 / 8275 [8086:24fd] (rev 78)
	Subsystem: Intel Corporation Dual Band Wireless-AC 8265 [8086:0010]
	Flags: bus master, fast devsel, latency 0, IRQ 159
	Memory at dc100000 (64-bit, non-prefetchable) [size=8K]
	Capabilities: <access denied>
	Kernel driver in use: iwlwifi
	Kernel modules: iwlwifi
```

[kern.log]
```
Aug 21 13:23:07 t480 kernel: [136930.972759] wlp3s0: Connection to AP 6c:5a:b0:xx:yy:zz lost
Aug 21 13:23:07 t480 kernel: [136931.217753] wlp3s0: authenticate with 6c:5a:b0:xx:yy:zz
Aug 21 13:23:07 t480 kernel: [136931.229486] wlp3s0: send auth to 6c:5a:b0:xx:yy:zz (try 1/3)
Aug 21 13:23:07 t480 kernel: [136931.234536] wlp3s0: authenticated
Aug 21 13:23:07 t480 kernel: [136931.239353] wlp3s0: associate with 6c:5a:b0:xx:yy:zz (try 1/3)
Aug 21 13:23:07 t480 kernel: [136931.253596] wlp3s0: RX AssocResp from 6c:5a:b0:xx:yy:zz (capab=0x1511 status=0 aid=2)
Aug 21 13:23:07 t480 kernel: [136931.255411] wlp3s0: associated
Aug 21 13:23:08 t480 kernel: [136931.843561] iwlwifi 0000:03:00.0: No beacon heard and the time event is over already...
Aug 21 13:23:08 t480 kernel: [136931.843650] wlp3s0: Connection to AP 6c:5a:b0:xx:yy:zz lost
Aug 21 13:23:08 t480 kernel: [136932.067556] wlp3s0: authenticate with 6c:5a:b0:xx:yy:zz
Aug 21 13:23:08 t480 kernel: [136932.078277] wlp3s0: send auth to 6c:5a:b0:xx:yy:zz (try 1/3)
Aug 21 13:23:08 t480 kernel: [136932.085292] wlp3s0: authenticated
Aug 21 13:23:08 t480 kernel: [136932.087341] wlp3s0: associate with 6c:5a:b0:xx:yy:zz (try 1/3)
Aug 21 13:23:08 t480 kernel: [136932.100737] wlp3s0: RX AssocResp from 6c:5a:b0:xx:yy:zz (capab=0x1511 status=0 aid=2)
Aug 21 13:23:08 t480 kernel: [136932.103238] wlp3s0: associated
Aug 21 13:23:08 t480 kernel: [136932.392477] wlp3s0: Limiting TX power to 20 (23 - 3) dBm as advertised by 6c:5a:b0:xx:yy:zz
```

```
$ sudo wpa_cli log_level
Selected interface 'wlp3s0'
Current level: INFO
Timestamp: 0

$ sudo wpa_cli log_level DEBUG
Selected interface 'wlp3s0'
OK
```

[syslog]
```
Aug 21 13:23:04 t480 wpa_supplicant[1613]: nl80211: BSS Event 59 (NL80211_CMD_FRAME) received for wlp3s0
Aug 21 13:23:04 t480 wpa_supplicant[1613]: nl80211: RX frame da=d4:3b:04:xx:yy:zz sa=6c:5a:b0:xx:yy:zz bssid=6c:5a:b0:xx:yy:zz freq=5240 ssi_signal=-65 fc=0x40d0 seq_ctrl=0x5a0 stype=13 (WLAN_FC_STYPE_ACTION
) len=75
Aug 21 13:23:04 t480 wpa_supplicant[1613]: wlp3s0: Event RX_MGMT (18) received
Aug 21 13:23:04 t480 wpa_supplicant[1613]: wlp3s0: Received Action frame: SA=6c:5a:b0:xx:yy:zz Category=5 DataLen=50 freq=5240 MHz
Aug 21 13:23:04 t480 wpa_supplicant[1613]: Measurement request type 5 token 84
Aug 21 13:23:04 t480 wpa_supplicant[1613]: Beacon request: No valid channels
Aug 21 13:23:04 t480 wpa_supplicant[1613]: nl80211: Send Action frame (ifindex=3, freq=5240 MHz wait=0 ms no_cck=0 offchanok=1)
Aug 21 13:23:04 t480 wpa_supplicant[1613]: nl80211: Drop oldest pending send frame cookie 0x36d
Aug 21 13:23:04 t480 wpa_supplicant[1613]: nl80211: Ignored event 60 (NL80211_CMD_FRAME_TX_STATUS) for foreign interface (ifindex 3 wdev 0x0)
Aug 21 13:23:04 t480 wpa_supplicant[1613]: nl80211: Drv Event 60 (NL80211_CMD_FRAME_TX_STATUS) received for wlp3s0
Aug 21 13:23:04 t480 wpa_supplicant[1613]: nl80211: Frame TX status event A1=6c:5a:b0:xx:yy:zz stype=13 cookie=0x381 ack=1
Aug 21 13:23:04 t480 wpa_supplicant[1613]: nl80211: Frame TX status: cookie=0x381 (match) (ack=1)
Aug 21 13:23:04 t480 wpa_supplicant[1613]: wlp3s0: Event TX_STATUS (16) received
Aug 21 13:23:04 t480 wpa_supplicant[1613]: wlp3s0: EVENT_TX_STATUS dst=6c:5a:b0:xx:yy:zz type=0 stype=13
Aug 21 13:23:04 t480 wpa_supplicant[1613]: Off-channel: Ignore Action TX status - no pending operation
Aug 21 13:23:06 t480 wpa_supplicant[1613]: nl80211: Ignored event 64 (NL80211_CMD_NOTIFY_CQM) for foreign interface (ifindex 3 wdev 0x0)
Aug 21 13:23:06 t480 wpa_supplicant[1613]: nl80211: Drv Event 64 (NL80211_CMD_NOTIFY_CQM) received for wlp3s0
Aug 21 13:23:06 t480 wpa_supplicant[1613]: nl80211: Beacon loss event
Aug 21 13:23:06 t480 wpa_supplicant[1613]: wlp3s0: Event BEACON_LOSS (53) received
Aug 21 13:23:06 t480 wpa_supplicant[1613]: wlp3s0: CTRL-EVENT-BEACON-LOSS
Aug 21 13:23:07 t480 wpa_supplicant[1613]: RTM_NEWLINK: ifi_index=3 ifname=wlp3s0 operstate=2 linkmode=1 ifi_family=0 ifi_flags=0x1003 ([UP])
Aug 21 13:23:07 t480 kernel: [136930.972759] wlp3s0: Connection to AP 6c:5a:b0:xx:yy:zz lost
Aug 21 13:23:07 t480 wpa_supplicant[1613]: nl80211: Ignored event 20 (NL80211_CMD_DEL_STATION) for foreign interface (ifindex 3 wdev 0x0)
Aug 21 13:23:07 t480 wpa_supplicant[1613]: nl80211: Drv Event 20 (NL80211_CMD_DEL_STATION) received for wlp3s0
Aug 21 13:23:07 t480 wpa_supplicant[1613]: nl80211: Delete station 6c:5a:b0:xx:yy:zz
Aug 21 13:23:07 t480 wpa_supplicant[1613]: nl80211: Ignored event 39 (NL80211_CMD_DEAUTHENTICATE) for foreign interface (ifindex 3 wdev 0x0)
Aug 21 13:23:07 t480 wpa_supplicant[1613]: nl80211: Drv Event 39 (NL80211_CMD_DEAUTHENTICATE) received for wlp3s0
Aug 21 13:23:07 t480 wpa_supplicant[1613]: nl80211: Deauthenticate event
Aug 21 13:23:07 t480 wpa_supplicant[1613]: wlp3s0: Event DEAUTH (11) received
Aug 21 13:23:07 t480 wpa_supplicant[1613]: wlp3s0: Deauthentication notification
Aug 21 13:23:07 t480 wpa_supplicant[1613]: wlp3s0:  * reason 4 (DISASSOC_DUE_TO_INACTIVITY) locally_generated=1
Aug 21 13:23:07 t480 wpa_supplicant[1613]: wlp3s0:  * address 6c:5a:b0:xx:yy:zz
Aug 21 13:23:07 t480 wpa_supplicant[1613]: Deauthentication frame IE(s) - hexdump(len=0): [NULL]
Aug 21 13:23:07 t480 wpa_supplicant[1613]: wlp3s0: CTRL-EVENT-DISCONNECTED bssid=6c:5a:b0:xx:yy:zz reason=4 locally_generated=1
```


[combined log of t480 and deco]
```
Aug 21 14:01:06 deco daemon.err nrd[22822] estimatorCmnHandleBeaconReportEvent: Invalid beacon report for D4:3B:04:XX:YY:ZZ
Aug 21 14:01:06 t480 wpa_supplicant[1613]: Beacon request: No valid channels
Aug 21 14:01:12 deco daemon.err nrd[22822] estimatorCmnHandleBeaconReportEvent: Invalid beacon report for D4:3B:04:XX:YY:ZZ
Aug 21 14:01:12 t480 wpa_supplicant[1613]: Beacon request: No valid channels
Aug 21 14:01:18 deco daemon.err nrd[22822] estimatorCmnHandleBeaconReportEvent: Invalid beacon report for D4:3B:04:XX:YY:ZZ
Aug 21 14:01:18 t480 wpa_supplicant[1613]: Beacon request: No valid channels
Aug 21 14:01:24 deco daemon.err nrd[22822] estimatorCmnHandleBeaconReportEvent: Invalid beacon report for D4:3B:04:XX:YY:ZZ
Aug 21 14:01:24 t480 wpa_supplicant[1613]: Beacon request: No valid channels
Aug 21 14:01:30 deco daemon.err nrd[22822] estimatorCmnHandleBeaconReportEvent: Invalid beacon report for D4:3B:04:XX:YY:ZZ
Aug 21 14:01:30 t480 wpa_supplicant[1613]: Beacon request: No valid channels
Aug 21 14:01:36 deco daemon.err nrd[22822] estimatorCmnHandleBeaconReportEvent: Invalid beacon report for D4:3B:04:XX:YY:ZZ
Aug 21 14:01:36 t480 wpa_supplicant[1613]: Beacon request: No valid channels
Aug 21 14:01:42 deco daemon.err nrd[22822] estimatorCmnHandleBeaconReportEvent: Invalid beacon report for D4:3B:04:XX:YY:ZZ
Aug 21 14:01:42 t480 wpa_supplicant[1613]: Beacon request: No valid channels
Aug 21 14:01:48 deco daemon.err nrd[22822] estimatorCmnHandleBeaconReportEvent: Invalid beacon report for D4:3B:04:XX:YY:ZZ
Aug 21 14:01:48 t480 wpa_supplicant[1613]: Beacon request: No valid channels
Aug 21 14:01:54 deco daemon.err nrd[22822] estimatorCmnHandleBeaconReportEvent: Invalid beacon report for D4:3B:04:XX:YY:ZZ
Aug 21 14:01:54 t480 wpa_supplicant[1613]: Beacon request: No valid channels
Aug 21 14:02:00 deco daemon.err nrd[22822] estimatorCmnHandleBeaconReportEvent: Invalid beacon report for D4:3B:04:XX:YY:ZZ
Aug 21 14:02:00 t480 wpa_supplicant[1613]: Beacon request: No valid channels
```


```
Sun Aug 21 17:12:05 2022 daemon.notice nrd[22822] estimatorPerformMeasurement: Do 11k measuremrent for D4:3B:04:XX:YY:ZZ on channel 48 from serving BSS APId 255 ChanId 48  ESSId 0
Sun Aug 21 17:12:05 2022 daemon.err nrd[22822] estimatorCmnHandleBeaconReportEvent: Invalid beacon report for D4:3B:04:XX:YY:ZZ
```


```
Sun Aug 21 18:15:55 2022 daemon.notice nrd[22822] estimatorPerformMeasurement: Do 11k measuremrent for D4:3B:04:XX:YY:ZZ on channel 48 from serving BSS APId 255 ChanId 48  ESSId 0
Sun Aug 21 18:15:55 2022 daemon.notice nrd[22822] wlanifBSteerEventsHandleBeaconReport: Beacon report from D4:3B:04:XX:YY:ZZ: APId 1   ChanId 48  ESSId 0  bssid 6C:5A:B0:XX:YY:ZZ rcpi: 104
Sun Aug 21 18:15:55 2022 daemon.notice nrd[22822] wlanifBSteerEventsHandleBeaconReport: Beacon report from D4:3B:04:XX:YY:ZZ: APId 255 ChanId 48  ESSId 0  bssid 6C:5A:B0:XX:YY:ZZ rcpi: 68
Sun Aug 21 18:15:55 2022 daemon.notice nrd[22822] stadbEntry_modifyTriggerThreshold: For client D4:3B:04:XX:YY:ZZ RSSI: 16 | RCPI: -76 | threshold RSSI: 35
Sun Aug 21 18:15:55 2022 daemon.notice nrd[22822] steerexecStartSteer: Starting new steer for D4:3B:04:XX:YY:ZZ(transaction 193)
Sun Aug 21 18:15:55 2022 daemon.notice nrd[22822] steerexecHandleBTMResponseEvent: Received BTM response from D4:3B:04:XX:YY:ZZ with non-success code (7) (transaction 193)
Sun Aug 21 18:15:55 2022 daemon.notice nrd[22822] stadbEntry_updateARBtmThreshold: update threshold of client D4:3B:04:XX:YY:ZZ for reason 1
```
