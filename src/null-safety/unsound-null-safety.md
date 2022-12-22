---
title: Unsound null safety
title: 非健全的空安全
description: Mixing language versions lets you migrate to null safety at your own pace, with some of the benefits of null safety.
description: 非健全的空安全讓你可以以自己的步調遷移到空安全，同時可以享受一些空安全的好處。
---

A Dart program can contain some libraries that
are [null safe][] and some that aren't.
These **mixed-version programs**
execute with **unsound null safety**.

一個 Dart 程式可以同時包含已經是 [空安全][null safe] 和未遷移至空安全的函式庫。
這些 **混合模式的程式** 會執行在 **非健全的空安全** 下。

[null safe]: /null-safety

The ability to mix [language versions][]
frees package maintainers to migrate their code,
with the knowledge that even legacy users can get new
bug fixes and other improvements.
However, mixed-version programs don't get all the advantages
that null safety can bring.

[混合模式版本][language versions] 的空安全，
讓軟體套件的維護者可以遷移至空安全的同時，
未遷移至空安全的使用者也可以享受新的問題修復和其他改進。
然而，混合模式的程式無法擁有空安全帶來的所有優勢。

[language versions]: /guides/language/evolution#language-versioning

This page describes the differences between sound and unsound null safety,
with the goal of helping you decide when to migrate to null safety.
After the conceptual discussion are instructions for migrating incrementally,
followed by details on testing and running mixed-version programs.

本文將描述健全和非健全的空安全之間的區別，讓你可以為何時進行空安全遷移下定論。

{{site.alert.note}}

  We recommend that, if possible, you wait for dependencies to migrate
  before you migrate your package.
  For details, see the [migration guide][].

  我們希望你能儘可能地等到所有你依賴的函式庫都遷移完成後，再對你的軟體套件進行空安全遷移。
  更多細節，請參考 [遷移指南][migration guide]。

{{site.alert.end}}

[migration guide]: /null-safety/migration-guide


## Sound and unsound null safety

## 健全和非健全的空安全

Dart provides sound null safety through a combination of
static and runtime checks.
Each Dart library that opts in to null safety gets
all the _static_ checks, with stricter compile-time errors.
This is true even in a mixed-version program that contains
null-unsafe libraries.
You start getting these benefits
as soon as you start migrating some of your code to null safety.

Dart 透過一系列的靜態和執行時期檢查來提供健全的空安全。
每一個使用了空安全的 Dart 庫都會擁有所有的 **靜態** 檢查和更嚴格的編譯期的錯誤提醒。
對於包含了空安全庫的混合模式程式也是如此。
程式碼一旦開始遷移，已遷移的部分就能立刻享有到它帶來的好處。

However, a mixed-version program can't have the
_runtime_ soundness guarantees that a fully null-safe app has.
It's possible for `null` to leak out of the null-unsafe libraries
into the null-safe code, because
preventing that would break the existing behavior of the unmigrated code.

然而，混合模式的程式無法獲得與空安全的程式的 **執行時** 健全性一致的保證。
`null` 很可能從非空安全的函式庫汙染到空安全的程式碼，
因為一旦它被阻止，就會對現有的程式碼行為造成破壞。

To maintain runtime compatibility with legacy libraries
while offering soundness to completely null-safe programs,
Dart tools support two modes:

為了在保持與傳統庫執行時的相容性的同時，能為健全的空安全程式提供健全性，
Dart 工具提供了以下兩種模式的支援：

* Mixed-version programs run with **unsound null safety**.
  It's possible for `null` reference errors to occur at runtime,
  but only because a `null` or nullable type escaped from
  some null-unsafe library and got into null-safe code.

  以 **非健全的空安全** 執行的混合模式的程式。
  在執行時有可能出現 `null` 參考錯誤，
  但這只是因為一些 `null` 值和可空型別在非空安全的函式庫中汙染了空安全的程式碼。

