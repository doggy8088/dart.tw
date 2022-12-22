---
title: Core libraries
title: Dart 語言核心函式庫
description: Learn about Dart's core libraries and APIs.
description: 學習使用 Dart 的核心庫和 API。
---

<style>
  th:first-child {
    width: 80%;
  }
</style>

Dart has a rich set of core libraries that provide essentials for many everyday
programming tasks such as 
working on collections of objects (`dart:collection`), 
making calculations (`dart:math`), 
and encoding/decoding data (`dart:convert`). 
Additional APIs are available in
[commonly used packages](/guides/libraries/useful-libraries).

Dart 擁有非常豐富的核心庫用以為諸如處理物件集合（`dart:collection`）、
進行數學運算（`dart:math`）以及編/解碼資料（`dart:convert`）
等常用程式設計操作提供支援。除此之外，
[由社群貢獻的 packages](/guides/libraries/useful-libraries) 中也提供了許多其它
的 API 便於開發者使用。

## Multi-platform libraries

## 全平台庫列表

The following table lists the Dart core libraries that work on all
[Dart platforms](/overview#platform).

下面提供的一些函式庫可以應用於所有 [Dart 可執行的平台](/overview#platform)。

|-----------------------------------------------+-------------------------------|
| Library                                       | Notes                         |
|-----------------------------------------------|-------------------------------|
| 函式庫名稱                                         | 備註                           |
| [`dart:core`][dart-core]              <br>Built-in types, collections, and other core functionality for every Dart program. | |
| [`dart:core`][dart-core]              <br> 每一個 Dart 程式都可能會使用到的內建型別、集合以及其它的一些核心功能。| |
| [`dart:async`][dart-async], [`package:async`][package-async]<br>Support for asynchronous programming, with classes such as `Future` and `Stream`.<br>`package:async` provides additional utilities around the `Future` and `Stream` types. | |
| [`dart:async`][dart-async], [`package:async`][package-async]<br>支援透過使用 `Future` 和 `Stream` 這樣的類實現非同步程式設計。<br>`package:async` 提供了更多圍繞 `Future` 和 `Stream` 建構的實用工具 | |
| [`dart:collection`][dart-collection], [`package:collection`][package-collection]<br>Classes and utilities that supplement the collection support in `dart:core`.<br>`package:collection` provides further collection implementations and functions for working on and with collections. | |
| [`dart:collection`][dart-collection], [`package:collection`][package-collection]<br>提供 `dart:core` 庫中不支援的額外的集合實用工具類別。<br>`package:collection` 則提供了更進一步的、用於處理和使用集合的函式和實現 | |
| [`dart:convert`][dart-convert], [`package:convert`][package-convert]<br>Encoders and decoders for converting between different data representations, including JSON and UTF-8.<br>`package:convert` provides additional encoders and decoders. ||
| [`dart:convert`][dart-convert], [`package:convert`][package-convert]<br>用於提供轉換不同資料的編碼器和解碼器，包括 JSON 和 UTF-8。<br>`package:convert` 則提供了更多編解碼器。 ||
| [`dart:developer`][dart-developer]<br>Interaction with developer tools such as the debugger and inspector. | [Native JIT][jit] and [dartdevc][] only |
| [`dart:math`][dart-math]<br>Mathematical constants and functions, plus a random number generator. | |
| [`dart:developer`][dart-developer]<br>類似偵錯程式和分析器這樣的與開發者互動配合的工具。| 僅支援 [Native JIT][jit] 和 [dartdevc][] |
| [`dart:math`][dart-math]<br>Mathematical constants and functions, plus a random number generator. | |
| [`dart:math`][dart-math]<br>包含算術相關函式和常量，還有隨機數產生器。 | |
| [`dart:typed_data`][dart-typed_data], [`package:typed_data`][package-typed_data]<br>Lists that efficiently handle fixed sized data (for example, unsigned 8-byte integers) and SIMD numeric types.<br>`package:typed_data` provides further classes and functions working on typed data. | |
| [`dart:typed_data`][dart-typed_data], [`package:typed_data`][package-typed_data]<br>高效處理固定大小資料（例如無符號的 8 位整型）和 SIMD 數字型別的列表。<br>`package:typed_data` 提供了更進一步的類和方法用於處理結構化的資料。 | |
{:.table .table-striped}

## Native platform libraries

## 原生平台函式庫

The following table lists the Dart core libraries that work on the
[Dart native platform](/overview#native-platform) (AOT- and JIT-compiled code).

下面列出的核心庫適用於 [Dart 原生平台](/overview#platform)（AOT 和 JIT 編譯執行）。

|-----------------------------------------------+-------------------------------|
| Library                                       | Notes                         |
|-----------------------------------------------|-------------------------------|
| 函式庫名稱                                         | 備註                           |
| [`dart:ffi`][dart-ffi], [`package:ffi`][package-ffi]<br>A foreign function interface that lets Dart code use native C APIs.<br>`package:ffi` contains utilities incl. support for converting Dart strings and C strings. | |
| [`dart:ffi`][dart-ffi], [`package:ffi`][package-ffi]<br>Dart 程式碼可以透過這個外部函式介面使用原生的 C 語言 API。<br>`package:ffi` 提供的實用工具套件括：支援轉換 Dart 字串和 C 字串。 | |
| [`dart:io`][dart-io], [`package:io`][package-io]<br>File, socket, HTTP, and other I/O support for non-web applications.<br>`package:io` provides functionality including support for ANSI colors, file copying, and standard exit codes. | |
| [`dart:io`][dart-io], [`package:io`][package-io]<br>用於支援非 Web 應用的檔案、Socket、HTTP 和其它 I/O 操作。<br>`package:io` 提供的功能包括 ANSI 顏色、檔案複製和標準化的退出程式碼。 | |
| [`dart:isolate`][dart-isolate]<br> Concurrent programming using isolates: independent workers similar to threads. | |
| [`dart:isolate`][dart-isolate]<br> 使用 Isolate 實現併發程式設計：類似於執行緒的獨立的 Worker。 | |
| [`dart:mirrors`][dart-mirrors]<br> Basic reflection with support for introspection and dynamic invocation. | Experimental<br>[Native JIT][jit] only (_not_&nbsp;Flutter) |
| [`dart:mirrors`][dart-mirrors]<br> 支援檢查和動態呼叫的基本反射功能。 | 實驗性<br>只在 JIT 中有效 (**Flutter 中無效**) |
{:.table .table-striped}

## Web platform libraries

## Web平台函式庫

The following table lists the Dart core libraries that work on the
[Dart web platform](/overview#web-platform) (code compiled to JavaScript).

下面列出的核心庫適用於 [Dart Web 平台](/overview#platform)（程式碼編譯成 JS 來執行）。

|-----------------------------------------------+-------------------------------|
| Library                                       | Notes                         |
|-----------------------------------------------|-------------------------------|
| 函式庫名稱                                         | 備註                           |
| [`dart:html`][dart-html]<br>HTML elements and other resources for web-based applications. | |
| [`dart:html`][dart-html]<br>為 Web 應用開發所提供的 HTML 元素和其它資源。 | |
| [`dart:indexed_db`][dart-indexed_db]<br>Client-side key-value store with support for indexes. | |
| [`dart:indexed_db`][dart-indexed_db]<br>客戶端上使用的可以索引的鍵值對儲存。 | |
| [`dart:js`][dart-js], [`dart:js_util`][dart-js_util], [`package:js`][package-js]<br>`dart:js_util` provides low-level primitives for interoperability; typically the higher-level annotations in `package:js` are recommended, as they help express interoperability more succinctly. For more details see [JavaScript interoperability][].<br>_Don't use `dart:js` directly; direct use of those legacy APIs is deprecated_. | |
| [`dart:js`][dart-js], [`dart:js_util`][dart-js_util], [`package:js`][package-js]<br>`dart:js_util` 為互操作提供了低階原語 (Low-Level Primitives)，通常情況下我們更推薦使用 `package:js` 中提供的更高階的註解內容，因為它們更有助於更簡潔地表達和體現互操作性。<br> **請不要直接使用 `dart:js` 了，那些 API 已被標記為已廢棄** | |
| [`dart:svg`][dart-svg]<br>Scalable Vector Graphics. | |
| [`dart:svg`][dart-svg]<br>用於可縮放的向量圖形 (SVG)。 | |
| [`dart:web_audio`][dart-web_audio]<br>High-fidelity audio programming in the browser. | |
| [`dart:web_audio`][dart-web_audio]<br>用於瀏覽器的高保真音訊程式設計。 | |
| [`dart:web_gl`][dart-web_gl]<br>3D programming in the browser. | |
| [`dart:web_gl`][dart-web_gl]<br>用於瀏覽器的 3D 程式設計。 | |
{:.table .table-striped}

<!---
Multi-platform libraries
-->
[dart-core]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/dart-core-library.html
[dart-async]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-async/dart-async-library.html
[package-async]: {{site.pub-pkg}}/async
[dart-collection]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-collection/dart-collection-library.html
[package-collection]: {{site.pub-pkg}}/collection
[dart-convert]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-convert/dart-convert-library.html
[package-convert]: {{site.pub-pkg}}/convert
[dart-developer]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-developer/dart-developer-library.html
[dart-math]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-math/dart-math-library.html
[dart-typed_data]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-typed_data/dart-typed_data-library.html
[package-typed_data]: {{site.pub-pkg}}/typed_data

<!---
Native platform libraries
-->
[dart-ffi]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/dart-ffi-library.html
[package-ffi]: {{site.pub-pkg}}/ffi
[dart-cli]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-cli/dart-cli-library.html
[dart-io]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-io/dart-io-library.html
[package-io]: {{site.pub-pkg}}/io
[dart-isolate]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-isolate/dart-isolate-library.html
[package-isolate]: {{site.pub-pkg}}/isolate
[dart-mirrors]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-mirrors/dart-mirrors-library.html

<!---
Web platform libraries
-->
[dart-html]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-html/dart-html-library.html
[dart-indexed_db]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-indexed_db/dart-indexed_db-library.html
[dart-js]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-js/dart-js-library.html
[package-js]: {{site.pub-pkg}}/js
[dart-js_util]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-js_util/dart-js_util-library.html
[dart-svg]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-svg/dart-svg-library.html
[dart-web_audio]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-web_audio/dart-web_audio-library.html
[dart-web_gl]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-web_gl/dart-web_gl-library.html

<!---
Misc
-->
[dartdevc]: /tools/dartdevc
[jit]: /overview#native-platform
[JavaScript interoperability]: /web/js-interop
