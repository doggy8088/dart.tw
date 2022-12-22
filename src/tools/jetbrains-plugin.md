---
title: IntelliJ & Android Studio
title: 使用 IntelliJ 和 Android Studio 開發 Dart 應用
description: Use Dart with a variety of IDEs and editors from JetBrains.
description: 使用由 JetBrains 開發的各種 IDE 和編輯器開發 Dart 應用。
---

The [Dart plugin][] adds Dart support
to IntelliJ Platform-based IDEs developed by JetBrains.
These IDEs provide features unique to specific development technologies.
The IDEs recommended for Dart and Flutter development include:

Dart 外掛為 JetBrains 公司開發的 IntelliJ 系列 IDE 提供了支援，
這些系列的 IDE 大都是為不同的平台、不同的語言和技術而開發的。
推薦使用下列 IDE 都是來做 Dart 和 Flutter 開發:

- [IntelliJ IDEA][] which specializes in JVM-based language development.

  [IntelliJ IDEA][] 是專門用於基於 JVM 語言開發的 IDE。

- [WebStorm][] which specializes in web app development.

  [WebStorm][] 是專門用於 Web 開發的。

- [Android Studio][] which specializes in Android and Flutter development.

  [Android Studio][] 專門用來從事 Android 和 Flutter 應用開發。


Whichever JetBrains IDE you choose for Dart development,
this page has resources to help you get started quickly
and find more information when you need it.

不管你選擇哪一款 JetBrains 的 IDE 用於 Dart 開發，
本文所包含的資源都可以幫助你快速入門並在你需要時給你提供更多的資訊。

[IntelliJ IDEA]: https://www.jetbrains.com/idea/
[WebStorm]: https://www.jetbrains.com/webstorm/
[Android Studio]: https://developer.android.com/studio

## Getting started

## 入門

If you don't already have the IDE and the Dart SDK, get them.
Then install the Dart plugin and tell it where to find the Dart SDK.

如果你還沒有 IDE 和 Dart SDK，請先下載。然後安裝相應的 Dart 外掛並配置 Dart SDK 的路徑。

### Downloading the IDE

### 下載 IDE

Install a JetBrains IDE if you don't already have one. Choose one:

如果你還沒有一款 JetBrains 的 IDE，請選擇一款：

* [IntelliJ IDEA][IDEA-Install]{:target="_blank" rel="noopener"}
* [IntelliJ IDEA EAP][IDEA-EAP-Install]{:target="_blank" rel="noopener"}
  (獲取早期 Dart 語言特性和 IntelliJ 新功能體驗)
* [WebStorm][WS-Install]{:target="_blank" rel="noopener"}
* [Android Studio][AS-Install]{:target="_blank" rel="noopener"}
* [Another JetBrains product][Other]{:target="_blank" rel="noopener"}

[IDEA-Install]: https://www.jetbrains.com/idea/download/
[IDEA-EAP-Install]: https://www.jetbrains.com/idea/nextversion/
[WS-Install]: https://www.jetbrains.com/webstorm/download/
[AS-Install]: https://developer.android.com/studio/install
[Other]: https://www.jetbrains.com/products.html

{{site.alert.note}}
  
  The Community Edition of IntelliJ IDEA has limited functionality.
  For example, it doesn't directly support debugging web apps.
  It also has very little support for JavaScript, HTML, CSS, and YAML.
  
  IntelliJ IDEA 的社群版有功能限制。例如，它不直接支援除錯 Web 應用。
  它對 JavaScript、HTML、CSS 和 YAML 的支援也很少。
  
{{site.alert.end}}


### Downloading the Dart SDK

### 下載 Dart SDK

If you don't already have the Dart SDK,
install it.
You can get it either by itself or by downloading the Flutter SDK,
which includes the full Dart SDK.

如果你還沒有安裝 Dart SDK，安裝一下。
你可以選擇下載 Dart SDK 或者下載安裝 Flutter SDK，
Flutter SDK 已經包含了完整的 Dart SDK 。

Choose one:

選一個吧：

* [Download the Dart SDK](/get-dart)

  [下載 Dart SDK](/get-dart)

* [Download the Flutter SDK]({{site.flutter-docs}}/get-started/install)

  [下載 Flutter SDK]({{site.flutter-docs}}/get-started/install)


### Configuring Dart support

### 配置以支援 Dart 開發

Here's one way to configure Dart support:

下面是配置以支援 Dart 開發的一種方式：