* When a program is fully migrated and _all_ its libraries are null safe,
  then it runs with **sound null safety**, with
  all of the guarantees and compiler optimizations that soundness enables.

  當程式完全遷移至空安全，且它所依賴的函式庫 **全部** 都遷移完成後，
  它就在以 **健全的空安全** 執行，擁有所有由健全性帶來的保證和編譯最佳化。

Sound null safety is what you want if possible.
Dart tools automatically run your program in sound mode if
the main entrypoint library of your program has opted into null safety.
If you import a null-unsafe library,
the tools print a warning to let you know that
they can only run with unsound null safety.

健全的空安全唾手可得。
當你的程式入口的函式庫已經遷移至空安全，Dart 會自動以健全的空安全執行你的程式碼。
如果你匯入了非空安全的函式庫，會有一條提示告訴你，你的程式只能
以非健全的空安全執行。

## Migrating incrementally

## 逐步遷移

Because Dart supports mixed-version programs,
you can migrate one library (generally one Dart file) at a time,
while still being able to run your program and its tests.

因為 Dart 支援混合模式的空安全，
所以你可以一個個遷移你的函式庫（通常是一個檔案），同時能正常執行程式和測試。

We recommend that you **first migrate leaf libraries**—libraries 
that don't import other files from the package.
Then migrate libraries that directly depend on the leaf libraries.
End by migrating the libraries that have the most
intra-package dependencies.

我們推薦你 **優先遷移最下層的函式庫**——指的是沒有匯入其他套件的庫。
接著遷移直接依賴了下層庫的依賴庫。
最後再遷移依賴項最多的函式庫。

For example, say you have a `lib/src/util.dart` file
that imports other (null-safe) packages and core libraries,
but that doesn't have any `import '<local_path>'` directives.
Consider migrating `util.dart` first,
and then migrating files that depend only on `util.dart`.
If any libraries have cyclic imports
(for example, A imports B which imports C, and C imports A),
consider migrating those libraries together.

舉個例子，假設你的 `lib/src/util.dart` 匯入了其他（空安全）的軟體包和核心庫，
但它沒有包含任何 `import '<本地路徑>'` 的參考。
那麼你應當優先考慮遷移 `util.dart`，然後遷移依賴了 `util.dart` 的檔案。
如果有一些迴圈參考的函式庫（例如 A 參考了 B，B 參考了 C，C 參考了 A），
建議同時對它們進行遷移。

### Using the migration tool

### 使用遷移工具

You can migrate incrementally using the
[migration tool][].
To opt out files or directories, click the green checkbox.
In the following screenshot,
all files in the `bin` directory are opted out.

你可以使用 [遷移工具][migration tool] 進行漸進遷移。
如果你需要排除部分檔案或資料夾，勾選綠色的勾選框。
下方的截圖中，`bin` 資料夾的所有檔案都已被排除。

![Screenshot of file viewer in migration tool](/null-safety/migration-tool-incremental.png)

[migration tool]: /null-safety/migration-guide#step2-migrate

Each opted out file will be unchanged
except for a 2.9 [language version comment][].
You can later run `dart migrate` again to continue the migration.
Any files that are already migrated feature a disabled checkbox:
you cannot un-migrate a file once it has been migrated.

每個不遷移的檔案都會加上 2.9 [語言版本的註釋][language version comment]。
你可以之後再次執行 `dart migrate` 繼續遷移。
已遷移的檔案將顯示為禁用的勾選框，它們無法撤銷遷移更改。

### Migrating by hand

### 手動進行遷移

If you want to incrementally migrate a package by hand, follow these steps:

手動對 package 進行遷移時，請參考以下步驟：

1. Edit the package's `pubspec.yaml` file,
   setting the minimum SDK constraint to `2.12.0`:

   編輯 package 的 `pubspec.yaml` 檔案，
   將最低 SDK 版本設定到 `2.12.0`：

   ```yaml
   environment:
     sdk: '>=2.12.0 <3.0.0'
   ```

