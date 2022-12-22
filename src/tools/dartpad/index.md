---
title: DartPad
title: DartPad 線上工具
description: The tool that lets you interactively play with Dart in a browser.
description: 在瀏覽器裡體驗 Dart 程式語言的工具。
---

DartPad is an [open source tool](https://github.com/dart-lang/dart-pad)
that lets you play with the Dart language in any modern browser.
Many pages in this site—especially [codelabs](/codelabs)—have
[embedded DartPads](#embedding).
To open DartPad as a standalone web page, visit 
the [DartPad site (dartpad.dev)][DartPad]{:target="_blank" rel="noopener"}.

DartPad 是一個可以讓你在任何現代化的瀏覽器中體驗 Dart 程式語言線上工具，
它是 [開源](https://github.com/dart-lang/dart-pad) 的。
本網頁裡一些頁面，尤其是 [codelabs](/codelabs) 頁面，
包含了很多[內嵌的 DartPads](#embedding)。
你可以在瀏覽器裡輸入下面的網址開啟一個 DartPad 頁面。
<a href="{{site.dartpad}}"
target="_blank">DartPad 頁面 (dartpad.cn).</a>

{{site.alert.tip}}

  If you're in China, try [dartpad.cn.](https://dartpad.cn)
  
  如果你在中國，請嘗試使用 [dartpad.cn](https://dartpad.cn)。

  If you have issues using DartPad, see the [DartPad troubleshooting
  tips](/tools/dartpad/troubleshoot).
  
  如果你在使用 DartPad 的過程中有任何的問題，
  請查閱 [DartPad 常見問題頁面](/tools/dartpad/troubleshoot)。
  
{{site.alert.end}}

Here's what DartPad looks like when configured to run Dart:

DartPad 看起來是這樣的：

<img 
   src="/assets/img/dartpad-hello.png" 
   alt="Showcases what a Hello World app looks like in DartPad">


## Library support

## 可以在 DartPad 中使用的函式庫

DartPad supports `dart:*` [core libraries](/guides/libraries) marked as
multi-platform and web platform. When writing Flutter apps,
DartPad also supports the `package:flutter`
and `dart:ui` libraries.

DartPad 支援標記為支援多平臺以及 Web 平台的 `dart:*` [核心函式庫](/guides/libraries)。
還支援在 Flutter 應用裡使用 `package:flutter` 和 `dart:ui` 庫。

DartPad doesn't support [deferred loading][] 
or using packages from the [pub.dev]({{site.pub}}) package repository
besides the [currently supported packages][].

DartPad 尚不支援 [延遲載入][deferred loading]，
除了 [列舉出的這些支援的 package][currently supported packages] 之外，
DartPad 尚不支援使用其他在 pub.dev 上的 package。

[currently supported packages]: https://github.com/dart-lang/dart-pad/wiki/Package-and-plugin-support#currently-supported-packages

DartPad 不支援 [延遲載入][deferred loading] 以及其他三方庫。
例如，DartPad 不支援使用 [pub.dev]({{site.pub}}) 儲存庫中的 package。
（對於 Package 的支援之後可能會有所變化，詳情請關注[issue 901][]。）

## Getting started

## 開始體驗

To get familiar with DartPad,
try running some samples and creating a simple command-line app.

為了可以儘快熟悉 DartPad，
你可以嘗試在 DartPad 中執行一些範例，然後在 DartPad 中建立一個簡單的命令列應用。

### Open DartPad and run a sample {#step-1-open-and-run}

### 開啟 DartPad 並執行一些範例 {#step-1-open-and-run}

1. Go to [DartPad][]{:target="_blank" rel="noopener"}.

   開啟 [DartPad][]
   
   Dart code appears on the left, and 
   a place for the output appears on the right.

   Dart 範例程式碼會出現在左邊，而程式碼執行後的輸出則會出現在右邊。


2. Choose a Flutter sample such as **Sunflower**, 
   using the **Samples** list at the upper right.

   選擇一個 Flutter 範例，例如 **Sunflower**，使用右上角的 **範例** 列表。
   
   The rendered output appears to the right.

   它的輸出會出現在右邊輸出框裡。
  </li>
</ol>


### Create a command-line app {#step-2-server}

### 建立一個命令列應用 {#step-2-server}

To create a simple command-line app, use **New Pad**.

使用 **New Pad** 來建立一個簡單的命令列應用。

1. Click the **New Pad** button,
   and confirm that you want to discard changes to the current pad.

   點選 **New Pad** 按鈕並確認你想放棄當前 Pad 中的修改。

2. Click the Dart logo, make sure that **HTML** support is disabled,
   and then click **Create**.

   點選 Dart logo，確保 **HTML** 支援已禁用，接著點選 **Create**。

3. Change the code. For example, change the `main()` function
   to contain this code: 

   然後修改程式碼。比如將 `main()` 函式中的程式碼修改為如下：

   <!-- library-tour/string-tests/bin/main.dart -->
   {% prettify dart tag=pre+code %}
   for (final char in 'hello'.split('')) {
     print(char);
   }
   {% endprettify %}  
   
   As you type, DartPad shows hints, documentation,
   and autocomplete suggestions.

   在你輸入的時候，DartPad 會顯示提示、文件以及自動完成建議。

4. Click the **Format** button.  

   點選 **Format** 按鈕。

   DartPad uses the [Dart formatter](/tools/dart-format)
   to ensure that your code has proper indentation, white space,
   and line wrapping.

   DartPad 會使用 [Dart 格式化器](https://github.com/dart-lang/dart_style#readme) 
   來格式化你的程式碼以確保有合適的縮排、空格和換行。

5. Run your app.

   執行你的應用。

6. If you didn't happen to have any bugs while you were entering the code,
   try introducing a bug.  

   如果你寫的程式碼沒有顯示任何 BUG，那麼請嘗試自行寫出一個 BUG。

   For example, if you change `split` to `spit`,
   you get warnings at the bottom right of the window.
   If you run the app, a compilation error appears in the console.

   如果你將 `split` 寫成 `spit`，
   你會在 DartPad 視窗的底部以及 Run 按鈕上看到一些警告資訊。
   如果你強行執行應用，你將會在控制檯中看到一個例外。



## Checking Dart version info

## 檢查 Dart 版本資訊

The language features and APIs that DartPad supports depend on the
**Dart SDK** version that DartPad is currently using.
You can find this SDK version at the bottom right of DartPad.

DartPad 支援的語言功能和 API 取決於 DartPad 使用的 **Dart SDK** 版本。
你可以在 DartPad 的右下方找到其所使用的 SDK 版本。

## Embedding DartPad in web pages {#embedding}

## 網頁中嵌入 DartPad {#embedding}

You can embed DartPad inside of web pages,
customizing it to suit your use case.
For example, the [futures codelab][]
contains multiple embedded DartPads
labeled as _examples_ and _exercises_.

你可以將 DartPad 嵌入到網頁中，根據你的用途對其進行自訂。
例如 [futures codelab][] 包含了多個嵌入的 DartPads
並把它們用作 _examples_ 和 _exercises_。

For more information about how to use embedded DartPads, see
[best practices for using DartPad in tutorials][].

更多有關如何使用嵌入式 DartPads 的相關資訊，參見[使用 DartPad 的最佳實踐指南][best practices for using DartPad in tutorials]。

For technical details on embedding DartPads, see the
[DartPad embedding guide.][]

有關嵌入式 DartPads 的具體技術細節，參見 [DartPad 嵌入指南。][DartPad embedding guide.]

[DartPad]: {{site.dartpad}}
[best practices for using DartPad in tutorials]: /resources/dartpad-best-practices
[DartPad embedding guide.]: https://github.com/dart-lang/dart-pad/wiki/Embedding-Guide
[deferred loading]: /guides/language/language-tour#lazily-loading-a-library
[futures codelab]: /codelabs/async-await
