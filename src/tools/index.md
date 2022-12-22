---
title: Tools
title: Dart 的開發工具
description: The tools that support the Dart language.
description: 支援 Dart 語言的開發工具。
---

When you're ready to create an app, get the SDK and tools for your app
type. If you aren't sure which tools you need, **get the Flutter SDK.**

當你準備好建立一個應用時，請根據你要建立的應用型別獲取相應的 SDK 和工具。

<div class="table-wrapper" markdown="1">
|------------+-----------------------------------+--------------------------|
| App type   | Get started instructions          | Tool information         |
|   應用型別 |             入門說明              |          工具資訊        |
|------------|-----------------------------------|--------------------------|
| Flutter (mobile and more) | [Install Flutter]({{site.flutter-docs}}/get-started/install) | [Flutter tools]({{site.flutter-docs}}/using-ide) |
| Flutter (移動應用和其他平台) | [安裝 Flutter]({{site.flutter-docs}}/setup) | [Flutter tools]({{site.flutter-docs}}/using-ide) |
| Web app (non-Flutter) | [Install the Dart SDK](/tools/sdk) | [General-purpose tools][] and [web tools](#web) |
| Web 應用 (非 Flutter) | [安裝 Dart SDK](/tools/sdk) | [通用工具][General-purpose tools] and [Web 工具](#web) |
| Server or command line | [Install the Dart SDK](/tools/sdk) | [General-purpose tools][] and [specialized tools](#server) |
| 伺服器或命令列應用 | [安裝 Dart SDK](/tools/sdk) | [通用工具][General-purpose tools] 和 [專業工具](#server) |
{:.table .table-striped}
</div>

[General-purpose tools]: #general-purpose-tools

{{site.alert.note}}

  The Flutter SDK includes the full Dart SDK.

  Flutter SDK 包含完整的 Dart SDK。

{{site.alert.end}}

## General-purpose tools

## 通用工具

The following tools support the Dart language on all platforms.

下述工具支援使用 Dart 語言的全部平台。

* [DartPad](#dartpad)
* [IDEs and editors](#ides-and-editors)

  [IDE 和編輯器](#ides-and-editors)

* [Command-line tools](#cli)

  [命令列工具](#cli)

### DartPad

<img src="/assets/img/dartpad-hello.png" alt="DartPad Hello World" width="200px" align="right" />
[DartPad](/tools/dartpad) is
a great, no-download-required way to learn Dart syntax
and to experiment with Dart language features.
It supports Dart's core libraries,
except for VM libraries such as `dart:io`.

<img src="/assets/img/dartpad-hello.png" alt="DartPad Hello World" width="200px" align="right" />
[DartPad](/tools/dartpad) 是一個用於學習 Dart 語法以及
體驗 Dart 語言功能的線上工具。
它支援 Dart 的核心庫，但不支援類似 dart:io 這樣的 VM 庫。

### IDEs and editors

### IDE 和編輯器

Dart plugins exist for these commonly used IDEs.

這些常用的 IDE 都帶有 Dart 外掛。

<ul class="col2">
<li>
<img src="/assets/img/tools/android_studio.svg" width="48" alt="Android Studio logo">
<a href="/tools/jetbrains-plugin"><b>Android Studio</b></a>
</li>
<li>
<img src="/assets/img/tools/intellij-idea.svg" width="48" alt="IntelliJ logo">
<a href="/tools/jetbrains-plugin"><b>IntelliJ IDEA<br>
(以及其它的 JetBrains IDE)</b></a>
</li>
<li>
<img src="/assets/img/tools/vscode.svg"
     width="48" alt="Visual Studio Code logo">
<a href="/tools/vs-code"><b>Visual Studio Code</b></a>
</li>
</ul>

The following Dart plugins are also available,
thanks to the Dart community.

下面的 Dart 外掛也是可用的，感謝 Dart 社群。

<ul class="col2">
<li>
<img src="/assets/img/tools/emacs.png" alt="Emacs logo">
<a class="no-automatic-external" href="https://github.com/nex3/dart-mode"><b>Emacs</b></a>
</li>
<li>
<img src="/assets/img/tools/vim.png" alt="Vim logo">
<a class="no-automatic-external" href="https://github.com/dart-lang/dart-vim-plugin"><b>Vim</b></a>
</li>
<li>
<img src="/assets/img/tools/eclipse-logo.png" alt="Eclipse logo">
<a class="no-automatic-external" href="https://github.com/eclipse/dartboard"><b>Eclipse</b></a>
</li>
</ul>

A [Language Server Protocol implementation][LSP] is also available for
[LSP-capable editors][] that don't have specific Dart extensions.

[語言伺服器協議實現][LSP] 同樣適用於沒有特定的 Dart 擴充但
[支援 LSP 的編輯器][LSP-capable editors] 。

[LSP]: https://github.com/dart-lang/sdk/blob/main/pkg/analysis_server/tool/lsp_spec/README.md
[LSP-capable editors]: https://microsoft.github.io/language-server-protocol/implementors/tools/

### Command-line tools {#cli}

### 命令列工具 {#cli}

The Dart SDK includes the following general-purpose `dart` tool:

Dart SDK 中包含下面的 `dart` 工具：

[`dart`](/tools/dart-tool)
<br> A command-line interface (CLI) for creating, formatting, analyzing,
  testing, documenting, compiling, and running Dart code,
  as well as working with the [pub package manager](/guides/packages).

[`dart`](/tools/dart-tool)
<br> 用於建立、格式化、分析、測試、編譯和執行 Dart 程式碼的命令列工具 (CLI)，
  同時包含 [pub package 管理器](/guides/packages)。


### Debugging

### 除錯工具

[Dart DevTools](/tools/dart-devtools)
<br>A suite of debugging and performance tools.

[Dart 開發者工具](/tools/dart-devtools) 
<br> 一個工具套裝幫助除錯和效能測試。

## Tool for developing web apps {#web}

## 開發 Web 應用的工具 {#web}

The following tool supports developing web apps:

下面工具主要針對 Web 應用開發：

[`webdev`](/tools/webdev)
<br> A CLI to build and serve Dart web apps.

[`webdev`](/tools/webdev)
<br> 用於建構和提供 Dart Web 應用開發的命令列介面 (CLI) 。

## Tools for developing command-line apps and servers {#server}

## 開發命令列應用和伺服器的工具 {#server}

The following tools support developing or running
command-line apps and servers:

下面的工具對開發或執行命令列應用和伺服器有特別的支援：

[`dart run`](/tools/dart-run)
<br> Use the `dart run` command to run uncompiled Dart command-line apps
  and some kinds of snapshots.

[`dart`](/tools/dart-vm)
<br> 透過 [`dart run`](/tools/dart-run) 命令來執行未編譯的 Dart 命令列應用和一些其他的快照。

[`dartaotruntime`](/tools/dartaotruntime)
<br> Use this Dart runtime to run AOT snapshots.

[`dartaotruntime` 命令](/tools/dartaotruntime)
<br> Dart 執行時環境，可以執行 AOT 快照。
