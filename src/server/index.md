---
title: Command-line and server apps
title: 命令列和伺服器端應用
short-title: CLI & server apps
short-title: 命令列和伺服器端應用
description: All things relating to command-line and server apps.
description: 與命令列和伺服器端應用有關的內容。
toc: false
---

This page points to tools and documentation
that can help you develop command-line and server apps.

本文向你展示可以幫助你開發命令列式和伺服器應用的相關工具和文件。

<p class="text-center">
  <a href="/tutorials/server/get-started" class="btn btn-primary btn-lg">開始使用</a>
</p>


## Tools

## 工具

[DartPad](/tools/dartpad)
<br> Handy for both beginners and experts,
  DartPad lets you try out language features and dart:* APIs.

[DartPad](/tools/dartpad)：
<br> 不管是對新手還是專家來說都易於使用的工具，
  DartPad 可以讓你嘗試語言相關功能和 dart:* 的 API。

  {{site.alert.note}}

    DartPad does **not** support using VM libraries, such as `dart:io`,
    or importing libraries from packages
    besides the [currently supported packages][].

    DartPad 尚且 **不支援** 使用 VM 庫，比如 `dart:io`，
    也不支援從匯入其他 package，除了
    目前已經支援了匯入 `bloc`、`characters`、`collection` 等 package，
    除了 [列舉出的這些支援的 package][currently supported packages] 之外，
    DartPad 尚且 **不支援** 匯入其他 package。
    
  {{site.alert.end}}

[currently supported packages]: https://github.com/dart-lang/dart-pad/wiki/Package-and-plugin-support#currently-supported-packages

[Dart SDK](/tools/sdk)
<br> [Install the Dart SDK](/get-dart) to get the core Dart
  libraries and [tools](/tools).

[Dart SDK](/tools/sdk)：
<br> [安裝 Dart SDK](/get-dart) 以獲取 Dart 的核心庫和 [工具](/tools)。

More tools
<br> The [Tools](/tools) page links to generally useful tools,
  such as Dart plugins for your favorite IDE or editor.

更多工具：
<br> [工具](/tools) 介面連結了一些有用的工具，比如你喜歡的 IDE 或編輯器的 Dart 外掛。

## Tutorials

## 課程

You might find the following tutorials helpful.

你可能會發現以下的課程會比較有用。

[Get started](/tutorials/server/get-started)
<br> Learn how to use the Dart SDK to develop command-line and server apps.

[開始](/tutorials/server/get-started)：
<br> 學習如何使用 Dart SDK 開發命令列或伺服器應用。

[gRPC Quickstart](https://grpc.io/docs/languages/dart/quickstart/)
<br> Walks you through running and modifying a client-server example that uses the gRPC framework.

[gRPC 快速上手](https://grpc.io/docs/quickstart/dart.html)：
<br> 手把手教你使用 gRPC 框架執行和修改一個客戶端-伺服器範例。

[Write command-line apps](/tutorials/server/cmdline)
<br> Introduces dart:io and the args package.

[開發 command-line 應用](/tutorials/server/cmdline)：
<br> dart:io 和 args package 的介紹。

[Write HTTP servers](/tutorials/server/httpserver)
<br> Features dart:io and the http_server package.

[開發 HTTP 客戶端和伺服器端](/tutorials/server/httpserver)：
<br> dart:io 和 http_server 套件的相關功能。

## More resources

## 更多資源

[Dart API]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}})
<br> API reference for dart:* libraries.

[Dart API]({{site.dart_api}}/{{site.data.pkg-vers.SDK.channel}})：
<br> dart:* 庫的 API 參考。

[dart:io section of the library tour](/guides/libraries/library-tour/#dartio)
<br> Shows how to use the major features of the dart:io library.
  You can use the dart:io library in command-line scripts, servers, and
  non-web [Flutter apps.]({{site.flutter}})

[函式庫概覽的 dart:io 小節](/guides/libraries/library-tour/#dartio)：
<br> 向你展示如何使用 dart:io 庫的主要功能。
你可以在命令列指令碼、伺服器應用以及不包含 Web 平台的
[Flutter 移動應用]({{site.flutter}}) 中使用 dart:io 庫。
