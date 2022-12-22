---
title: Dart overview
title: Dart 概覽
description: A short introduction to Dart
description: Dart 的簡單介紹
js: [{url: 'https://dartpad.dev/inject_embed.dart.js', defer: true}]
---

<img 
  style="padding: 30px; float: right; width: 300px" 
  src="/assets/img/logo_lockup_dart_horizontal.png" 
  alt="Dart product logo">

Dart is a client-optimized language for developing fast apps on any platform.
Its goal is to offer the most productive programming language for
multi-platform development, paired with a
[flexible execution runtime platform](#platform) for app frameworks.

Dart 是一種針對客戶最佳化的語言，可在任何平臺上開發快速的應用程式。
其目標是為多平臺開發提供最高效的程式語言，
併為應用程式框架搭配了
[靈活的執行時執行平台](#platform)。

Languages are defined by their _technical envelope_—the 
choices made during development that
shape the capabilities and strengths of a language.
Dart is designed for a technical envelope that is
particularly suited to client development,
prioritizing both development (sub-second stateful hot reload) and
high-quality production experiences across
a wide variety of compilation targets (web, mobile, and desktop).

通常來說，程式語言會包含一些 **技術壁壘**，即語言在設計中的抉擇決定了其功能和優勢。
Dart 的語言設計針對客戶端開發，它優先考慮多平臺 (Web，移動端和桌面端) 上的開發 (亞秒級的狀態熱重載) 和高品質生產環境體驗。

Dart also forms the foundation of [Flutter]({{site.flutter}}).
Dart provides the language and runtimes that power Flutter apps,
but Dart also supports many core developer tasks like
formatting, analyzing, and testing code.

Dart 也是 [Flutter]({{site.flutter}}) 的基礎。
Dart 作為 Flutter 應用程式的程式語言，為驅動應用執行提供了環境，
同時 Dart 還支援許多核心的開發任務，例如格式化，分析和程式碼測試。

## Dart: The language {#language}

## Dart 語言 {#language}

The Dart language is type safe;
it uses static type checking to ensure that
a variable’s value _always_ matches the variable’s static type.
Sometimes, this is referred to as sound typing.
Although types are mandatory,
type annotations are optional because of type inference.
The Dart typing system is also flexible,
allowing the use of a `dynamic` type combined with runtime checks,
which can be useful during experimentation or
for code that needs to be especially dynamic.

Dart 語言是型別安全的；
它使用靜態型別檢查來確保變數的值 **始終** 與變數的靜態型別相匹配。
這也叫健全型別。
儘管型別是強制性的，但由於 Dart 支援型別推斷，型別註釋仍是可選的。
Dart 的型別系統也很靈活，允許結合使用 dynamic 型別與執行時期檢查，
在測試開發期間，或是遇到需要特別指定為動態型別的程式碼時，這項特性很有幫助。

Dart offers [sound null safety](/null-safety),
meaning that values can’t be null unless you say they can be.
With sound null safety, Dart can protect you from
null exceptions at runtime through static code analysis.
Unlike many other null-safe languages,
when Dart determines that a variable is non-nullable,
that variable is _always_ non-nullable.
If you inspect your running code in the debugger,
you’ll see that non-nullability is retained at runtime
(hence _sound_ null safety).

與其他許多空安全語言不同，
Dart 提供 [健全的空值安全](/null-safety)，
這意味著只有您宣告值可以為空的情況下，值才可以為空；
當 Dart 確定變數不可為空時，
該變數 **永遠** 不可為空。
憑藉健全的空值安全，Dart 可以透過靜態程式碼分析在執行時保護您免受空值例外的影響。
如果你在偵錯程式中審查正在執行的程式碼，你會看到不可為空性仍在執行時被保留 (所以是完全空值安全)。

The following code sample showcases several Dart language features,
including libraries, async calls, nullable and non-nullable types,
arrow syntax, generators, streams, and getters.
To find examples of using additional Dart features,
see the [samples page](/samples).
To learn more about the language, take the [Dart language
tour](/guides/language/language-tour).

以下程式碼範例展示了 Dart 語言的一些功能，包括庫、非同步呼叫、可空和不可空的型別、箭頭語法、產生器、流和 getter。
想要查詢使用更多 Dart 功能的範例，請參閱 [範例頁面](/samples)。
要了解有關 Dart 語言的更多資訊，請參閱 [Dart 語言之旅](/guides/language/language-tour)。

<?code-excerpt "misc/lib/overview_pi.dart"?>
```dart:run-dartpad:ga_id-overview
import 'dart:math' show Random;

void main() async {
  print('Compute π using the Monte Carlo method.');
  await for (final estimate in computePi().take(100)) {
    print('π ≅ $estimate');
  }
}

/// Generates a stream of increasingly accurate estimates of π.
Stream<double> computePi({int batch = 100000}) async* {
  var total = 0; // Inferred to be of type int
  var count = 0;
  while (true) {
    final points = generateRandom().take(batch);
    final inside = points.where((p) => p.isInsideUnitCircle);

    total += batch;
    count += inside.length;
    final ratio = count / total;

    // Area of a circle is A = π⋅r², therefore π = A/r².
    // So, when given random points with x ∈ <0,1>,
    // y ∈ <0,1>, the ratio of those inside a unit circle
    // should approach π / 4. Therefore, the value of π
    // should be:
    yield ratio * 4;
  }
}

Iterable<Point> generateRandom([int? seed]) sync* {
  final random = Random(seed);
  while (true) {
    yield Point(random.nextDouble(), random.nextDouble());
  }
}

class Point {
  final double x;
  final double y;

  const Point(this.x, this.y);

  bool get isInsideUnitCircle => x * x + y * y <= 1;
}
```

{{site.alert.info}}

  This example is running in an embedded [DartPad](/tools/dartpad).
  You can also
  <a href="{{site.dartpad}}/bc63d212c3252e44058ff76f34ef5730"
  target="_blank">open this example in its own window</a>.

  此範例在嵌入式 [DartPad](/tools/dartpad) 中執行。您也可以
  <a href="{{site.dartpad}}/4d688b6e468fb4c53d312250f557ec5c"
  target="_blank" rel="noopener">在此範例自己的視窗中開啟它</a>。

{{site.alert.end}}

## Dart: The libraries {#libraries}

## Dart 庫 {#libraries}

Dart has [a rich set of core libraries](/guides/libraries),
providing essentials for many everyday programming tasks:

Dart 擁有 [豐富的核心庫](/guides/libraries)，為許多日常程式設計任務提供了必要工具：

* Built-in types, collections, and other core functionality for
  every Dart program
  (`dart:core`)

  為每個 Dart 程式提供的內建型別，集合與其他核心功能
  (`dart:core`)

* Richer collection types such as queues, linked lists, hashmaps, and
  binary trees
  (`dart:collection`)

  更豐富的集合型別，諸如佇列、連結列表、雜湊圖和二叉樹
  (`dart:collection`)

* Encoders and decoders for converting between different data representations,
  including JSON and UTF-8
  (`dart:convert`)

  用於在不同的資料表示形式之間進行轉換編碼器和解碼器，
  包括 JSON 和 UTF-8
  (`dart:convert`)

* Mathematical constants and functions, and random number generation
  (`dart:math`)

  數學常數和函式，以及隨機數產生
  (`dart:math`)

* File, socket, HTTP, and other I/O support for non-web applications
  (`dart:io`)

  為非 Web 應用程式提供的檔案、套接字、HTTP 和其他 I/O 支援
  (`dart:io`)

* Support for asynchronous programming,
  with classes such as `Future` and `Stream`
  (`dart:async`)

  非同步程式設計支援，比如 `Future` 和 `Stream` 類
  (`dart:async`)

* Lists that efficiently handle fixed-sized data
  (for example, unsigned 8-byte integers) and SIMD numeric types
  (`dart:typed_data`)

  能夠有效處理固定大小的資料（例如，無符號的 8 位元組整數）
  和 SIMD 數字型別的列表
  (`dart:typed_data`)

* Foreign function interfaces for interoperability with
  other code that presents a C-style interface
  (`dart:ffi`)

  用於提供 C 語言風格程式碼互通性支援的外部函式介面
  (`dart:ffi`)

* Concurrent programming using _isolates_—independent workers
  that are similar to threads but
  don't share memory, communicating only through messages
  (`dart:isolate`)

  使用 **isolates** 的併發程式設計 —
  這些獨立的工作程式與執行緒相似但它們不共享記憶體並僅透過訊息進行通訊
  (`dart:isolate`)

* HTML elements and other resources for web-based applications that need to
  interact with the browser and the Document Object Model (DOM)
  (`dart:html`)

  基於 Web 的應用程式中需要與瀏覽器和文件物件模型
  (DOM) 互動的 HTML 元素和其他資源
  (`dart:html`)

Beyond the core libraries, many APIs are provided through
a comprehensive set of packages.
The Dart team publishes many useful supplementary packages,
such as these:

除核心函式庫外，Dart 還透過一整套軟體套件提供了許多 API。
Dart 團隊釋出了許多有用的補充套件，
例如：

* [characters]({{site.pub-pkg}}/characters)

  [characters (字元)]({{site.pub-pkg}}/characters)

* [intl]({{site.pub-pkg}}/intl) 

  [intl (國際化)]({{site.pub-pkg}}/intl) 

* [http]({{site.pub-pkg}}/http)

  [http (http 請求)]({{site.pub-pkg}}/http)

* [crypto]({{site.pub-pkg}}/crypto)

  [crypto (雜湊加密)]({{site.pub-pkg}}/crypto)

* [markdown]({{site.pub-pkg}}/markdown)

Additionally, third-party publishers and the broader community
publish thousands of packages, with support for features like these:

此外，第三方釋出者和更廣泛的社群也
釋出了上千個軟體套件，支援諸如此類功能：

* [XML]({{site.pub-pkg}}/xml) 

* [Windows integration]({{site.pub-pkg}}/win32)

  [Windows integration (Windows API 呼叫) ]({{site.pub-pkg}}/win32)

* [SQLite]({{site.pub-pkg}}/sqflite_common)

* [compression]({{site.pub-pkg}}/archive)

  [compression (壓縮)]({{site.pub-pkg}}/archive)

To see a series of working examples featuring the Dart core libraries,
take the [library tour](/guides/libraries/library-tour).
To find additional APIs, see the
[commonly used packages page](/guides/libraries/useful-libraries).

你可以存取 [函式庫概覽](/guides/libraries/library-tour)，檢視關於 Dart 核心函式庫的一系列範例。
如果你想要查詢其他 API，請參見 [常用軟體包頁面](/guides/libraries/useful-libraries)。

## Dart: The platforms {#platform}

## Dart 平台 {#platform}

Dart's compiler technology lets you run code in different ways:

Dart 的編譯器技術可讓您以不同的方式執行程式碼：

* **Native platform**: For apps targeting mobile and desktop devices,
  Dart includes both a Dart VM with just-in-time (JIT) compilation and
  an ahead-of-time (AOT) compiler for producing machine code.

  **原生平台**：針對面向移動和桌面裝置的應用程式，
  Dart 擁有具有即時 (JIT) 編譯功能的 Dart VM 和用於產生機器程式碼的提前 (AOT) 編譯器。

* **Web platform**: For apps targeting the web, Dart can compile for
  development or production purposes. Its web compiler translates Dart
  into JavaScript.

  **Web 平台**：Dart 可用於編譯開發和生產階段的面向 Web 的應用，
  它的 Web 編譯器可以將 Dart 轉換為 JavaScript。

<img 
  src="/assets/img/Dart-platforms.svg" 
  width="800" 
  alt="An illustration of the targets supported by Dart">

The [Flutter framework]({{site.flutter}}) is a popular,
multi-platform UI toolkit that's powered by the Dart platform,
and that provides tooling and UI libraries to build UI experiences that run
on iOS, Android, macOS, Windows, Linux, and the web.

[Flutter 框架]({{site.flutter}}) 是一款流行的多平臺 UI 工具套件，由 Dart 語言強力驅動，
提供一套工具和 UI 庫，幫助開發者們在 iOS、Android、macOS、Windows、Linux 和 Web 平台
建構優秀的 UI 體驗。

#### Dart Native (machine code JIT and AOT) {#native-platform}

#### 原生平台的 Dart (JIT 和 AOT 機器碼) {#native-platform}

During development, a fast developer cycle is critical for iteration.
The Dart VM offers a just-in-time compiler (JIT) with
incremental recompilation (enabling hot reload), live metrics collections
(powering [DevTools](/tools/dart-devtools)), and rich debugging support.

在開發過程中，快速的開發週期對於迭代至關重要。
Dart VM 提供了一個即時編譯器 (JIT)，編譯器擁有增量重編譯功能 (支援熱重載)、
執行資料收集（用於驅動 [DevTools](/tools/dart-devtools)）
以及豐富的開發除錯支援。

When apps are ready to be deployed to production—whether you're
publishing to an app store or deploying to a production backend—the 
Dart ahead-of-time (AOT) compiler can compile to native ARM or x64
machine code. Your AOT-compiled app launches with consistent, short
startup time.

當應用程式可以部署到生產環境時 (無論是釋出到應用商店還是部署到生產後端)，
Dart AOT 編譯器可以編譯成原生的 ARM 或 x64 的機器碼。
經過 AOT 編譯的應用程式將穩定快速地啟動。

The AOT-compiled code runs inside an efficient Dart runtime that
enforces the sound Dart type system and
manages memory using fast object allocation and a
[generational garbage collector](https://medium.com/flutter-io/flutter-dont-fear-the-garbage-collector-d69b3ff1ca30).

經過 AOT 編譯的程式碼會在高效的 Dart 執行環境中執行，該執行環境擁有健全的 Dart 型別系統，
並使用快速物件分配和
[分代垃圾收集器](https://medium.com/flutter-io/flutter-dont-fear-the-garbage-collector-d69b3ff1ca30) 來管理記憶體。

More information:

更多相關資訊：

* [Get started: Command-line and server apps](/tutorials/server/get-started)

  [快速上手：命令列與伺服器應用](/tutorials/server/get-started)

* [`dart` tool for running with JIT or AOT compiling to machine code](/tools/dart-tool)

  [用於 JIT 執行或 AOT 編譯為機器碼的 `dart` 工具](/tools/dart-tool)

* [Write command-line apps](/tutorials/server/cmdline)

  [編寫命令列應用應用程式](/tutorials/server/cmdline)

* [Write HTTP servers](/tutorials/server/httpserver)

  [編寫 HTTP 伺服器](/tutorials/server/httpserver)

#### Dart Web (JavaScript dev & prod) {#web-platform}

#### Web 平台的 Dart (使用 JavaScript 開發和部署) {#web-platform}

Dart Web enables running Dart code on web platforms powered by
JavaScript. With Dart Web, you compile Dart code to JavaScript code, which in
turn runs in a browser—for example, [V8](https://v8.dev/) inside
[Chrome](https://www.google.com/chrome/).

Dart 的 Web 支援讓你可以在 JavaScript 驅動的網頁平臺上執行 Dart 程式碼。
使用 Web 環境下的 Dart 時，你可以將 Dart 編譯為在瀏覽器中執行的 JavaScript 程式碼，
例如: [Chrome](https://www.google.cn/chrome/) 中的 [V8](https://v8.dev/)。

Dart web contains two compiliation modes:

Dart Web 包含兩種編譯模式:

* An incremental development compiler enabling a fast developer cycle

  一個為快速開發提供幫助的增量編譯器。

* An optimizing production compiler which compiles Dart code to fast,
  compact, deployable JavaScript. These effeciencies come from
  techniques such as dead-code elimination.

  一個為生產環境最佳化的編譯器，可以將 Dart 程式碼編譯成快速、緊湊、可部署的 JavaScript。
  它的高效之處在於使用了類似消除無用程式碼的最佳化。

More information:

更多相關資訊：

* [Get started: Web apps](/tutorials/web/get-started)

  [快速上手：網頁應用程式](/tutorials/web/get-started)

* [`dart compile js`](/tools/dart-compile#js)
* [`webdev` tool](/tools/webdev)

  [`webdev` 工具](/tools/webdev)

* [Web deployment tips](/web/deployment)

  [網頁部署提示](/web/deployment)

#### The Dart runtime {#runtime}

#### Dart 執行時環境 {#runtime}

Regardless of which platform you use or how you compile your code,
executing the code requires a Dart runtime.
This runtime is responsible for the following critical tasks:

不論你在哪個平臺上使用、選擇如何建構你的程式碼，
執行程式碼時都需要一個 Dart 執行時環境。
這個執行時環境負責下面的關鍵任務：

* Managing memory:
  Dart uses a managed memory model,
  where unused memory is reclaimed by a garbage collector (GC). 

  記憶體管理：
  Dart 使用一個受管理的記憶體模型，
  未被使用的記憶體會由垃圾收集器 (GC) 回收。

* Enforcing the Dart type system:
  Although most type checks in Dart are static (compile-time),
  some type checks are dynamic (runtime).
  For example, the Dart runtime enforces dynamic checks by
  [type check and cast operators](/guides/language/language-tour#type-test-operators).

  執行 Dart 語言的型別體系：
  Dart 語言裡大多數型別檢查都是靜態的（編譯時），但仍有部分檢查是動態的（執行時）。
  比如，Dart 執行時環境會在遇到
  [型別判斷運算子](/guides/language/language-tour#type-test-operators)
  時執行動態檢查。

* Managing [isolates](/guides/language/language-tour#isolates):
  The Dart runtime controls the main isolate (where code normally runs)
  and any other isolates that the app creates.

  管理 [isolates](/guides/language/language-tour#isolates)：
  Dart 執行時環境會負責控制主 isolate（程式碼通常在這裡執行）
  以及其他應用建立的 isolate。

On native platforms, the Dart runtime is automatically
included inside self-contained executables, 
and is part of the Dart VM provided by
the [`dart run`](/tools/dart-run) command.

在原生平臺上，Dart 執行時環境被自動包含在獨立的可執行檔案中，
是 [`dart run`](/tools/dart-run) 命令提供的 Dart VM 的一部分。

## Learning Dart {#learning-dart}

## 學習 Dart {#learning-dart}

You have many choices for learning Dart. Here are a few that we recommend:

學習 Dart 有很多選擇。以下是我們推薦的一些方法：

* [Explore Dart in the browser]({{site.dartpad}}/) through DartPad,
  a web-based execution environment for Dart code.

  [在瀏覽器中探索 Dart]({{site.dartpad}}/) - DartPad 是一個基於網頁的 Dart 程式碼執行環境。

* [Take a tour of the Dart language](/guides/language/language-tour),
  which shows you how to use each major Dart feature.

  [Dart 開發語言概覽](/guides/language/language-tour)，
  它展示瞭如何使用 Dart 的主要特性。

* [Complete a Dart tutorial](/tutorials/server/cmdline) that 
  covers the basics of using Dart to build for the command line.

  [完成 Dart 課程](/tutorials/server/cmdline) 它涵蓋了透過命令列建構使用 Dart 的基礎知識。

* [Work through extensive online training][udemy]
  from Dart experts.

  來自 Dart 專家的 [線上課程][udemy]。

* [Explore the API documentation]({{site.dart-api}}) that
  describes the Dart core libraries.

  [探索 API 文件]({{site.dart_api}}) - 描述了 Dart 核心函式庫。

* [Read a book about Dart programming](/resources/books).

  [閱讀關於 Dart 程式設計的書籍](/resources/books)。

[udemy]: https://www.udemy.com/course/complete-dart-guide/?couponCode=NOV-20
