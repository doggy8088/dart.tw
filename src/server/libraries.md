---
title: Command-line and server libraries and packages
title: 命令列和伺服器的函式庫和 Package
short-title: CLI & server libraries
short-title: CLI 和 伺服器庫
description: Libraries and packages that can help you write Dart command-line & server apps.
description: 庫和 Package 可以幫助你開發命令列和伺服器應用。
---

The [Dart SDK][] contains [dart:io][] and other libraries
that provide low-level command-line & server APIs.

[Dart SDK][] 包含 [dart:io][] 和其它提供底層命令列和伺服器 API 的函式庫。

[Dart SDK]: /tools/sdk
[dart:io]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-io/dart-io-library.html

## SDK libraries

## SDK 庫

The Dart SDK contains dart:io and other libraries
that provide low-level web APIs.

Dart SDK 包含 dart:io 和其它提供底層 web API 的函式庫。

[The dart:io section](/guides/libraries/library-tour#dartio) of the library tour
<br> An example-driven tour of using the dart:io library.
  Topics include working with files & directories, and making & handling
  HTTP requests.

函式庫概覽的 [dart:io 小節](/guides/libraries/library-tour#dartio)：
<br> 一個使用 dart:io 庫的範例驅動概覽。主要包括如何使用檔案和目錄以及發起和處理 HTTP 請求。

[dart:io API reference][dart:io]
<br> Complete reference documentation for the dart:io library.

[dart:io API 參考][dart:io]：
<br> 完整的 dart:io 庫參考文件。


## Community packages

## 社群貢獻的一些三方 package

The [pub.dev site]({{site.pub}}) allows you to search for packages
that support command-line and server apps
by specifying the platforms your app needs to support.
You can also search for words that describe the functionality you need.

[pub.dev 網站]({{site.pub}}) 可以透過選擇「命令列」或「伺服器端」來檢索和過濾平台支援，
你也可以搜尋描述所需功能的關鍵字。

### Command-line packages

### 命令列相關的 package

Command-line apps often use the following packages,
in addition to [general-purpose packages][] such as `archive`, `intl`, and `yaml`:

命令列應用常常使用下述列表中列出的 package，
除此之外還有像 `archive`、`intl` 和 `yaml` 這樣的 [通用 package][general-purpose packages]：

| **Package**                   | **Description** |

| **套件名稱**                   | **描述** |

| [args]({{site.pub-pkg}}/args) | Parses raw command-line arguments into a set of options and values. |

| [args]({{site.pub-pkg}}/args) | 將原始命令列引數解析為一組選項和值。|

| [cli_util]({{site.pub-pkg}}/cli_util) | Provides utilities for building command-line apps. |

| [cli_util]({{site.pub-pkg}}/cli_util) | 提供用於建構命令列應用程式的工具。|

| [completion]({{site.pub-pkg}}/completion) | Adds command-line completion to apps that use the `args` package. |

| [completion]({{site.pub-pkg}}/completion) | 向使用 `args` 套件的應用中新增命令列完成功能。|

| [path]({{site.pub-pkg}}/path) | Provides comprehensive, cross-platform operations for manipulating paths. |

| [path]({{site.pub-pkg}}/path) | 為操作路徑提供全面的跨平臺操作。|

| [usage]({{site.pub-pkg}}/usage) | Wraps Google Analytics. |

| [usage]({{site.pub-pkg}}/usage) | 新增 Google 分析。|
{:.table .table-striped .nowrap}

### Server packages

### 伺服器相關的 package

Server apps can choose from many packages, in addition to
the packages listed in the previous table
and [general-purpose packages][] such as `logging`:

伺服器應用可選擇使用的 package 更多一些，
除了上面上面表格中列出的外還支援比如 `logging` 這樣的 [通用 package][general-purpose packages]：

| **Package**                   | **Description** |
| [crypto]({{site.pub-pkg}}/crypto) | Implements cryptographic hashing functions for algorithms such as SHA-1, SHA-256, MD5, and HMAC. |
| [crypto]({{site.pub-pkg}}/crypto) | 為 SHA-1、SHA-256、MD5 和 HMAC 等演算法實現加密雜湊雜湊函式。|
| [grpc]({{site.pub-pkg}}/grpc) | Implements [gRPC][] a high performance, open source, general RPC framework that puts mobile and HTTP/2 first. |
| [grpc]({{site.pub-pkg}}/grpc) | 實現 [gRPC][]，將移動和 HTTP/2 放在首位的高效能開源通用的 RPC 框架。|
| [shelf]({{site.pub-pkg}}/shelf) | Provides a model for web server middleware that encourages composition and easy reuse. |
| [shelf]({{site.pub-pkg}}/shelf) | 為 Web 伺服器中介軟體提供一個模型，該模型促成組合和易於重用。|
{:.table .table-striped .nowrap}

[general-purpose packages]: /guides/libraries/useful-libraries#general-purpose-packages
[gRPC]: https://grpc.io/
