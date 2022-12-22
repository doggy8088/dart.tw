---
title: Dart 2 migration guide
title: Dart 2 遷移指南
description: How Dart 2 is different from Dart 1.x, and how you can convert your code to work with Dart 2.
description: Dart 2 和 1.x 版本的區別，以及如何遷移到 Dart 2。
---

<style>
del { color: rgba(255,0,0,.35); }
del code { color: darkred; }
</style>

Dart 2 has a few key differences from earlier versions of Dart.
This page briefly covers those differences and
gives general advice on migrating your code to Dart 2.

Dart 2 有一些與早期版本 Dart 不同的關鍵點。本文將會簡單地介紹這些差異並提供了一些將程式碼遷移到 Dart 2 的建議。

For information on _why_ Dart 2 has changed, see the
[Dart 2 announcement.][Dart 2 announcement]

至於 **為什麼** Dart 2 要做這些改變，
你可以查閱 [Dart 2 公告][Dart 2 announcement]

## Differences

## 差異點

The Dart language, libraries, build system, 
and web development tools have changed.

Dart 語言、庫、編譯系統以及 Web 開發工具都已經有所變化。

### Language and libraries

### 語言和庫

* [Dart's type system][sound Dart] is now sound.

  [Dart 的型別系統][sound Dart] 現在是健全的。

  * [Fixing common type problems][Fixing Common Type Problems]

    [修復常見的型別問題][Fixing Common Type Problems]

  * [Flutter announcement: Breaking Change: `--preview-dart-2` turned on by default][Leaf's email]

    [Flutter 公告：變更：`--preview-dart-2` 命令引數預設為開啟][Leaf's email]

* Instance creation keywords are now generally optional,
  as described in [Using constructors][]:

  例項物件建立的關鍵字現在是可選的，就像 [使用建構函式][Using constructors] 中所說的那樣：

  * `new` is always optional.

    `new` 關鍵字總是可選的。

  * `const` is optional inside of a constant context.

    `const` 在常量上下文環境中也是可選的。

* Dart no longer has checked mode.

  Dart 不再有檢查模式。

  * [Assert statements][] are still supported, but you enable them differently.

    [Assert 陳述式][Assert statements] 依然支援，但是開啟的方式改變了。

* Functions marked `async` now run synchronously
  until the first `await` statement. 

  標記為 `async` 的方法現在可以同步執行，直到第一個 `await` 陳述式為止。
  
  * Previously, execution returned immediately to the caller, and
    the function body was scheduled to run later.

    之前是立刻返回給呼叫者，方法體內的程式碼會在之後執行。
    
  * To learn more, read [the September 2017 Dart newsletter][sync async start].

    瞭解更多，請閱讀 [2017 年 9 月的 Dart 新聞快報][sync async start]。

* The Dart language and core libraries have changed,
  partly as a result of the type system changes.

  Dart 語言和核心庫也改變了，部分原因是因為型別系統的改變而改變。

  * [Dev channel API reference documentation][apiref]

    [開發渠道 API 參考文件][apiref]

  * [dart-lang/sdk CHANGELOG][]

    [dart-lang/sdk 變更日誌][dart-lang/sdk CHANGELOG]

### Tools

### 工具

* Pub no longer supports transformers.
  Instead, use the [new build system.][build system]

  Pub 不再支援轉換。作為替代，請使用 [新的編譯系統。][build system]

* Tools related to web development have changed.

  Web 開發相關的工具已經改變。

  * The new build system [replaces `pub build` and `pub serve`.][build_runner web]

    新的編譯系統 [替代 `pub build` 和 `pub serve`][build_runner web]。

  * Dartium is no longer supported.
    Instead, use the [`webdev` tool][] and Chrome.

  * Dartium 不再支援。作為替代，
    使用 [`webdev` 工具][`webdev` tool] 和 Chrome。

## Migrating your code {#migration}

## 遷移你的程式碼 {#migration}

How to migrate your code depends on how old your code is
and what platforms it runs on.
For help with migrating web apps,
see [Migrating web apps](#web-migration).
If you're migrating a Flutter app,
consult the [breaking change notification.][Leaf's email]
If you publish packages,
then in addition to making platform-specific changes,
follow the [package migration instructions below](#migrating-packages).

如何遷移你的程式碼取決於你的程式碼有多古老以及執行在什麼平台。
有關如何遷移 Web 應用的幫助請查閱 [ Web 應用遷移指南][webdev dart2]。
如果你遷移一個 Flutter 應用，
請查閱 [變革公告][Leaf's email] 如果你釋出了 package，
則除了適配平台不同的特性之外，
還需要遵循 [下述的 package 遷移說明](#migrating-packages)。

### General process

### 通用流程

Here's an overview of the process of migrating to Dart 2,
from either Dart 1.x or an earlier version of Dart 2.

下面是遷移到 Dart 2 的一個流程概述。

1. **Get an up-to-date version of the Flutter or Dart SDK
   and the plugins for any IDEs you use.**

   **獲取一個最新的 Flutter 或 Dart SDK 版本以及你所使用的 IDE 的最新外掛。**

   * [Flutter SDK upgrade instructions][Flutter SDK upgrade]

     [Flutter SDK 升級說明][Flutter SDK upgrade]

   * [Dart SDK instructions][Dart SDK install] (server-side or web)

     [Dart SDK 說明][Dart SDK install] (伺服器端或 Web)

2. **Upgrade the packages your app depends on.**

   **升級你應用依賴的 package。**

   * Flutter: [`flutter pub upgrade`][flutter pub upgrade]

     Flutter：[`flutter pub upgrade`][flutter pub upgrade]

   * * Server-side or web: [`dart pub upgrade`][dart pub upgrade]

     伺服器端或 Web：[`dart pub upgrade`][dart pub upgrade]

3. **Run the [dart2_fix tool][dart2_fix]**. It helps migrate some
   usages of deprecated Dart 1.x APIs to Dart 2.

   **執行 [dart2_fix 工具][dart2_fix]**，
   它可以幫助遷移一些過時的 Dart 1.x API 到 Dart 2。

4. **Run the analyzer** to find [compile-time errors][]
   and deprecation hints.

   **執行分析器**以找出 [編譯時錯誤][compile-time errors] 以及棄用提示。

   * Flutter: [`flutter analyze`][Flutter analyzer]
     or use the problems view in Android Studio/IntelliJ or VS Code.

     Flutter：[`flutter analyze`][Flutter analyzer]
     或使用 Android Studio/IntelliJ 或 VS Code 的問題檢視。

   * Server-side or web: [`dart analyze`][dartanalyzer]

     伺服器端或 Web：[`dart analyze`][dartanalyzer]

5. **Fix issues in your code and run the analyzer again**,
   repeating until your code passes static analysis.

   **修復程式碼問題並再次執行分析器**，重複該操作直到你的程式碼透過靜態分析。

6. **Run tests to find [runtime errors][].**

   **執行測試以找出 [執行時錯誤][runtime errors]**

   * Run all [automated tests] for your software.

     執行你軟體所有的 [自動化測試]。

   * Do manual testing, and look for console errors.

     執行手動測試以查詢控制檯錯誤。

   Consider adding automated tests to catch issues that you find.

   考慮新增自動化測試來捕獲你發現的問題。

7. **Fix issues until your code works.**

   **修復問題知道你的程式碼可以正常執行。**

8. _Optional:_ **Remove `new` and unnecessary `const`.**

   **可選的：** **移除 `new` 以及不必要的 `const` 關鍵字。**

   * You can remove these by hand or use a tool such as `dart format --fix`.

     你可以手動地移除它們或者使用類似 `dart format --fix` 這樣的工具。

   * To find occurrences of `new` and unnecessary `const`, add the rules
     `unnecessary_new` and `unnecessary_const` to the `linter` section of your
     [analysis options file][].

     為了找到 `new` 和不必要的 `const` 出現的地方，
     可以將 `unnecessary_new` 和 `unnecessary_const` 
     規則新增至 [分析選項檔案][analysis options file] 的 `linter` 部分。

### Migrating packages

### 遷移 package

As a package owner, you need to do the following:

作為一個 package 的擁有者，你需要遵循下列幾項：

* Follow the migration tips for the platforms that your package supports
  (see [above](#migration)).

  遵循你的 package 所支援的平台的遷移技巧（詳見 [上述](#migration)）。

* Make sure your package passes Dart 2 analysis (see **Run the analyzer** above)

  確保你的 package 通過了 Dart 2 分析（查閱上面的**執行分析器**）。

* Make sure your package's users know how to report issues.

  確保你 package 的使用者知道如何上報問題。

* Respond quickly to issue reports.

  能夠對上報的問題快速地作出響應。

* If code changes aren't backward compatible,
  update the lower SDK constraint.

  如果程式碼的變更導致無法向後相容，請升級最低的 SDK 版本限制。


### Migrating web apps {#web-migration}

### 遷移 Web 應用 {#web-migration}

To update your web app to Dart 2,
you need to first update your tool usage
as described in [Tools](#tools).

如果需要將你的 Web 應用更新到使用 Dart 2.x，
你需要先更新一波 [工具](#tools)。

You'll also need to make the following changes
to the `<script>` elements referencing your compiled Dart code:

你還要對 `<script>` 元素進行如下的改動，以更新編譯後的 Dart 程式碼參考：

- Drop <del>`<script defer src="packages/browser/dart.js"></script>`</del>

  刪除 <del>`<script defer src="packages/browser/dart.js"></script>`</del>

- Replace <del>`<script defer src="foo.dart" type="application/dart"></script>`</del> by<br>
  `<script defer src="foo.dart.js"></script>`

  用 `<script defer src="foo.dart.js"></script>` 替換<br>
  <del>`<script defer src="foo.dart" type="application/dart"></script>`</del>

#### Changes and backward compatibility

#### 變化以及向後相容性

If you have to change your package's code,
**try to make it work in 1.x**, as well as Dart 2.
For example, you might be able to add type annotations
or (if an API has been removed) to use an alternative 1.x API.

如果你必須更改 package 的程式碼，請 **嘗試令其可以在 1.x 中使用**，
就像其在 Dart 2 中使用那樣。
例如，你可能需要新增型別註解（或者如果一個已被移除的 API）
去使用一個替代的 1.x API。

If a backward-compatible change isn't possible,
**update the lower [SDK constraint.][SDK constraints]**

如果程式碼的變更導致無法向後相容，請**升級最低的 [SDK 限制][SDK constraints]**

[Test your changes][testing] to make sure that your package works as expected.

[測試你程式碼的變更][testing] 以確保你的 package 在使用時可以如你所願地執行。

#### Upper constraints on the SDK version {#upper-constraint}

#### SDK 版本上限 {#upper-constraint}

Once your package passes Dart 2 analysis, update the upper constraint
to declare that the package is compatible with Dart 2:

一旦你的 package 通過了 Dart 2 分析，
請更新版本上限以表明其支援相容到 Dart 2：

```yaml
environment:
  # 只能在 Dart 2 中使用
  sdk: '>=2.0.0 <3.0.0'
```

If you plan to maintain compatibility with older versions of Dart, adjust the
lower SDK constraint accordingly:

如果你計劃保持與舊版 Dart 的相容性，
請相應地調整 SDK 為較低的版本限制：

```yaml
environment:
  # 可以在 Dart 1（1.20.1 開始）以及 Dart 2 中使用。
  sdk: '>=1.20.1 <3.0.0'
```

If you use [features introduced after 2.0,][dart-lang/sdk CHANGELOG]
be sure to specify the correct lower SDK constraint:

如果你使用 [2.0 後引入的功能][dart-lang/sdk CHANGELOG] 請確保你指定了正確的 SDK 下限：

```yaml
environment:
  # 可以在 2.1 中使用但不能在 2.0 中使用
  sdk: '>=2.1.0 <3.0.0'
```

{{site.alert.warning}}

  **Packages must have an upper constraint of `<3.0.0`** to work in
  Dart 2 stable and subsequent Dart 2 releases.
  Dart 2 dev builds _before_ the stable release have
  lax upper constraint checking and can use packages that have
  no SDK constraints or an upper constraint of `<2.0.0`.

  **Package 版本必須有 `<3.0.0` 的上限限制**
  以便在 Dart 2 穩定版以及隨後的發行版中使用。
  Dart 2 在建構編譯穩定發行版前的開發版本時有較為寬鬆的上限限制檢查，
  你可以在此情況下使用沒有 SDK 限制或者上限限制 `<2.0.0` 的 package。

{{site.alert.end}}

## More resources

## 更多資源

* [DartPad](/tools/dartpad)

  [DartPad](/tools/dartpad)

* [Dart Language Specification][]

  [Dart 語言規範][Dart Language Specification]

* [About Dart SDK release channels and version strings][prerelease]

  [關於 Dart SDK 釋出渠道和版本字串][prerelease]

* [SDK constraints][]

  [SDK 限制][SDK constraints]

* [Updating your pub package to Dart 2,][]
  an article that includes tips for updating your code and
  using Travis to perform continuous integration (CI) testing

  [更新你的 pub package 到 Dart 2][Updating your pub package to Dart 2,]，
  這篇文章包含了更新程式碼和使用 Travis 執行可持續整合 (CI) 測試的技巧。

[analysis options file]: /guides/language/analysis-options#the-analysis-options-file
[`webdev` tool]: /tools/webdev
[build system]: https://github.com/dart-lang/build/tree/master/docs
[automated tests]: /guides/testing
[Flutter analyzer]: {{site.flutter-docs}}/testing/debugging#the-dart-analyzer
[dart analyze]: /tools/dart-analyze
[flutter pub upgrade]: {{site.flutter-docs}}/development/packages-and-plugins/using-packages#updating-package-dependencies
[dart pub upgrade]: /guides/packages#upgrading-a-dependency
[dart2_fix]: https://github.com/dart-archive/dart2_fix
[apiref]: {{site.dart-api}}/dev
[assert statements]: /guides/language/language-tour#assert
[build_runner web]: /tools/build_runner
[compile-time errors]: /guides/language/sound-problems#static-errors-and-warnings
[Dart 2 announcement]: https://medium.com/dartlang/announcing-dart-2-80ba01f43b6
[Dart Language Specification]: /guides/language/spec
[dart-lang/sdk CHANGELOG]: https://github.com/dart-lang/sdk/blob/main/CHANGELOG.md#200---2018-08-07
[Fixing Common Type Problems]: /guides/language/sound-problems
[Flutter SDK upgrade]: {{site.flutter-docs}}/development/tools/sdk/upgrading
[Dart SDK install]: /get-dart
[Leaf's email]: https://groups.google.com/d/msg/flutter-dev/H8dDhWg_c8I/_Ql78q_6AgAJ
[prerelease]: /get-dart#release-channels
[runtime errors]: /guides/language/sound-problems#runtime-errors
[SDK constraints]: /tools/pub/pubspec#sdk-constraints
[sound Dart]: /guides/language/type-system
[testing]: /guides/testing
[Updating your pub package to Dart 2,]: https://filiph.medium.com/updating-your-pub-package-to-dart-2-cd8ca343b1be
[Using constructors]: /guides/language/language-tour#using-constructors
[sync async start]: https://github.com/dart-lang/language/blob/master/archive/newsletter/20170915.md#synchronous-async-start
