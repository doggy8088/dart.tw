---
title: "Get started: Command-line and server apps"
title: 起步課程：編寫命令列和伺服器端應用
description: Get Dart, run and compile a small app
description: 獲取 Dart SDK，執行和編譯一個小應用
nextpage:
  url: /tutorials/server/cmdline
  title: Write command-line apps
  title: 編寫命令列應用
prevpage:
  url: /tutorials/server
  title: Dart command-line and server tutorials
  title: 課程目錄：使用 Dart 編寫命令列和伺服器端應用
js: [{url: 'https://dartpad.cn/inject_embed.dart.js', defer: true}]
---

Follow these steps to start using the Dart SDK to develop command-line and server apps.
First you’ll play with the Dart language in your browser, no download required.
Then you’ll install the Dart SDK, write a small program, and run that program using the Dart VM.
Finally, you'll use an AOT (_ahead of time_) compiler to compile your finished program to native machine code,
which you'll execute using the Dart runtime.

跟著下面這些步驟開始使用 Dart SDK 來開發命令列和伺服器應用。
首先你將在瀏覽器中執行 Dart 程式語言而不需要下載它。
接著，你需要安裝 Dart SDK 並嘗試開發一個小程式，
然後使用 Dart VM 執行它。
最後你將使用一個 AOT（**預**）編譯器將你剛才完成的程式
編譯為可以被 Dart 執行時執行的原生機器碼。

## 1. Play with Dart code in DartPad

## 1. 在 DartPad 中執行 Dart 程式碼

With [DartPad](/tools/dartpad) you can experiment with the Dart language and
APIs, no download necessary.

你可以使用 DartPad 來簡單地嘗試 Dart 程式語言和 API 且不需要下載任何東西。

For example, here's an embedded DartPad that lets you play with the code for a
small Hello World program. Click **Run** to run the app; output appears in the
console view. Try editing the source code—perhaps you'd like to change the
greeting to use another language.

例如，下面這個內嵌的 DartPad 可以讓你嘗試一個簡單的 Hello World 程式程式碼。
點選**執行**來執行應用；控制檯輸出的內容位於程式碼塊下方。
你可以嘗試更改原始碼，比如更改問候語或者其它的一些陳述式。

{{site.alert.note}}

  {% include dartpad-embedded-troubleshooting.md %}

{{site.alert.end}}

<?code-excerpt "misc/test/samples_test.dart (hello-world)"?>
```dart:run-dartpad:ga_id-hello_world
void main() {
  print('Hello, World!');
}
```

More information:

更多資訊：

* [DartPad documentation][]

  [DartPad 文件][DartPad documentation]

* [Dart language tour][]

  [Dart 語言概覽][Dart language tour]

* [Dart library tour][]

  [Dart 函式庫概覽][Dart library tour]

## 2. Install Dart

## 2. 安裝 Dart

{% include get-sdk.md %}

## 3. Create a small app

## 3. 建立一個小應用

Use the [`dart create`](/tools/dart-create) command
and the `console` template to create a command-line app:

使用 [`dart create`](/tools/dart-create) 命令，
以 `console-full` 範本建立一個命令列應用：

```terminal
$ dart create -t console cli
```

This command creates a small Dart app that has the following:

該命令會建立一個包含下述資訊的 Dart 應用：

* A main Dart source file, `bin/cli.dart`, that contains a top-level
  `main()` function. This is the entrypoint for your app.

  一個主要的 Dart 原始檔，`bin/cli.dart`，該檔案包含一個最上層 `main()` 函式。
  該函式是你應用的入口。

* An additional Dart file, `lib/cli.dart`, that contains the functionality of
  the app and is imported by the `cli.dart` file.

  一個額外的 Dart 檔案，`lib/cli.dart`，包含一些功能性的函式方法，
  這些函式方法將會匯入到 `cli.dart` 檔案中。

* A pubspec file, `pubspec.yaml`, that contains the app's metadata, including
  information about which [packages](/guides/packages) the app depends on
  and which versions of those packages are required.

  一個 pubspec 檔案，`pubspec.yaml`，包含應用的元資料，包括應用依賴的
  [package](/guides/packages) 資訊以及所需的版本等。

{{site.alert.note}}

  Under the hood, `dart create` runs [`dart pub get`][], which
  scans the generated pubspec file and downloads dependencies.
  If you add other dependencies to your pubspec file,
  then run `dart pub get` to download them.

  實際上，`dart create` 會執行 [`dart pub get`][]，
  掃描產生的 pubspec 檔案和下載的依賴。
  如果你在 pubspec 檔案中添加了其他的依賴，執行 `dart pub get` 進行下載。