<ol>
<li>
  <p>
    Start the IDE, and install the <b>Dart</b> plugin.
  </p>
  <p>
    啟動 IDE 並安裝 <b>Dart</b> 外掛。
  </p>

  <ol type="a">
    <li>From the Welcome screen, choose <b>Plugins</b>.
    
    在歡迎介面視窗，選擇 <b>Plugins</b>/
    </li>
    <li>Search for <b>Dart</b>.
    
    搜尋 <b>Dart</b> 關鍵字。
    </li>
    <li>Once you've installed the Dart plugin, restart the IDE.
    
    安裝好 Dart 外掛後，記得重啟 IDE。
    </li>
  </ol>
</li>
<br>

<li>
  <p>
    Create a new Dart project:
  </p>
  <p>
    建立新的 Dart 專案：
  </p>

  <ol type="a">
    <li>
    From the Welcome screen, click <b>New Project</b>.
    
    在歡迎視窗介面，點選 <b>New Project</b>。
    </li>
    <li>
    In the next dialog, click <b>Dart</b>.
    
    在接下來的對話方塊中點選 <b>Dart</b>。
    </li>
  </ol>
</li>
<br>

<li>
  <p>
    If you don't see a value for the <b>Dart SDK</b> path,
    enter or select it.
  </p>
  <p>
    如果 <b>Dart SDK</b> 路徑為空請先設定。
  </p>

  <p>
    For example, the SDK path might be
    <code><em>&lt;dart installation directory></em>/dart/dart-sdk</code>.
  </p>
  <p>
    例如，SDK 的路徑可能是 <code><em>&lt;Dart 安裝目錄></em>/dart/dart-sdk</code>.
  </p>

  {{site.alert.note}}
  
    The **Dart SDK** specifies the directory that
    contains the SDK's `bin` and `lib` directories;
    the `bin` directory contains tools such as `dart` and `dartaotruntime`.
    The IDE ensures that the path is valid.
    
    **Dart SDK** 路徑為包含了 `bin` 和 `lib` 目錄的路徑；
    `bin` 目錄中包含了類似 `dart` 和 `dartfmt` 的工具。
    IDE 需要確保路徑有效。
  
  {{site.alert.end}}
</li>

<li>
  <p>
    Choose a starting template.
  </p>
  <p>
    選擇一個起步模版 (starting template)。
  </p>

  <ol type="a">
    <li>To enable starting templates, click <b>Generate sample content</b>.
    
    為了啟用起步模版，點選 <b>Generate sample content</b>。
    </li>
    <li>Pick your desired template.
    
    選擇你希望使用的模版。
    </li>
  </ol>

  {{site.alert.info}}
    
    The provided templates are supplied and created
    by [`dart create`](/tools/dart-create).
    
    模版由 [`dart create`](/tools/dart-create) 命令提供和建立。
    
  {{site.alert.end}}
</li>

<li>
  <p>Click <b>Next</b> and continue project setup.</p>
  <p>點選 <b>Next</b> 繼續進行工程設定</p>
</li>
</ol>

An alternative to Step 2 is to open an existing Dart project,
and then open its `pubspec.yaml` file or any of its Dart files.

如果你已經建立過 Dart 專案，則可以在第二步的時候選擇開啟該專案，
然後選擇開啟 `pubspec.yaml` 或其它 Dart 檔案。

## Reporting issues

## 上報問題

Please report issues and feedback via the official
[JetBrains issue tracker for Dart.][]

請透過 [JetBrains 關於 Dart 的問題追蹤器][JetBrains issue tracker for Dart.]
來報告錯誤或者提供反饋。

Include details of the expected behavior, the actual behavior,
and screenshots if appropriate.

錯誤報告包括預期行為、實際行為的細節。
如果合適的話，建議提供合適的螢幕截圖。

[JetBrains issue tracker for Dart.]: https://youtrack.jetbrains.com/issues/WEB?q=Subsystem:%20Dart

## More information

## 更多資訊

See the JetBrains website for more information.

你可以檢視 JetBrains 網站獲取更多的資訊。

* [IntelliJ IDEA](https://www.jetbrains.com/idea/)
  * [Dart WebStorm Help](https://www.jetbrains.com/help/webstorm/dart.html)
  * [Features](https://www.jetbrains.com/idea/features/)
  * [Quick start](https://www.jetbrains.com/help/idea/getting-started.html)
* [Dart Plugin by JetBrains][Dart plugin]

[Dart plugin]: https://plugins.jetbrains.com/plugin/6351-dart/
