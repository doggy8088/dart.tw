---
title: Sound null safety
title: 健全的空安全
description: Information about Dart's null safety feature
description: Dart 空安全的有關內容
---

The Dart language comes with sound null safety.

Dart 語言包含了健全的空安全特性。

Null safety prevents errors that result from unintentional access
of variables set to `null`.
For example, if a method expects an integer but receives `null`,
your app causes a runtime error. This type of error, a null dereference error,
can be difficult to debug.

空安全會在編譯期防止意外存取 `null` 變數的錯誤的產生。
例如，如果一個方法需要整型結果，卻接收到了 `null`，你的應用會出現執行時錯誤。
這類空參考錯誤在以前除錯是非常困難的。

With sound null safety variables are 'non-nullable' by default:
They can be assigned only values of the declared type
(e.g. `int i=42`), and never be assigned `null`.
You can specify that a type of a variable is nullable
(e.g. `int? i`),
and only then can they contain either a `null` *or*
a value of the defined type.

有了健全的空安全體系，變數預設是「非空」的：
它們可以賦予與定義的型別相同型別的任意值（例如 `int i = 42`），
且永遠不能被設定為 `null`。
你可以指定一個類別型為可空（例如 `int? i`），
這類變數只能包含對應型別的值或者 `null`。

Sound null safety changes potential **runtime errors**
into **edit-time** analysis errors, by flagging when
any non-nullable variable hasn't been initialized with a 
non-null value or is being assigned a `null`.
This allows you to fix these errors before deploying your app.

健全的空安全透過對非空變數的未被初始化或以 `null` 初始化的情況進行標記，
把潛在的 **執行時錯誤** 轉變成了 **編輯時** 的分析錯誤。
這樣的特性讓你在開發應用的過程中就可以修復這類錯誤。

