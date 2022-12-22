---
title: build_runner
title: build_runner 命令
description: A tool for building, testing, and running Dart code.
description: 編譯、測試和執行 Dart 程式碼的工具。
---

The [build_runner][] package provides general-purpose commands for
generating files, including testing the generated files
or serving both source and generated files.
This page explains how to use `build_runner`.
To learn how to use build_runner with a specific package,
see the documentation for that package.

[build_runner][] 這個 Package 提供了一些用於產生檔案的通用命令，
這些命令中有的可以用於測試產生的檔案，
有的可以用於對外提供這些產生的檔案以及它們的原始碼。
透過本文以及本文結尾處更多資訊中的相關連結，
你可以對 build_runner 以及如何使用它有一個大概的瞭解。
至於如何針對某個特定 Package 使用 build_runner，
你可以參考該 Package 的相關文件。

{{site.alert.info}}

  **If you're a web developer**, use the [`webdev` tool][webdev] to
  build and serve web apps.

  **如果你是一個 Web 開發者：**，你可以使用 [`webdev` 工具][webdev] 建構和啟動 Web 應用。

{{site.alert.end}}

The build_runner commands work with _builders_—packages
that use the [Dart build system][build]
to generate output files from input files.
For example, the [json_serializable][] and [built_value_generator][]
packages define builders that generate Dart code.

build_runner 的命令需要與使用 [Dart 編譯系統][build] 
從輸入檔案產生輸出檔案的產生器 Package 配合使用。
例如，[json_serializable][] 與 [built_value_generator][] 
這兩個 Package 共同定義了產生 Dart 程式碼的產生器。

Although the Dart build system is a good alternative to
reflection (which has performance issues) and
macros (which Dart's compilers don't support),
it can do more than just read and write Dart code.
For example, the [sass_builder][] package implements a builder that
generates `.css` files from `.scss` and `.sass` files.

Dart 的編譯系統是反射（目前還有些效能問題）
和宏指令（Dart 編譯器還不支援）的替代方案，
它可以做的不僅僅是用於讀寫 Dart 程式碼。
例如，[sass_builder][] 這個 Package
實現了用於從 `.scss` 和 `.sass` 檔案產生
`.css` 檔案的產生器。

## Setting up build_runner

## build_runner 設定

To use build_runner, add a [dev dependency][] on **build_runner**
to your app's pubspec:

在你應用 pubspec 檔案的 [dev dependency][] 層新增
**build_runner** 依賴以開啟使用 build_runner：

<?code-excerpt "build_runner_usage/pubspec.yaml" from="dev_dependencies" to="build_test" replace="/args.*/# ···/g"?>
```yaml
dev_dependencies:
  # ···
  build_runner: ^2.1.0
  build_test: ^2.1.0
```

Depending on **build_test** is optional; do it if you'll be testing your code.

依賴項 **build_test** 是可選的；但是它可以讓你測試你的程式碼。

As usual after `pubspec.yaml` changes, run `dart pub get` or `dart pub upgrade`:

像往常一樣在你修改了 `pubspec.yaml` 檔案後，
記得執行 `dart pub get` 或 `dart pub upgrade` 命令以令修改生效：

```terminal
$ dart pub get
```

## Using built-in commands

## 使用內建命令

How you use the build_runner commands depends on whether you're using
the Dart SDK or the Flutter SDK.
Here are examples of using the build_runner **build** command:

build_runner 的命令使用方式取決於你當前使用的是 Dart SDK 還是 Flutter SDK。
下面是使用 build_runner 中 **build** 命令的範例：

```terminal
$ # 從含有 pubspec.yaml 檔案的目錄執行下述命令：
$ dart run build_runner build  # Dart SDK
$ flutter pub run build_runner build  # Flutter SDK
```

The build_runner package includes the following commands:

build_runner 中包含下述幾個命令：

`build`
<br> Performs a one-time build.

`build` 命令：
<br> 處理一次性建構。

`serve`
<br> Runs a development server.
  Instead of directly using this command,
  you can use [`webdev serve`,][webdev serve]
  which has convenient default behavior.

`serve` 命令：執行一個用於開發的伺服器。
你可以使用 [`webdev serve`][webdev serve] 替代該命令，
它會包含一些方便的預設功能。

`test`
<br> Runs [tests.][tests]

`test` 命令
<br>用於執行 [測試][tests]。

`watch`
<br> Launches a build server that watches for edits to input files.
  Responds to changes by performing incremental rebuilds.

`watch` 命令
<br> 啟動一個建構伺服器用於監聽輸入檔案的編輯。透過處理增量重建來響應程式碼的修改。

## More information

## 更多資訊

If you're working on web-specific code,
see the [webdev page.][webdev]

如果你編寫的程式碼針對的是 Web 應用，請查閱 [webdev 頁面][webdev]。

For details on using build_runner, see the following:

你也可以透過下述連結查閱更多有關如何使用 build_runner 相關命令的資訊：

- Documentation for packages that require you to use build_runner.
  These packages generally have a dependency
  [on build][] or [on build_runner.][]

  如果某個 Package 需要使用 build_runner，請認真閱讀該 Package 的文件。
  這些 Package 通常都需要依賴
  [build][on build] 或 [build_runner][on build_runner.]。

- Build_runner documentation:

  build_runner 的相關文件：

  - [Getting started with build_runner][]

    [開始使用 build_runner][Getting started with build_runner]

  - [Build FAQ][]

    [建構常見問題][Build FAQ]

[build]: https://github.com/dart-lang/build
[Build FAQ]: https://github.com/dart-lang/build/blob/master/docs/faq.md
[build_runner]: {{site.pub-pkg}}/build_runner
[built_value_generator]: {{site.pub-pkg}}/built_value_generator
[dev dependency]: /tools/pub/dependencies#dev-dependencies
[Getting started with build_runner]: https://github.com/dart-lang/build/blob/master/docs/getting_started.md
[json_serializable]: {{site.pub-pkg}}/json_serializable
[on build]: {{site.pub-pkg}}?q=dependency%3Abuild
[on build_runner.]: {{site.pub-pkg}}?q=dependency%3Abuild_runner
[sass_builder]: {{site.pub-pkg}}/sass_builder
[tests]: /guides/testing
[webdev]: /tools/webdev
[webdev serve]: /tools/webdev#serve