{{site.alert.end}}

[`dart pub get`]: /tools/pub/cmd/pub-get

## 4. Run the app

## 4. 執行應用

To run the app from the command line, use the Dart VM by running the
[`dart run`](/tools/dart-run) command in the app's top directory:

想要從命令列執行應用，請使用 [`dart run`](/tools/dart-run) 命令
在應用的根目錄執行 Dart VM：

```terminal
$ cd cli
$ dart run
Hello world: 42!
```

If you want to run the app with debugging support, see
[Dart DevTools](/tools/dart-devtools).

## 5. Modify the app

## 5. 修改應用

Let's customize the app you just created.

 現在我們來自訂剛才你所建立的應用。

 1. Edit `lib/cli.dart` to calculate a different result. For example, divide the
    previous value by two (for details about `~/`, see [Arithmetic operators][]):

    編輯 `lib/cli.dart` 以返回一個不同的結果。例如，將先前的值除以2。
    （關於 `~/` 的詳情請檢視 [Arithmetic operators][]）：

    <?code-excerpt "misc/test/tutorial/get_started.dart (calculate)" replace="/~\/ 2/[!$&!]/g"?>
    {% prettify dart tag=pre+code %}
    int calculate() {
      return 6 * 7 [!~/ 2!];
    }
    {% endprettify %}

 1. Save your changes.

    儲存你剛才所做的改變。

 1. Rerun the main entrypoint of your app:

    重新執行你應用的入口 main 函式：

    ```terminal
    $ dart run
    Hello world: 21!
    ```

More information:
[Write command-line apps](/tutorials/server/cmdline)

更多資訊：[開發命令列應用](/tutorials/server/cmdline)

## 6. Compile for production

## 6. 編譯成正式產品

The steps above used the Dart VM (`dart`) to run the app. The Dart VM is
optimized for fast, incremental compilation to provide instant feedback
during development. Now that your small app is done,
it's time to AOT compile your Dart code to optimized native machine code.

上面的範例步驟我們使用的是 Dart VM（即 `dart` 命令）執行的應用。
Dart VM 針對快速增量編譯進行了最佳化，以便在開發過程中提供即時的響應。
現在你的小應用已經完成，是時候 AOT 最佳化編譯你的 Dart 程式碼為原生機器程式碼了。

Use the `dart compile` tool to AOT compile the program to machine code:

使用 `dart compile` 工具將程式 AOT 編譯成機器程式碼：

```terminal
$ dart compile exe bin/cli.dart
```
Notice how the compiled program starts instantly, completing quickly:

看看編譯後的程式啟動有多快：

```terminal
$ time bin/cli.exe
Hello world: 21!

real	0m0.016s
user	0m0.008s
sys	0m0.006s
```

## What next?

## 接下來做什麼？

Check out these resources:

檢索這些資源：

* Additional tutorials and codelabs for Dart

  其它額外的一些 Dart 課程和指引

  * [Tutorials](/tutorials)

    [課程](/tutorials)

  * [Codelabs](/codelabs)

    [指引](/codelabs)

* Dart language, libraries, and conventions

  Dart 語言、庫以及習慣用法

  * [Sample code](/samples)

    [範例程式碼](/samples)

  * [Language tour](/guides/language/language-tour)

    [語言概覽](/guides/language/language-tour)

  * [Library tour](/guides/libraries/library-tour)

    [函式庫概覽](/guides/libraries/library-tour)

  * [Effective Dart](/guides/language/effective-dart)

    [高效 Dart 程式設計](/guides/language/effective-dart)

* Tools and libraries

  工具和庫

  * [Dart SDK](/tools/sdk)
  * [Dart tools](/tools)

    [Dart 工具](/tools)

  * [IDEs](/tools#ides-and-editors)
* Other examples of natively compiled apps
  * [native_app](https://github.com/dart-lang/samples/tree/master/native_app)

If you get stuck, find help at [Community and support.](/community)

如果你在這一步無法繼續進行，可以從 [社群和幫助](/community) 中查詢幫助。

[Arithmetic operators]: /guides/language/language-tour#arithmetic-operators
[DartPad documentation]: /tools/dartpad
[Dart language tour]: /guides/language/language-tour
[Dart library tour]: /guides/libraries/library-tour
[ide]: /tools#ides-and-editors