{{site.alert.warn}}

  In Dart 2.x SDKs, you can enable or disable sound null safety 
  through configuration of the project SDK constraint.
  To learn more, see [Enabling/disabling null safety](#enable-null-safety).

  在 Dart 2.x 版本的 SDK 裡，你可以透過設定專案的 SDK 限制來控制是否啟用空安全。
  你可以透過 [啟用/禁用空安全](#enable-null-safety) 小節來了解更多。

  Dart 3--planned for a mid-2023 release--
  will require sound null safety. It will prevent code from running without it.
  All existing code must be [migrated](#migrate) to sound null safety
  to be compatible with Dart 3.
  To learn more, see the [Dart 3 sound null safety tracking issue][].

  預計在 2023 年中釋出的 Dart 3 計劃強制使用空安全。
  該版本不允許未使用空安全的程式碼執行。
  所有的程式碼都需要 [遷移](#migrate) 至健全的空安全，以適配 Dart 3。
  你可以透過 [Dart 3 空安全問題追蹤][Dart 3 sound null safety tracking issue]
  來了解更多。

{{site.alert.end}}

[Dart 3 sound null safety tracking issue]: https://github.com/dart-lang/sdk/issues/49530

## Introduction through examples

## 透過範例程式碼介紹空安全

With null safety,
all of the variables in the following code are non-nullable:

有了空安全，下面程式碼中所有的變數都是非空的：

```dart
// In null-safe Dart, none of these can ever be null.
var i = 42; // Inferred to be an int.
String name = getFileName();
final b = Foo();
```

<a id="creating-variables"></a>
To indicate that a variable might have the value `null`,
just add `?` to its type declaration:

<a id="creating-variables"></a>
若你想讓變數可以為 `null`，只需要在型別聲明後加上 `?`。

```dart
int? aNullableInt = null;
```

- To try an interactive example,
  see the [null safety codelab][Null safety codelab].

  你可以透過 [空安全 Codelab][Null safety codelab] 互動範例快速瞭解空安全。

- To learn more about this topic, see
  [Understanding null safety](/null-safety/understanding-null-safety).

  更深入的探討，可以閱讀文件 [深入理解空安全](/null-safety/understanding-null-safety)。

## Null safety principles

## 空安全的原則

Dart null safety support is based on the following three core design principles:

Dart 的空安全支援基於以下三條核心原則：

* **Non-nullable by default**. Unless you explicitly tell Dart that a variable
   can be null, it's considered non-nullable. This default was chosen
   after research found that non-null was by far the most common choice in APIs.

   **預設不可空**。除非你將變數顯式宣告為可空，否則它一定是非空的型別。
   我們在研究後發現，非空是目前的 API 中最常見的選擇，所以選擇了非空作為預設值。

* **Incrementally adoptable**. You choose _what_ to migrate to null safety, and _when_.
  You can migrate incrementally, mixing null-safe and
  non-null-safe code in the same project. We provide tools to help you
  with the migration.

  **漸進遷移**。你可以自由地選擇何時進行遷移，多少程式碼會進行遷移。
  你可以使用混合模式的空安全，在一個專案中同時使用空安全和非空安全的程式碼。
  我們也提供了幫助你進行遷移的工具。

* **Fully sound**. Dart’s null safety is sound, which enables compiler optimizations.
  If the type system determines that something isn’t null, then that thing can _never_ be
  null. Once you migrate your whole project
  and its dependencies to null safety, 
  you reap the full benefits of soundness—not only 
  fewer bugs, but smaller binaries and faster execution.

  **完全可靠**。Dart 的空安全是非常可靠的，意味著編譯期間包含了很多最佳化。
  如果型別系統推斷出某個變數不為空，那麼它 **永遠** 不為空。
  當你將整個專案和其依賴完全遷移至空安全後，
  你會享有健全性帶來的所有優勢&mdash;&mdash;
  更少的 BUG、更小的二進位制檔案以及更快的執行速度。

## Enabling/disabling null safety {#enable-null-safety}

## 啟用和禁用空安全 {#enable-null-safety}

You can use sound null safety in Dart 2.12 and Flutter 2.0 or later.
Dart 3 and later will [_only_ support sound null safety][Dart 3 sound null safety tracking issue].

健全的空安全已在 Dart 2.12 和 Flutter 2.0 及更高版本中可用。
[Dart 3 及以後的版本將 **只支援** 健全的空安全][Dart 3 sound null safety tracking issue]。

<a id="constraints"></a>
To enable sound null safety, set the
[SDK constraint lower-bound](/tools/pub/pubspec#sdk-constraints)
to a [language version][] of 2.12 or later.
For example, your `pubspec.yaml` file might have the following constraints:

<a id="constraints"></a>
想要啟用健全空安全，你需要將 [SDK 的最低版本約束](/tools/pub/pubspec#sdk-constraints)
設定為 2.12 或者更高的 [語言版本][language version]。
例如，你的 `pubspec.yaml` 可以設定為如下的限制：

```yaml
environment:
  sdk: '>=2.12.0 <3.0.0'
```

[language version]: /guides/language/evolution#language-versioning

### Migrating an existing package or app {#migrate}

### 遷移現有的 package 或應用 {#migrate}

The Dart SDK includes the `dart migrate` tool.
This tool helps you migrate code that supports sound null safety. 
Use `dart migrate` if you wrote Dart code with Dart 2.12 or earlier.

Dart SDK 內建了 `dart migrate` 工具。
該工具會幫助你遷移你的程式碼至健全的空安全。
如果你的程式碼是在 Dart 2.12 或更早前編寫的（未完全支援空安全），
你就可以使用這個工具進行遷移。

```terminal
$ cd my_app
$ dart migrate
```

To learn how to migrate your code to null safety,
see the [migration guide][].

你可以閱讀 [遷移指南][migration guide] 來學習如何遷移你的程式碼至空安全。


## Where to learn more

## 學習更多

For more information about null safety, see the following resources:

想了解關於空安全的更多內容，可檢視以下的資源內容：

* [Null safety codelab][]

  [空安全 Codelab][Null safety codelab]

* [Understanding null safety][]

  [深入理解空安全][Understanding null safety]

* [Migration guide for existing code][migration guide]

  [現有程式碼的遷移指南][migration guide]

* [Null safety FAQ][]

  [空安全常見問題和解答][Null safety FAQ]
  
* [DartPad with null safety]({{site.dartpad}})

  [支援空宣告的 DartPad (DartPad with null safety)]({{site.dartpad}})

* [Null safety sample code][calculate_lix]

  [空安全範例程式碼 (Null safety sample code)][calculate_lix]

* [Null safety tracking issue][110]

  [空安全的問題追蹤 (Null safety tracking issue)][110]

* [Dart blog][]

  [Dart 官方部落格][Dart blog]

[110]: https://github.com/dart-lang/language/issues/110
[calculate_lix]: https://github.com/dart-lang/samples/tree/master/null_safety/calculate_lix
[`dart create`]: /tools/dart-create
[Dart blog]: https://medium.com/dartlang
[migration guide]: /null-safety/migration-guide
[Null safety FAQ]: /null-safety/faq
[Null safety codelab]: /codelabs/null-safety
[Understanding null safety]: /null-safety/understanding-null-safety