2. Regenerate the [package configuration file][]:

   重新產生 [Package 的配置檔案][package configuration file]：

   ```terminal
   $ dart pub get
   ```

   [package configuration file]: https://github.com/dart-lang/language/blob/master/accepted/2.8/language-versioning/package-config-file-v2.md

   Running `dart pub get` with a lower SDK constraint of `2.12.0`
   sets the default language version of
   every library in the package to 2.12,
   opting them all in to null safety.

   在版本低於 `2.12.0` 的 SDK 上執行 `dart pub get` 時，
   會將每個 package 的預設 SDK 版本設定為 2.12，並且預設它們已經遷移至空安全。

3. Open the package in your IDE. <br>
   You're likely to see a lot of analysis errors.
   That's OK.

   在你的 IDE 上開啟 package。<br>
   你也許會看到很多錯誤，沒關係，讓我們繼續。

4. Add a [language version comment][] to the top of
   any Dart files that you don't want to consider during your current migration:

   在所有你不考慮進行遷移的 Dart 檔案頂部加上
   [語言版本註釋][language version comment]。

   ```dart
   // @dart=2.9
   ```

   Using language version 2.9 for a library that's in a 2.12 package
   can reduce analysis errors (red squiggles) coming from unmigrated code.
   However, **unsound null safety reduces the
   information the analyzer can use.**
   For example, the analyzer might assume a
   parameter type is non-nullable,
   even though a 2.9 file might pass in a null value.

   在 2.12 的 package 中為庫指定 2.9 的語言版本可以減少一些未遷移的分析錯誤。
   然而，**非健全的空安全減少了分析器中可用的資訊**。
   例如，就算 2.9 版本的檔案中一個引數可能會傳入空值，分析器也可能會假定引數型別不為空，

5. Migrate the code of each Dart file,
   using the analyzer to identify static errors. <br>
   Eliminate static errors by adding `?`, `!`, `required`, and `late`,
   as needed.

   利用分析器來辨析靜態錯誤，逐個遷移 Dart 檔案。<br>
   按需新增 `?`、`!`、`required` 以及 `late` 來消除靜態錯誤。

## Testing or running mixed-version programs

## 測試或執行混合版本的程式

To test or run mixed-version code,
you need to disable sound null safety.
You can do this in two ways:

想要測試或執行混合版本的程式碼，你需要禁用健全的空安全。
有兩種方式可以進行操作：

* Disable sound null safety using the `--no-sound-null-safety` flag
  to the `dart` or `flutter` command:

  在 `dart` 和 `flutter` 命令裡，加入 `--no-sound-null-safety` 標記禁用。
  例如：

  ```terminal
  $ dart --no-sound-null-safety run
  $ flutter run --no-sound-null-safety
  ```

* Alternatively, set the language version in the 
  entrypoint—the file that contains `main()` function—to 2.9.
  In Flutter apps, this file is often named `lib/main.dart`.
  In command-line apps, this file is often named `bin/<packageName>.dart`.
  You can also opt out files under `test`,
  because they are also entrypoints.
  Example:

  或者，設定包含 `main()` 函式的檔案程式入口的語言版本為 2.9。
  在 Flutter 應用中，一般是 `lib/main.dart`。
  在命令列應用中，一般是 `bin/<package 名稱>.dart`。
  同時你也可以設定 `test` 下的檔案，因為它們也包含程式入口。
  例如：

  ```dart
  // @dart=2.9
  import 'src/my_app.dart';

  void main() {
    //...
  }
  ```
  
Opting out tests using either of these mechanisms can be useful
for testing **during** your incremental migration process,
but doing so means that you aren't testing your code with
full null safety enabled.
It's important to opt your tests back _in_ to null safety
when you've finished the incremental migration of your libraries.

以上兩種方式的規避，對於 **正在** 增量遷移的過程非常有用，
但這樣做意味著你並未在完全啟用空安全的情況下測試你的程式碼。
當你完成增量遷移後，也請記得將測試程式碼 **重新** 遷移至空安全。

[language version comment]: /guides/language/evolution#per-library-language-version-selection
