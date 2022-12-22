---
title: Creating packages
title: 建立 package
description: Learn how to create library packages in Dart.
description: 學習如何在 Dart 裡建立可複用的函式庫。
---

The Dart ecosystem uses [packages](/guides/packages)
to share software such as libraries and tools.
This page tells you how to create a package,
with a focus on the most common kind of package,
[_library_ packages](/tools/pub/glossary#library-package).

在 Dart 生態系統中使用 [packages](/guides/packages) 實現共享軟體，
比如一些 Library 和工具。
本章將透過最常見的 [Library Package](/tools/pub/glossary#library-package)
來介紹如何建立一個 Package。


## Creating a new package

## 建立一個新的 package

To create the initial directory and structure for a package,
use the [`dart create`](/tools/dart-create) command
and the `package` template:

若要為 package 建立一個初始化的目錄和結構，
使用 [`dart create`](/tools/dart-create) 命令，
並加入 `package` 作為命令引數來建立：

```terminal
$ dart create -t package <PACKAGE_NAME>
```

{% comment %}
TODO: Add coverage of packages that contain tools.
{% endcomment %}

## What makes a library package

## Library Package 的組成

The following diagram shows the layout of the simplest
library package:

下圖展示了最簡單的 Library Package 佈局：

<img 
  src="/assets/img/libraries/simple-lib2.png" 
  alt="root directory contains pubspec.yaml and lib/file.dart">

The minimal requirements for a library are:

Library 的最基本要求包括：

pubspec file
<br> The `pubspec.yaml` file for a library is the same
  as for an application package—there is no special
  designation to indicate that the package is a library.

pubspec 檔案
<br> Library 的 `pubspec.yaml` 檔案與應用程式的
  `pubspec.yaml` 檔案相同&mdash; `pubspec.yaml`
  檔案中並沒有特別的指出這個 Package 是一個 Library 。

lib directory
<br> As you might expect, the library code lives under the _lib_
  directory and is public to other packages.
  You can create any hierarchy under lib, as needed.
  By convention, implementation code is placed under _lib/src_.
  Code under lib/src is considered private;
  other packages should never need to import `src/...`.
  To make APIs under lib/src public, you can export lib/src files
  from a file that's directly under lib.

lib 目錄
<br> 如你所料， Library 的程式碼位於 _lib_ 目錄下，
  且對於其他 Package 是公開的。
  你可以根據需要在 **lib** 下任意建立組織檔案結構。
  按照慣例，實現程式碼會放在 **lib/src** 目錄下。
  lib/src 目錄下的程式碼被認為是私有的。
  其他 Package 應該永遠不需要匯入 `src/...` 目錄下程式碼。
  透過匯出 lib/src 目錄的檔案到一個 lib 目錄的檔案，實現
  對 lib/src 目錄中 API 的公開。


{{site.alert.note}}

  When the `library` directive isn't specified, a unique
  tag is generated for each library based on its path and filename.
  Therefore, we suggest that you omit the `library` directive from
  your code unless you plan to
  [generate library-level documentation](#documenting-a-library).

  在未指定 `library` 命令下，每個 Library 會根據它的路徑及檔案
  產生一個唯一標記。
  因此，這裡我們建議在你的程式碼中忽略 `library` 命令，除非想要
  [產生 Library-Level 文件](#documenting-a-library)。

{{site.alert.end}}

</dl>

## Organizing a library package

## 組織 Library Package

Library packages are easiest to maintain, extend, and test
when you create small, individual libraries, referred to as
_mini libraries_.
In most cases, each class should be in its own mini library, unless
you have a situation where two classes are tightly coupled.

在建立一個小的，獨立的 Library（稱之為 **Mini Library**）。
Library Package 非常容易維護，擴充和測試。
大多數情況下，除非存在兩個類緊密耦合的情況，否則每個類都應該將自己視為一個 Mini Library 。

{{site.alert.note}}

  You may have heard of the `part` directive, which allows
  you to split a library into multiple Dart files. We recommend
  that you avoid using `part` and create mini libraries instead.

  在檔案的頭部使用 `part` 命令，能夠將一個 Library 分割成多個 Dart 檔案。
  這裡，我們建議應該建立 Mini Library ，而避免使用 `part` 命令。

{{site.alert.end}}

Create a "main" library file directly under lib,
lib/_&lt;package-name&gt;_.dart, that
exports all of the public APIs.
This allows the user to get all of a library's functionality
by importing a single file.

直接在 lib 目錄下建立“主” Library 檔案，lib/_&lt;package-name&gt;_.dart，
該檔案匯出所有的公開的 API 。
這樣就可以允許使用者匯入單個檔案就能夠獲得 Library 的所有功能。

The lib directory might also include other importable, non-src, libraries.
For example, perhaps your main library works across platforms, but
you create separate libraries that rely on `dart:io` or `dart:html`.
Some packages have separate libraries that are meant to be imported
with a prefix, when the main library is not.

lib 目錄還可能包含其他可匯入的非src Library。
例如，主 Library 可能是跨平臺的，但建立的獨立 Library 依賴於 dart:io 或 dart:html 。
Some packages have separate libraries that are meant to be imported
with a prefix, when the main library is not.（無法確切理解含義，暫未翻譯）

Let's look at the organization of a real-world library package: shelf. The
[shelf](https://github.com/dart-lang/shelf)
package provides an easy way to create web servers using Dart,
and is laid out in a structure that is commonly used for Dart
library packages:

這裡讓我們來看下一個真實 Library Package 的組織結構：shelf 。
[shelf](https://github.com/dart-lang/shelf) Package
提供了一種使用 Dart 建立 Web 伺服器的簡便方法，
它的佈局在 Dart Library Package 中是一種常用佈局：

<img 
  src="/assets/img/libraries/shelf.png"
  alt="shelf root directory contains example, lib, test, and tool subdirectories">

Directly under lib, the main library file,
`shelf.dart`, exports API from several files in `lib/src`.
To avoid exposing more API than intended—and 
to give developers an overview of the entire
public API of the package—`shelf.dart` 
uses `show` to specify exactly which symbols to export:

主 Library 檔案 `shelf.dart` 在 lib 目錄下，
透過 `shelf.dart` 檔案匯出 lib/src 目錄下的若干檔案。
為了不匯出過多的 API，並且為開發者提供公開的 API 的概覽，
`shelf.dart` 使用了 `show` 來指定哪些內容需要匯出：

```dart
export 'src/cascade.dart' show Cascade;
export 'src/handler.dart' show Handler;
export 'src/hijack_exception.dart' show HijackException;
export 'src/middleware.dart' show Middleware, createMiddleware;
export 'src/middleware/add_chunked_encoding.dart' show addChunkedEncoding;
export 'src/middleware/logger.dart' show logRequests;
export 'src/middleware_extensions.dart' show MiddlewareExtensions;
export 'src/pipeline.dart' show Pipeline;
export 'src/request.dart' show Request;
export 'src/response.dart' show Response;
export 'src/server.dart' show Server;
export 'src/server_handler.dart' show ServerHandler;
```

The shelf package also contains a mini library: shelf_io.
This adapter handles HttpRequest objects from `dart:io`.

在 shelf Package 中同樣包含了 Mini Library：shelf_io 。
介面卡用來處理來自 `dart:io` 的 HttpRequest 物件。

{{site.alert.tip}}

  For the best performance when developing with [dartdevc,](/tools/dartdevc)
  put [implementation files](/tools/pub/package-layout#implementation-files) 
  under `/lib/src`, instead of elsewhere under `/lib`.
  Also, avoid imports of <code>package:<em>package_name</em>/src/...</code>.

  為了在開發時使 [dartdevc](/tools/dartdevc) 工具能夠達到最佳新能，
  應該將 [實現檔案](/tools/pub/package-layout#implementation-files) 放到目錄 `/lib/src` 下，
  而不是 `/lib` 目錄的其他地方。
  另外，避免透過 <code>package:<em>package_name</em>/src/...</code> 匯入檔案。

{{site.alert.end}}


## Importing library files

## 匯入 Library 檔案

When importing a library file from another package, use the
the `package:` directive to specify the URI of that file.

在從其他 package 匯入 Library 檔案時，
使用 `package:` 命令來指定檔案的 URI 。

```dart
import 'package:utilities/utilities.dart';
```

When importing a library file from your own package,
use a relative path when both files are inside of lib,
or when both files are outside of lib. 
Use `package:` when the imported file is in lib and the importer is outside.

在兩個檔案都在 lib 目錄中，或兩個檔案都在 lib 目錄外，
我們都可以使用相對路徑的方式匯入 Library 。
但是，如果兩個檔案不都在 lib 目錄中，需要對 lib 內或者 lib 外進行查詢，
那麼此時必須要使用 `package:` 匯入。
如果對當前使用存在疑惑，那麼直接 `package:` ； `package:` 滿足所有情況。

The following graphic shows how
to import `lib/foo/a.dart` from both lib and web.

下面圖片展示分別從 lib 和 web 目錄中匯入 `lib/foo/a.dart` 。

<img 
  src="/assets/img/libraries/import-lib-rules.png"
  alt="lib/bar/b.dart uses a relative import; web/main.dart uses a package import">

## Conditionally importing and exporting library files

## 條件匯入或條件匯出 Library 檔案

If your library supports multiple platforms,
then you might need to conditionally import or export library files.
A common use case is a library that supports both web and native platforms.

如果你的 library 支援多平臺，那麼你應該會用到條件匯入
或條件匯出 library 檔案。
常見的使用案例是，一個函式庫同時支援 Web 和 Native 平台。

To conditionally import or export,
you need to check for the presence of `dart:*` libraries.
Here's an example of conditional export code that
checks for the presence of `dart:io` and `dart:html`:

為了使用條件匯入或條件匯出，你需要檢查
是否存在 `dart:*` 庫。
下面是一個條件匯出程式碼的範例，
它將檢查是否存在 `dart:io` and `dart:html` 庫：

<?code-excerpt "create_libraries/lib/hw_mp.dart (export)"?>
```dart
export 'src/hw_none.dart' // Stub implementation
    if (dart.library.io) 'src/hw_io.dart' // dart:io implementation
    if (dart.library.html) 'src/hw_html.dart'; // dart:html implementation
```
<div class="prettify-filename">lib/hw_mp.dart</div>

Here's what that code does:

該程式碼的作用如下：

* In an app that can use `dart:io`
  (for example, a command-line app),
  export `src/hw_io.dart`.

  在一個可以使用 `dart:io` 的 app 中
  （例如一個命令列應用），
  匯出 `src/hw_io.dart`。

* In an app that can use `dart:html`
  (a web app),
  export `src/hw_html.dart`.

  在一個 web 應用中可以使用 `dart:html`
  匯出 `src/hw_html.dart`。

* Otherwise, export `src/hw_none.dart`.

  若是其他情況，則匯出 `src/hw_none.dart`。

To conditionally import a file, use the same code as above,
but change `export` to `import`.

要條件匯入一個檔案可以使用和上面一樣的方式，
僅需將 `export` 改為 `import` 即可。

{{site.alert.note}}

  The conditional import or export checks only whether the library is
  _available for use_ on the current platform,
  not whether it's actually imported or used.

  條件匯入或條件匯出僅檢查該庫在當前平臺上_是否可用_，
  而不管是實際匯入還是使用。

{{site.alert.end}}

All of the conditionally exported libraries must implement the same API.
For example, here's the `dart:io` implementation:

所有條件匯出的函式庫必須實現相同的 API。
下面是 `dart:io` 實現的一個例子：

<?code-excerpt "create_libraries/lib/src/hw_io.dart"?>
```dart
import 'dart:io';

void alarm([String? text]) {
  stderr.writeln(text ?? message);
}

String get message => 'Hello World from the VM!';
```
<div class="prettify-filename">lib/src/hw_io.dart</div>

And here's the default implementation,
which uses stubs that throw `UnsupportedError`:

這是一個預設實現，它會導致丟擲 `UnsupportedErrors`：

<?code-excerpt "create_libraries/lib/src/hw_none.dart"?>
```dart
void alarm([String? text]) => throw UnsupportedError('hw_none alarm');

String get message => throw UnsupportedError('hw_none message');
```
<div class="prettify-filename">lib/src/hw_none.dart</div>

On any platform,
you can import the library that has the conditional export code:

在任何平臺上，你都可以匯入具有條件匯出程式碼的函式庫：

<?code-excerpt "create_libraries/example/hw_example.dart" replace="/create_libraries/hw_mp/g"?>
```dart
import 'package:hw_mp/hw_mp.dart';

void main() {
  print(message);
}
```

## Providing additional files

## 提供額外檔案

A well-designed library package is easy to test.
We recommend that you write tests using the
[test](https://github.com/dart-lang/test) package,
placing the test code in the `test` directory at the
top of the package.

一個精心設計的 Library Package 很容易被測試。
我們建議使用 [test](https://github.com/dart-lang/test) Package
編寫測試使用案例，並將測試程式碼放到 Package 根目錄的 `test` 目錄中。

If you create any command-line tools intended for public consumption,
place those in the `bin` directory, which is public.
Enable running a tool from the command line, using
[`dart pub global activate`](/tools/pub/cmd/pub-global#activating-a-package).
Listing the tool in the
[`executables` section](/tools/pub/pubspec#executables)
of the pubspec allows a user to run it directly without calling
[`dart pub global run`](/tools/pub/cmd/pub-global#running-a-script-using-dart-pub-global-run).

如果要建立一個公用的命令列工具，應該將這些工具放到公共目錄 `bin` 中。
使用 [`dart pub global activate`](/tools/pub/cmd/pub-global#activating-a-package) 命令列
來執行工具。
在 pubspec 的 [`executables` 部分](/tools/pub/pubspec#executables)
列出的工具允許使用者直接執行它而無需呼叫
[`dart pub global run`](/tools/pub/cmd/pub-global#running-a-script-using-dart-pub-global-run)。

It's helpful if you include an example of how to use your library.
This goes into the `example` directory at the top of the package.

在 Library 中包含一個 example 程式示範如何使用 Library 是非常有用的。
example 程式在 Package 根目錄的 `example` 目錄中。

Any tools or executables that you create during development that aren't for
public use go into the `tool` directory.

在開發過程中任何非公開的工具或可執行程式，應該放到 `tool` 資料夾。

Other files that are required if you publish your library to the
[pub.dev]({{site.pub}}) site, such as `README.md` and `CHANGELOG.md`, are
described in [Publishing a package](/tools/pub/publishing).
For more information on how to organize a package directory,
see the [pub package layout conventions](/tools/pub/package-layout).

如果要將 Library 釋出到 Pub 網站還要求一些其他的檔案來描述 [釋出的 Package](/tools/pub/publishing) ，
例如：`README.md` 和 `CHANGELOG.md` 檔案。
更多關於如何組織 Package 目錄的內容，參見 [Pub Package 佈局約定](/tools/pub/package-layout)。

## Documenting a library

## 為 Library 製作文件

You can generate API docs for your library using
the [`dart doc`][] tool.
`dart doc` parses the source looking for
[documentation comments](/guides/language/effective-dart/documentation#doc-comments),
which use the `///` syntax:

使用 [dartdoc][] 可以為 Library 產生 API 文件。
dartdoc 解析原始檔去查詢使用 `///` 語法標註的
[文件註釋](/guides/language/effective-dart/documentation#doc-comments)：

{% prettify dart tag=pre+code %}
/// The event handler responsible for updating the badge in the UI.
void updateBadge() {
  ...
}
{% endprettify %}

For an example of generated docs, see the
[shelf documentation.]({{site.pub-api}}/shelf/latest)

文件產生範例，參見 [shelf 文件]({{site.pub-api}}/shelf/latest)。

{{site.alert.note}}

To include any library-level documentation in the generated docs,
you must specify the `library` directive.
See [issue 1082.](https://github.com/dart-lang/dartdoc/issues/1082)

在產生的文件中要包含任何 Library-Level 的文件，必須要指定 `library` 命令。
參見 [issue 1082](https://github.com/dart-lang/dartdoc/issues/1082)。

{{site.alert.end}}

## Distributing an open source library {#distributing-a-library}

## 分發開源 Library {#distributing-a-library}

If your library is open source,
we recommend sharing it on the [pub.dev site.]({{site.pub}})
To publish or update the library,
use [pub publish](/tools/pub/cmd/pub-lish),
which uploads your package and creates or updates its page.
For example, see the page for the [shelf package.]({{site.pub-pkg}}/shelf)
See [Publishing a package](/tools/pub/publishing)
for details on how to prepare your package for publishing.

如果 Library 是開源的，
我們建議將他共享到 [Pub 網站]({{site.pub}})。
使用 [pub publish](/tools/pub/cmd/pub-lish) 來發布或者更新 Library，
該命令將會上傳 Package 並建立或更新其頁面。
範例參見 [shelf Package]({{site.pub}}/packages/shelf) 頁面。
有關如何準備釋出 Package 的詳細內容，參見 [釋出 Package](/tools/pub/publishing)。

The pub.dev site not only hosts your package,
but also generates and hosts your package's API reference docs.
A link to the latest generated docs is in the package's **About** box;
for example, see the shelf package's
[API docs.]({{site.pub-api}}/shelf)
Links to previous versions' docs are in the
**Versions** tab of the package's page.

Pub.dev 網站不僅僅是 Dart Packages 的釋出網站，
Pub 網站不僅用於託管 Package ，還能夠產生託管 Package 的 API 參考文件。
最新產生的文件的連結位於 Package 的 **About** 選項卡中;
範例參見 shelf Package 的 [API 文件]({{site.pub-api}}/shelf)。
連結到以前版本的文件，位於 Package 頁面的 **Versions** 選項卡中。

To ensure that your package's API docs look good on the pub.dev site,
follow these steps:

為了確保 Package 的 API 文件在 Pub 網站上看起來更美觀，
請遵循以下步驟：

* Before publishing your package, run the [`dart doc`][] tool
  to make sure that your docs generate successfully and look as expected.

  在釋出 Package 前，請透過執行 [`dart doc`][]
  工具確保文件能夠產生成功且符合預期。

* After publishing your package, check the **Versions** tab
  to make sure that the docs generated successfully.

  在釋出 Package 後，請檢查 **Versions** 選項卡，確保文件產生成功。

* If the docs didn't generate at all,
  click **failed** in the **Versions** tab to see the `dart doc` output.

  如果文件沒有產生，點選 **Versions** 選項卡中的 **failed** 檢視 dartdoc 的輸出。

## Resources

## 資源

Use the following resources to learn more about library packages:

透過運用以下資源來了解學習更多關於 Library Package 的內容：

* [Libraries and visibility](/guides/language/language-tour#libraries-and-visibility)
  in the [language tour](/guides/language/language-tour) covers
  using library files.

  [Libraries 和可見性](/guides/language/language-tour#libraries-and-visibility)
  在 [語言概覽](/guides/language/language-tour) 中涵蓋了 Library 檔案使用的內容。

* The [package](/guides/packages) documentation is useful, particularly the
  [package layout conventions](/tools/pub/package-layout).

  [Package](/guides/packages) 文件非常有用，尤其是 
  [Package 佈局約定](/tools/pub/package-layout).

* [What not to commit](private-files)
  covers what should not be checked into a source code repository.

  [什麼不應該被提交](private-files)包含了關於什麼檔案不應該被提交到原始碼儲存庫的介紹。

* The newer library packages under the
  [dart-lang](https://github.com/dart-lang) organization tend
  to show best practices. Consider studying these examples:
  [dart_style,](https://github.com/dart-lang/dart_style)
  [path,](https://github.com/dart-lang/path)
  [shelf,](https://github.com/dart-lang/shelf)
  [source_gen,](https://github.com/dart-lang/source_gen) and
  [test.](https://github.com/dart-lang/test)

  [dart-lang](https://github.com/dart-lang) 組織下的最新 Library Package
  常常是最佳實踐的提現。可以參考學些以下這些例項：
  [dart_style](https://github.com/dart-lang/dart_style)，
  [path](https://github.com/dart-lang/path)，
  [shelf](https://github.com/dart-lang/shelf)，
  [source_gen](https://github.com/dart-lang/source_gen) 以及
  [test](https://github.com/dart-lang/test) 。

[`dart doc`]: /tools/dart-doc
