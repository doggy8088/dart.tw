---
title: Dart SDK archive
title: Dart SDK 歸檔
short-title: Archive
short-title: 歸檔
description: Download specific stable, beta, dev, and main channel versions of the Dart SDK and the Dart API documentation.
description: 下載特定的穩定版、測試版、開發版和主分支開發版的 Dart SDK 和 Dart API 文件。
js:
- url: https://cdn.jsdelivr.net/gh/dart-lang/site-www/src/get-dart/archive/assets/download_archive.dart.js
  defer: true
- url: https://cdn.jsdelivr.net/gh/dart-lang/site-www/src/get-dart/archive/assets/install.js
  defer: true
---

Use this archive to download
[specific versions](/get-dart#release-channels) of the
[Dart SDK](/tools/sdk)
and the [Dart API documentation.]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}})

你可以在此頁面下載 [特定版本](/get-dart#release-channels)
的 [Dart SDK](/tools/sdk) 和
[Dart API 文件](({{site.dart_api}}/{{site.data.pkg-vers.SDK.channel}}))。

Want to install Dart with your OS's package manager?
[Get Dart.](/get-dart)

希望透過系統的包管理安裝 Dart？請檢視如何 [獲取 Dart SDK](/get-dart)。

{{site.alert.warn}}
  {% include_relative _sdk-terms.md %}
{{site.alert.end}}

## Stable channel

## 穩定版渠道

Stable channel builds are tested and approved for production use.

**穩定版渠道的建構是經過測試且可用於生產環境的。**

{% include_relative _archives_table.html channel="stable" %}

## Beta channel

## 測試版渠道

Beta channel builds are preview builds for the stable channel.
We recommend testing, but not releasing, your apps against beta
to preview new features or test compatibility with future releases.
Beta channel builds are not suitable for production use.

測試版渠道的建構是穩定版的前置預覽。我們推薦你使用該渠道對你的應用進行測試，
包括新特性和對未來發布內容的相容性，但不要用於釋出。
**測試版渠道的建構不適合在生產環境使用。**

{% include_relative _archives_table.html channel="beta" %}

## Dev channel

## 開發版渠道

Dev channel builds can provide early access
to new features but might contain bugs.
Dev channel builds are not suitable for production use.

開發版渠道的建構提供了早期的新特性預覽，但它們可能包含一些 BUG。
**開發版渠道的建構不適合在生產環境使用。**

{% include_relative _archives_table.html channel="dev" %}

## Main channel

## 主分支開發版渠道

Main channel builds are the latest, raw builds from
the `main` branch of the Dart SDK repository.
These are the freshest builds available,
and they're likely to contain bugs.
Main channel builds are suitable only for
experimental development use, not for production use.

主分支開發版的建構是最新且最原始的，是來自
Dart SDK 儲存庫的主分支 (`master`) 的建構。
這些建構是永遠是最新的，所以它們有可能包含多個 BUG。
**主分支開發版的建構只適用於更專業的開發，而不是生產用途的開發**。

{{site.alert.note}}

  Main channel builds are unsigned.

  主分支開發版渠道的建構是未經簽名的。

{{site.alert.end}}

To download a main channel build, use a
[main channel URL](#main-channel-url-scheme).

若你想下載主分支開發版渠道的建構，請使用
[主分支開發版渠道的連結](#main-channel-url-scheme)。

## Download URLs

## 下載連結

You can download zip files for any channel.

你可以下載任意渠道的 zip 檔案。

### Stable, beta, and dev channel URL scheme

### 穩定版、測試版和開發版的連結格式

Stable, beta, and dev channel releases
are available at URLs like the following:

穩定版、測試版和開發版渠道的釋出建構，
可以使用以下格式的連結進行下載：

{% prettify none tag=pre+code %}
https://storage.googleapis.com/dart-archive/channels/<[!stable|beta|dev!]>/release/<[!version!]>/sdk/dartsdk-<[!platform!]>-<[!architecture!]>-release.zip
{% endprettify %}

Examples:

範例：

{% prettify none tag=pre+code %}
https://storage.googleapis.com/dart-archive/channels/stable/release/2.7.2/sdk/dartsdk-windows-ia32-release.zip
https://storage.googleapis.com/dart-archive/channels/stable/release/2.1.1/sdk/dartsdk-macos-x64-release.zip
https://storage.googleapis.com/dart-archive/channels/beta/release/2.8.0-20.11.beta/sdk/dartsdk-linux-x64-release.zip
https://storage.googleapis.com/dart-archive/channels/dev/release/2.9.0-1.0.dev/sdk/dartsdk-linux-x64-release.zip
{% endprettify %}

### Main channel URL scheme

### 主分支開發版的連結格式

The latest main channel build
is available at URLs like the following:

最新的主分支開發版渠道的建構，
可以使用以下格式的連結進行下載：

{% prettify none tag=pre+code %}
https://storage.googleapis.com/dart-archive/channels/be/raw/latest/sdk/dartsdk-<[!platform!]>-<[!architecture!]>-release.zip
{% endprettify %}

Example:

範例：

{% prettify none tag=pre+code %}
https://storage.googleapis.com/dart-archive/channels/be/raw/latest/sdk/dartsdk-windows-x64-release.zip
{% endprettify %}

{{site.alert.note}}

  Main channel builds are unsigned.

  主分支開發版渠道的建構是未經簽名的。

{{site.alert.end}}
