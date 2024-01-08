---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "表示されないFlutter版ubuntu-desktop-installerをgit版で無理やり動かす"
slug: 1704727518
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
date: 2024-01-09T00:25:18+09:00
lastmod: 2024-01-09T00:25:18+09:00

featured: false
draft: false

# Featured image
# To use, add an image named "featured.jpg/png" to your page's folder.
image:
  preview_only: true
---

Ubuntu開発版のインストールイメージのスナップショットが置いてある["daily-live"](https://cdimages.ubuntu.com/daily-live/)を使う人はほぼいないと思うけど（正式版でもないし）、昨年末ぐらいからまた画面が真っ白になっちゃう問題が発生しているみたい。

- https://bugs.launchpad.net/ubuntu-desktop-installer/+bug/2043915
- https://github.com/canonical/ubuntu-desktop-installer/issues/2391
- https://github.com/canonical/ubuntu-desktop-installer/issues/2407

{{< figure src="featured.png" caption="インストーラーを起動しても画面が真っ白" >}}

エラーとしてはLibGLまわりで、snap化されてるFlutterアプリケーションで起こっているみたい。

```bash
$ ubuntu-desktop-installer

(ubuntu_desktop_installer:10370): Gtk-WARNING **: 07:10:37.648: /usr/lib/x86_64-linux-gnu/gtk-3.0/3.0.0/immodules/im-ibus.so: undefined symbol: ibus_input_context_set_post_process_key_event

(ubuntu_desktop_installer:10370): Gtk-WARNING **: 07:10:37.648: Loading IM context type 'ibus' failed
libGL error: No matching fbConfigs or visuals found
libGL error: failed to load driver: radeonsi
libGL error: No matching fbConfigs or visuals found
libGL error: failed to load driver: radeonsi
libGL error: No matching fbConfigs or visuals found
libGL error: failed to load driver: swrast

** (ubuntu_desktop_installer:10370): WARNING **: 07:10:37.791: Failed to start Flutter renderer: Unable to create a GL context

** (ubuntu_desktop_installer:10370): WARNING **: 07:10:40.793: Unable to retrieve framework response: No engine to send to
```

これといった回避策はなくて、23.10をインストールしてから`do-release-upgrade -d`すればインストール直後だしすぐにnoble開発版に追いつけるんだけどまあちょっと無理やり動かしてみたメモ。たぶん今後一切使うことがないと思う。


## インストーラーを一旦終了する

Liveセッション立ち上げると、画面の真ん中に真っ白なウィンドウが出てきて何もできないので、まずはこのインストーラーを終了する。ウィンドウが閉じらればよくて、`Alt + F4`とか、`Alt + Space`でメニューを出して`Close`を押すとかそんな感じ。

## Flutterのインストール

次にFlutterのインストール。Snap版とそうじゃない2パターンがドキュメントにあって今回はSnap版じゃない方を使う。CIでもSnap版使ってなさそうですし。

https://github.com/canonical/ubuntu-desktop-installer/blob/4edfab2b3cd4fb18ab95c79ffa16040fa57449b9/.github/workflows/installer.yaml#L31-L35
> ```yaml
>       - name: Install Flutter
>         uses: subosito/flutter-action@v2
>         with:
>           channel: 'stable'
>           flutter-version: ${{env.FLUTTER_VERSION}}
> ```

ちなみにインストールするバージョンは最新では自分で色々と変更する必要がありそうで、インストーラーが想定している`3.10.6`を明示的に使う。

https://github.com/canonical/ubuntu-desktop-installer/blob/4edfab2b3cd4fb18ab95c79ffa16040fa57449b9/snap/snapcraft.yaml#L130-L132
> ```yaml
>   flutter-git:
>     source: https://github.com/flutter/flutter.git
>     source-tag: 3.10.6
> ```

インストール手順としてはFlutterの公式ドキュメントの`Method 2: Manual installation`を少し変えて

https://docs.flutter.dev/get-started/install/linux#method-2-manual-installation

```bash
sudo apt update
sudo apt install git

wget https://storage.googleapis.com/flutter_infra_release/releases/stable/linux/flutter_linux_3.10.6-stable.tar.xz

tar xf flutter_linux_3.10.6-stable.tar.xz

export PATH="$PATH:`pwd`/flutter/bin"
```

とした後に、`flutter doctor`でインストール自体ができてるかの確認。

```bash
$ flutter doctor

┌─────────────────────────────────────────────────────────┐
│ A new version of Flutter is available!                  │
│                                                         │
│ To update to the latest version, run "flutter upgrade". │
└─────────────────────────────────────────────────────────┘

...

Doctor summary (to see all details, run flutter doctor -v):
[✓] Flutter (Channel stable, 3.10.6, on Ubuntu Noble Numbat (development branch) 6.5.0-9-generic, locale C.UTF-8)
[✗] Android toolchain - develop for Android devices
    ✗ Unable to locate Android SDK.
      Install Android Studio from: https://developer.android.com/studio/index.html
      On first launch it will assist you in installing the Android SDK components.
      (or visit https://flutter.dev/docs/get-started/install/linux#android-setup for detailed instructions).
      If the Android SDK has been installed to a custom location, please use
      `flutter config --android-sdk` to update to that location.

[✗] Chrome - develop for the web (Cannot find Chrome executable at google-chrome)
    ! Cannot find Chrome. Try setting CHROME_EXECUTABLE to a Chrome executable.
[✗] Linux toolchain - develop for Linux desktop
    ✗ clang++ is required for Linux development.
      It is likely available from your distribution (e.g.: apt install clang), or can be downloaded from https://releases.llvm.org/
    ✗ CMake is required for Linux development.
      It is likely available from your distribution (e.g.: apt install cmake), or can be downloaded from https://cmake.org/download/
    ✗ ninja is required for Linux development.
      It is likely available from your distribution (e.g.: apt install ninja-build), or can be downloaded from https://github.com/ninja-build/ninja/releases
    ✗ pkg-config is required for Linux development.
      It is likely available from your distribution (e.g.: apt install pkg-config), or can be downloaded from https://www.freedesktop.org/wiki/Software/pkg-config/
[!] Android Studio (not installed)
[✓] Connected device (1 available)
[✓] Network resources

! Doctor found issues in 4 categories.

...
```

`Linux toolchain`のところで足りないものがあるよと言われるので、これもまた公式ドキュメントの`Linux prerequisites`の通り

https://docs.flutter.dev/get-started/install/linux#linux-prerequisites

> ```bash
> sudo apt-get install clang cmake git ninja-build pkg-config libgtk-3-dev liblzma-dev libstdc++-12-dev
> ```

とすれば、`flutter doctor`でインストール自体と`Linux toolchain`のところのチェックが通るようになる。

```bash
$ flutter doctor
Doctor summary (to see all details, run flutter doctor -v):
[✓] Flutter (Channel stable, 3.10.6, on Ubuntu Noble Numbat (development branch) 6.5.0-9-generic, locale C.UTF-8)
[✗] Android toolchain - develop for Android devices
    ✗ Unable to locate Android SDK.
      Install Android Studio from: https://developer.android.com/studio/index.html
      On first launch it will assist you in installing the Android SDK components.
      (or visit https://flutter.dev/docs/get-started/install/linux#android-setup for detailed instructions).
      If the Android SDK has been installed to a custom location, please use
      `flutter config --android-sdk` to update to that location.

[✗] Chrome - develop for the web (Cannot find Chrome executable at google-chrome)
    ! Cannot find Chrome. Try setting CHROME_EXECUTABLE to a Chrome executable.
[✓] Linux toolchain - develop for Linux desktop
[!] Android Studio (not installed)
[✓] Connected device (1 available)
[✓] Network resources

! Doctor found issues in 3 categories.
```

## Git版ubuntu-desktop-installerの実行

Flutterがインストールできたので、今度はubuntu-desktop-installerの実行。

https://github.com/canonical/ubuntu-desktop-installer/blob/4edfab2b3cd4fb18ab95c79ffa16040fa57449b9/README.md#build-and-dry-run-the-installer

Githubから引っ張ってくるところはそのままでよくて、

```bash
git clone https://github.com/canonical/ubuntu-desktop-installer.git
cd ubuntu-desktop-installer
git submodule update --init --recursive
```

依存するものをインストールして

```bash
make install_deps
```

で、実際のディレクトリに移動して実行するところで、このまま実行するとドキュメントに書いてある通り`Dry-run`モードになるので実機にはUbuntuをインストールできない（テスト用の仮想ドライブが使われる）。

Dry-runをやめるには`LIVE_RUN=1`が肝で、

https://github.com/canonical/ubuntu-desktop-installer/blob/4edfab2b3cd4fb18ab95c79ffa16040fa57449b9/snap/snapcraft.yaml#L34
> ```yaml
>       LIVE_RUN: 1
> ```

これを渡してあげるとGit版から実機インストールできる。

```bash
cd packages/ubuntu_desktop_installer
env LIVE_RUN=1 flutter run
```

## まとめ

どっからどう読んでも無理やりなので、正式なインストールイメージを使うか、どうしても`daily-live`がいいっていう場合は待つのが正解。

ちなみに、`ubuntu-desktop-installer` snapは消してしまってはダメで、`flutter run`するだけだとDry-runモード用の`subiquity-server`が立ち上がって、もちろん実機を想定してない。実機インストールの場合はサーバープロセスはもちろん高い権限が必要なので、最初から入ってるSnap版から`snap.ubuntu-desktop-installer.subiquity-server.service`を使った。問題があったのはフロントエンドの表示部分だけなので。
