---
title: Get the Dart SDK
title: 獲取 Dart SDK
description: Get the libraries and command-line tools that you need to develop Dart web, command-line, and server apps.
description: 獲得 Dart 命令列工具和庫，用以編寫 Dart web，命令列以及伺服器端應用。
js:
- url: /get-dart/archive/assets/install.js
  defer: true
---

This page describes how to download the Dart SDK.
The Dart SDK has the libraries and command-line tools that you need to develop
Dart command-line, server, and non-Flutter web apps.
For details, see the [Dart SDK overview](/tools/sdk).

本頁面主要引介紹如何下載 Dart SDK，Dart SDK 包含了各種
庫和命令列工具幫助你建構 Dart 命令列、伺服器端、以及 Web (非 Flutter) 應用。
瞭解更多，請檢視 [Dart SDK 概覽](/tools/sdk)。

## Installing the Dart SDK {#install}

## 安裝 Dart SDK {#install}

As the following instructions show,
you can use a package manager
to easily install and update a stable channel Dart SDK.
Alternatively, you can
[build the SDK from source][],
grab a [Dart Docker image][], or
install from [any release channel](#release-channels) by
[downloading the SDK as a zip file][].

如下所述，你可以使用包管理輕鬆地安裝和更新 Dart SDK。
你也可以 以 [編譯 SDK 原始碼][build the SDK from source] 的形式安裝，
也可以在我們的 [各個釋出渠道里](#release-channels) 
[下載 SDK 的 zip 壓縮檔案][downloading the SDK as a zip file] 。

{% comment %}
NOTE to editors: Keep the zip file link as the last thing in the paragraph,
so it's easy to find (but not more tempting than package managers).
{% endcomment %}

*Note*: The Flutter SDK includes the full Dart SDK,
and has Dart's [`dart`](/tools/dart-tool) command-line interface
in its `bin` folder.

{{site.alert.warn}}
  {% include_relative archive/_sdk-terms.md %}
{{site.alert.end}}

<ul class="tabs__top-bar">
  <li class="tab-link current" data-tab="tab-sdk-install-windows">Windows</li>
  <li class="tab-link" data-tab="tab-sdk-install-linux">Linux</li>
  <li class="tab-link" data-tab="tab-sdk-install-mac">macOS</li>
</ul>
<div id="tab-sdk-install-windows" class="tabs__content current" markdown="1">
{% include_relative _windows.md %}
</div>
<div id="tab-sdk-install-linux" class="tabs__content" markdown="1">
{% include_relative _linux.md %}
</div>
<div id="tab-sdk-install-mac" class="tabs__content" markdown="1">
{% include_relative _mac.md %}
</div>

## System requirements

## 系統要求

The Dart SDK is supported on Windows, Linux, and macOS.

Dart SDK 支援 Windows、Linux 和 macOS。

### Windows

* **Supported versions:** Windows 10 and 11.

  **支援的版本：**Windows 10 和 11。

* **Supported architectures:** x64, IA32, ARM64.<br>
  Support for ARM64 is experimental,
  and is available only in the dev channel.

  **支援的架構：** x64、ia32、ARM64<br>
  ARM64 的支援處於實驗性階段，僅在 Dev 渠道可用。

### Linux

* **Supported versions:** [Debian stable][] and [Ubuntu LTS][] under standard support.

  **支援的版本：**已支援標準版的 [Debian stable][] 以及 [Ubuntu LTS][]。

* **Supported architectures:** x64, IA32, ARM64, ARM, RISC-V (RV64GC).<br>
  Support for RISC-V is experimental, 
  and is available only in the dev channel.

  **支援的架構：** x64、IA32、ARM64、ARM、RISC-V (RV64GC)。<br>
  RISC-V 的支援目前還在實驗階段，僅在 Dev 渠道可用。

{{site.alert.note}}

  The arm support requires glibc 2.23 or newer due to a
  [dynamic linker bug](https://sourceware.org/bugzilla/show_bug.cgi?id=14341).

  由於 [動態連結的缺陷](https://sourceware.org/bugzilla/show_bug.cgi?id=14341)，
  Arm 的支援需要使用 glibc 2.23 或更新的版本。

{{site.alert.end}}

### macOS

* **Supported versions:** Latest three major versions.
Dart supports the following macOS versions as of November 2022:
  - macOS 11 (Big Sur)
  - macOS 12 (Monterey)
  - macOS 13 (Ventura)

  **支援的版本：**最新的三個主要版本。
  截止 2022 年 11 月，支援 macOS 的以下版本 (macOS 10.15 的支援已被移除)：
  - macOS 11 (Big Sur)
  - macOS 12 (Monterey)
  - macOS 13 (Ventura)

* **Supported architectures:** x64, ARM64.

  **支援的架構：** x64、ARM64。

## About release channels and version strings {#release-channels}

## 關於發行渠道和版本字串

The Dart SDK has three release channels:

Dart SDK 有三個釋出渠道：

* **Stable** channel: **stable releases**, updated roughly every three months;
  currently `[calculating]`{:.editor-build-rev-stable}.

  穩定版 (Stable) 渠道：**穩定發行版**，每 **三個月** 更新一次；
  當前版本 `[calculating]`{:.editor-build-rev-stable}.
  
  Stable releases are suitable for production use.
  
  穩定版可適用於生產環節。
  
* **Beta** channel: **preview releases**, usually updated every month;
  currently `[calculating]`{:.editor-build-rev-beta}.

  測試版 (Beta) 渠道：也稱 **發行預覽版**，通常 **每月** 更新一次；
  當前版本 `[calculating]`{:.editor-build-rev-dev}.
  
  Beta channel builds are preview builds for the stable channel. We recommend
  testing, but not releasing, your apps against beta to preview new features or
  test compatibility with future releases.
  
  測試版渠道的建構是穩定版渠道的“預覽版建構”。我們推薦您使用這個渠道的 SDK、
  進行測試，但是不建議您的應用釋出，您可以用這個渠道的建構
  預覽新功能或測試與未來版本的相容性。
  
* **Dev** channel: **prereleases**, usually updated twice a week;
  currently `[calculating]`{:.editor-build-rev-dev}.

  開發版 (Dev) 渠道：也稱 **預發行版**，通常每 **雙週** 更新一次；
  當前版本 `[calculating]`{:.editor-build-rev-dev}.
  
  Dev channel releases are the most current with latest changes, may be broken,
  are unsupported, and may contain unvetted breaking changes.
  
  開發版渠道的建構包含最新的更新，也可能本身就是不完善的，這個版本我們不受支援，
  並且可能會包含未經稽核的重大更改 (breaking changes)。

**Stable** channel releases of the Dart SDK have `x.y.z` version strings like
`1.24.3` and `2.1.0`. They consist of dot-separated integers, with no hyphens or
letters, where `x` is the major version, `y` is the minor version, and `z` is
the patch version.

Dart SDK **穩定版** 建構渠道的版本號設定為 `x.y.z`，比如 `1.24.3` 和 `2.1.0`。
它們由點分隔的整陣列成，沒有連字元或字母，
其中 `x` 是主版本，`y` 是次要版本，而 `z` 是補丁版本。

**Beta** and **dev** channel releases of the Dart SDK (non-stable releases) have
`x.y.z-a.b.<beta|dev>` versions like `2.8.0-20.11.beta`. The part before the
hyphen follows the stable version scheme, `a` and `b` after the hyphen are the
prerelease and prerelease patch versions, and `beta` or `dev` is the channel.

Dart SDK 的非穩定版 (**測試版 (Beta)** 和 **開發版 (Dev)**) 建構渠道
的版本號設定為：`x.y.z-a.b.<beta|dev>`，比如：`2.8.0-20.11.beta`。
連字元前的部分 (`x.y.z`) 遵循穩定版本方案，
連字元後的 `a` 和 `b` 分別是預發行版和預發行補丁版本，
而 `beta` 或 `dev` 是建構渠道通道標示。

You can get stable channel releases using
the [instructions above](#install), or you can
get stable, beta, or dev channel releases
using [a package manager][] or [Dart Docker image][], or
by [downloading the SDK as a zip file][].

你可以透過本頁面 [上方安裝部分](#install) 的介紹來獲取穩定版渠道的 Dart，
或者透過 [任一個包管理器 (如 brew、choco、apt get)][a package manager][]
或者透過 Dart 的 [Docker 映象][Dart Docker image] 來獲取
穩定版 (Stable)、測試版 (Beta)、開發版 (Dev) 渠道的 Dart SDK，
再或者，你也可以直接 [下載各個版本的 SDK 壓縮包][downloading the SDK as a zip file]。

For more information, see the [Dart 2 page.][Dart 2]

你可以查閱 [Dart 2 相關頁面][Dart 2] 獲取更多資訊。

[SDK constraints]: /tools/pub/pubspec#sdk-constraints
[Dart 2]: /dart-2
[build the SDK from source]: https://github.com/dart-lang/sdk/wiki/Building
[Dart libraries]: /guides/libraries/library-tour
[Dart Docker image]: https://hub.docker.com/_/dart
[downloading the SDK as a zip file]: /get-dart/archive
[Debian stable]: https://www.debian.org/releases
[Ubuntu LTS]: https://wiki.ubuntu.com/Releases
[flutter]: https://flutter.dev/docs/get-started/install
[site SDK version]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/{{site.data.pkg-vers.SDK.vers}}/index.html
[a package manager]: https://github.com/dart-lang/sdk/wiki/Installing-beta-and-dev-releases-with-brew,-choco,-and-apt-get
