---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Build mattermost android app quickly for github pr"
slug: build-mattermost-android-app-quickly-for-github-pr
subtitle: ""
summary: ""
authors: []

tags: []
categories: []

year: 2021
date: 2021-01-02T14:08:37+09:00
lastmod: 2021-01-02T14:08:37+09:00

featured: false
draft: true

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

Reference:
- https://developers.mattermost.com/contribute/mobile/
- https://github.com/mattermost/mattermost-mobile/blob/master/.circleci/config.yml



lxc launch ubuntu:focal android-build-test
lxc exec android-build-test -- sudo --login --user ubuntu


cat <<EOF | xargs sudo apt install -y

ruby-bundler
ruby-dev
build-essential
zlib1g-dev
default-jre

EOF



https://developer.android.com/studio/index.html#command-tools


wget https://dl.google.com/android/repository/commandlinetools-linux-6858069_latest.zip

unzip commandlinetools-linux-6858069_latest.zip

export ANDROID_SDK_ROOT=~/Android/Sdk
mkdir -p $ANDROID_SDK_ROOT/cmdline-tools/latest
cp -r ~/cmdline-tools/* $ANDROID_SDK_ROOT/cmdline-tools/latest/

$ANDROID_SDK_ROOT/cmdline-tools/latest/bin/sdkmanager --list_installed
-> 0

mkdir $ANDROID_SDK_ROOT/licenses/
cat > $ANDROID_SDK_ROOT/licenses/android-sdk-license <<EOF
24333f8a63b6825ea9c5514f83c2829b004d1fee
EOF



$ ~/Android/Sdk/cmdline-tools/latest/bin/sdkmanager --list_installed
Installed packages:
  Path                 | Version | Description                    | Location
  -------              | ------- | -------                        | -------
  build-tools;29.0.2   | 29.0.2  | Android SDK Build-Tools 29.0.2 | build-tools/29.0.2/
  emulator             | 30.2.6  | Android Emulator               | emulator/
  patcher;v4           | 1       | SDK Patch Applier v4           | patcher/v4/
  platform-tools       | 30.0.5  | Android SDK Platform-Tools     | platform-tools/
  platforms;android-29 | 5       | Android SDK Platform 29        | platforms/android-29/



$ keytool -genkey -v -keystore my-release-key.keystore -alias my-key-alias -keyalg RSA -keysize 2048 -validity 10000
Enter keystore password:
Re-enter new password:
What is your first and last name?
  [Unknown]:
What is the name of your organizational unit?
  [Unknown]:
What is the name of your organization?
  [Unknown]:
What is the name of your City or Locality?
  [Unknown]:
What is the name of your State or Province?
  [Unknown]:
What is the two-letter country code for this unit?
  [Unknown]:
Is CN=Unknown, OU=Unknown, O=Unknown, L=Unknown, ST=Unknown, C=Unknown correct?
  [no]:  yes

Generating 2,048 bit RSA key pair and self-signed certificate (SHA256withRSA) with a validity of 10,000 days
        for: CN=Unknown, OU=Unknown, O=Unknown, L=Unknown, ST=Unknown, C=Unknown
[Storing my-release-key.keystore]


* What went wrong:
A problem occurred configuring project ':react-native-reanimated'.
> Failed to install the following Android SDK packages as some licences have not been accepted.
     patcher;v4 SDK Patch Applier v4
     platform-tools Android SDK Platform-Tools
     platforms;android-29 Android SDK Platform 29
     tools Android SDK Tools
     build-tools;29.0.2 Android SDK Build-Tools 29.0.2
     emulator Android Emulator
  To build this project, accept the SDK license agreements and install the missing components using the Android Studio SDK Manager.
  Alternatively, to transfer the license agreements from one workstation to another, see http://d.android.com/r/studio-ui/export-licenses.html




sudo rm -v /etc/apt/apt.conf.d/90cloud-init-aptproxy


curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -
sudo apt-get install -y nodejs

$ node --version
v14.15.3
$ npm --version
6.14.9


git clone https://github.com/mattermost/mattermost-mobile.git
cd mattermost-mobile/


$ npm run build:android


./scripts/build.sh: line 65: bundle: command not found



> SDK location not found. Define location with an ANDROID_SDK_ROOT environment variable or by setting the sdk.dir path in your project's local properties file at '/home/ubuntu/mattermost-mobile/android/local.properties'.



https://developer.android.com/studio/index.html#command-tools


/var/lib/gems/2.7.0/gems/fastlane-2.170.0/fastlane_core/lib/fastlane_core/ui/interface.rb:153:in `shell_error!': \e[31m[!] Exit status of command '/home/ubuntu/mattermost-mobile/android/gradlew assembleUnsigned -p android/' was 1 instead of 0. (FastlaneCore::Interface::FastlaneShellError)

ERROR: JAVA_HOME is not set and no 'java' command could be found in your PATH.

Please set the JAVA_HOME variable in your environment to match the
location of your Java installation.


## Run `sudo apt-get install -y nodejs` to install Node.js 14.x and npm
## You may also need development tools to build native addons:
     sudo apt-get install gcc g++ make
## To install the Yarn package manager, run:
     curl -sL https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
     echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
     sudo apt-get update && sudo apt-get install yarn


/usr/lib/ruby/2.7.0/mkmf.rb:471:in `try_do': The compiler failed to generate an executable file. (RuntimeError)
You have to install development tools first.


zlib is missing; necessary for building libxml2



```
16835 silly build mmjstool@1.0.0
16836 info linkStuff mmjstool@1.0.0
16837 silly linkStuff mmjstool@1.0.0 has /home/ubuntu/.npm/_cacache/tmp as its parent node_modules
16838 silly install mmjstool@1.0.0
16839 info lifecycle mmjstool@1.0.0~install: mmjstool@1.0.0
16840 silly postinstall mmjstool@1.0.0
16841 info lifecycle mmjstool@1.0.0~postinstall: mmjstool@1.0.0
16842 silly prepublish mmjstool@1.0.0
16843 info lifecycle mmjstool@1.0.0~prepublish: mmjstool@1.0.0
16844 info lifecycle mmjstool@1.0.0~prepublish: ignored because ignore-prepublish is set to true mmjstool@1.0.0
16845 info lifecycle mmjstool@1.0.0~prepare: mmjstool@1.0.0
16846 warn lifecycle The node binary used for scripts is /snap/bin/node but npm is using /snap/node/3450/bin/node itself. Use the `--scripts-prepend-node-path` option to include the path for the node binary npm was executed with.
16847 verbose lifecycle mmjstool@1.0.0~prepare: unsafe-perm in lifecycle true
16848 verbose lifecycle mmjstool@1.0.0~prepare: PATH: /snap/node/3450/lib/node_modules/npm/node_modules/npm-lifecycle/node-gyp-bin:/home/ubuntu/.npm/_cacache/tmp/git-clone-fd773057/node_modules/.bin:/snap/node/3450/lib/node_modules/npm/node_modules/npm-lifecycle/node-gyp-bin:/home/ubuntu/mattermost-mobile/node_modules/.bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin
16849 verbose lifecycle mmjstool@1.0.0~prepare: CWD: /home/ubuntu/.npm/_cacache/tmp/git-clone-fd773057
16850 silly lifecycle mmjstool@1.0.0~prepare: Args: [ '-c', 'webpack' ]
16851 silly lifecycle mmjstool@1.0.0~prepare: Returned: code: 1  signal: null
16852 info lifecycle mmjstool@1.0.0~prepare: Failed to exec prepare script
16853 verbose stack Error: mmjstool@1.0.0 prepare: `webpack`
16853 verbose stack Exit status 1
16853 verbose stack     at EventEmitter.<anonymous> (/snap/node/3450/lib/node_modules/npm/node_modules/npm-lifecycle/index.js:332:16)
16853 verbose stack     at EventEmitter.emit (events.js:315:20)
16853 verbose stack     at ChildProcess.<anonymous> (/snap/node/3450/lib/node_modules/npm/node_modules/npm-lifecycle/lib/spawn.js:55:14)
16853 verbose stack     at ChildProcess.emit (events.js:315:20)
16853 verbose stack     at maybeClose (internal/child_process.js:1048:16)
16853 verbose stack     at Process.ChildProcess._handle.onexit (internal/child_process.js:288:5)
16854 verbose pkgid mmjstool@1.0.0
```


$ snap list node
Name  Version  Rev   Tracking   Publisher  Notes
node  14.15.3  3450  14/stable  iojs✓      classic

$ sudo snap install ruby --classic


I figured it out, you need to cd into ./fastlane THEN run bundle install



gem install bundler --version 2.0.2

gem install fastlane -NV

$ sudo snap refresh --channel 2.7/stable ruby

$ sudo apt install build-essential

$ sudo apt install zlib1g-dev


sudo gem install fastlane

fastlane android dev




Jan 02 15:13:37 t480 kernel: audit: type=1400 audit(1609568017.312:5049): apparmor="DENIED" operation="file_inherit" namespace="root//lxd-android-build_<var-snap-lxd-common-lxd>" profile="/snap/snapd/10492/usr/lib/snapd/snap-confine" pid=712890 comm="snap-confine" family="unix" sock_type="stream" protocol=0 requested_mask="send receive" denied_mask="send receive" addr=none peer_addr=none peer="snap.node.npm"
Jan 02 15:13:37 t480 kernel: audit: type=1400 audit(1609568017.312:5050): apparmor="DENIED" operation="file_inherit" namespace="root//lxd-android-build_<var-snap-lxd-common-lxd>" profile="/snap/snapd/10492/usr/lib/snapd/snap-confine" pid=712890 comm="snap-confine" family="unix" sock_type="stream" protocol=0 requested_mask="send receive" denied_mask="send receive" addr=none peer_addr=none peer="snap.node.npm"
Jan 02 15:13:37 t480 kernel: audit: type=1400 audit(1609568017.312:5051): apparmor="DENIED" operation="file_inherit" namespace="root//lxd-android-build_<var-snap-lxd-common-lxd>" profile="/snap/snapd/10492/usr/lib/snapd/snap-confine" pid=712890 comm="snap-confine" family="unix" sock_type="stream" protocol=0 requested_mask="send receive" denied_mask="send receive" addr=none peer_addr=none peer="snap.node.npm"






npm ERR! prepareGitDep 2> internal/modules/cjs/loader.js:883
npm ERR! prepareGitDep   throw err;
npm ERR! prepareGitDep   ^
npm ERR! prepareGitDep
npm ERR! prepareGitDep Error: Cannot find module 'semver'
npm ERR! prepareGitDep Require stack:
npm ERR! prepareGitDep - /usr/share/npm/lib/utils/unsupported.js
npm ERR! prepareGitDep - /usr/share/npm/bin/npm-cli.js
npm ERR! prepareGitDep     at Function.Module._resolveFilename (internal/modules/cjs/loader.js:880:15)
npm ERR! prepareGitDep     at Function.Module._load (internal/modules/cjs/loader.js:725:27)
npm ERR! prepareGitDep     at Module.require (internal/modules/cjs/loader.js:952:19)
npm ERR! prepareGitDep     at require (internal/modules/cjs/helpers.js:88:18)
npm ERR! prepareGitDep     at Object.<anonymous> (/usr/share/npm/lib/utils/unsupported.js:2:14)
npm ERR! prepareGitDep     at Module._compile (internal/modules/cjs/loader.js:1063:30)
npm ERR! prepareGitDep     at Object.Module._extensions..js (internal/modules/cjs/loader.js:1092:10)
npm ERR! prepareGitDep     at Module.load (internal/modules/cjs/loader.js:928:32)
npm ERR! prepareGitDep     at Function.Module._load (internal/modules/cjs/loader.js:769:14)
npm ERR! prepareGitDep     at Module.require (internal/modules/cjs/loader.js:952:19) {
npm ERR! prepareGitDep   code: 'MODULE_NOT_FOUND',
npm ERR! prepareGitDep   requireStack: [
npm ERR! prepareGitDep




         3: from /home/ubuntu/.gem/gems/fastlane-plugin-android_change_string_app_name-0.1.1/lib/fastlane/plugin/android_change_string_app_name/actions/android_change_string_app_name_action.rb:8:in `run'
         2: from /home/ubuntu/.gem/gems/fastlane-plugin-android_change_string_app_name-0.1.1/lib/fastlane/plugin/android_change_string_app_name/actions/android_change_string_app_name_action.rb:8:in `require'
         1: from /home/ubuntu/.gem/gems/nokogiri-1.10.10/lib/nokogiri.rb:30:in `<top (required)>'


/home/ubuntu/.gem/gems/nokogiri-1.10.10/lib/nokogiri.rb:32:in `require': /snap/core18/current/lib/x86_64-linux-gnu/libc.so.6: version `GLIBC_2.28' not found (required by /home/ubuntu/.gem/gems/nokogiri-1.10.
10/lib/nokogiri/nokogiri.so) - /home/ubuntu/.gem/gems/nokogiri-1.10.10/lib/nokogiri/nokogiri.so (LoadError)



Gem::Ext::BuildError: ERROR: Failed to build gem native extension.

    current directory: /tmp/bundler20210102-16185-17jf1gdigest-crc-0.6.2/gems/digest-crc-0.6.2/ext/digest
/usr/bin/ruby2.7 -I/usr/lib/ruby/2.7.0/rubygems -rrubygems /usr/share/rubygems-integration/all/gems/rake-13.0.1/exe/rake
RUBYARCHDIR\=/tmp/bundler20210102-16185-17jf1gdigest-crc-0.6.2/extensions/x86_64-linux/2.7.0/digest-crc-0.6.2
RUBYLIBDIR\=/tmp/bundler20210102-16185-17jf1gdigest-crc-0.6.2/extensions/x86_64-linux/2.7.0/digest-crc-0.6.2
mkmf.rb can't find header files for ruby at /usr/lib/ruby/include/ruby.h

You might have to install separate package for the ruby development
environment, ruby-dev or ruby-devel for example.

rake failed, exit code 1

Gem files will remain installed in /tmp/bundler20210102-16185-17jf1gdigest-crc-0.6.2/gems/digest-crc-0.6.2 for inspection.
Results logged to /tmp/bundler20210102-16185-17jf1gdigest-crc-0.6.2/extensions/x86_64-linux/2.7.0/digest-crc-0.6.2/gem_make.out

An error occurred while installing digest-crc (0.6.2), and Bundler cannot continue.
Make sure that `gem install digest-crc -v '0.6.2' --source 'https://rubygems.org/'` succeeds before bundling.
