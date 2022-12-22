---
title: dart run
title: dart run 命令
description: Command-line tool for running a Dart program.
description: 執行Dart程式的命令列工具。
---

The `dart run` command supports running 
a Dart program—located in a file, in the current package, 
or in one of the dependencies of the current package—from the command line.
This command provides functionality that was previously in `pub run`
and the Dart VM tool.
To run a program from an arbitrary location,
use the [pub global](/tools/pub/cmd/pub-global) command.

`dart run` 命令支援執行檔案、當前 package
或者當前 package 的依賴中執行Dart程式。
該命令提供了以前在 `pub run` 和 Dart VM 工具裡提供的功能。
如果想從任意位置執行程式，可以使用 [pub global](/tools/pub/cmd/pub-global) 命令。

```
dart run [options] [<DART_FILE> | <PACKAGE_TARGET>] [args]
```

Here's an example of creating a new app and running it:

下面的例子示範瞭如何建立並且執行一個 Dart 程式：

```terminal
$ dart create myapp
$ cd myapp
$ dart run
```

{% include tools/dart-tool-note.md %}

## Running a Dart file

## 執行 Dart 檔案

You can run a Dart file by passing its relative path:

可以透過傳入 Dart 檔案的相對路徑來執行一個 Dart 程式：

```terminal
$ dart run tool/debug.dart
```

## Running a program that's in a package

## 執行 package 中的 Dart 程式

The instructions in this section assume that
you're executing the `dart run` command
from the directory that's at the top of a Dart package
(the _current package_).
For information on the directory structure of Dart packages, see
[package layout conventions](/guides/libraries/create-library-packages).

本節說明的前提是你在 package 的根目錄執行 `dart run` 命令。
更多關於 package 目錄結構的介紹，請查閱
[package 佈局約定](/guides/libraries/create-library-packages)。

### In a depended-on package

### 執行依賴 package 中的 Dart 程式

You can run programs that are
distributed in the `bin` directory of any package
that the current package depends on.
To run such a program,
specify the depended-on package name and the program name.
You can omit the program name if it's the same as the package name.

你可以運行當前 package 所依賴的任何 package 的 Dart 程式，只要它在 `bin` 目錄下。
執行時請指定依賴的 package 名和程式名。
如果程式名與 package 名相同，則可以省略。

For example, say you're in the top directory of a package
that depends on the `bar` package.
To run the main program that's in the `bar` package (`bin/bar.dart`),
you can use this command:

例如，在 package 的根目錄，
想執行一個名為 `bar` 的依賴 package 中的 main 程式（`bin/bar.dart`），
使用如下命令：

```terminal
$ dart run bar
```

If the program name doesn't match the package name,
use the form `<package name>:<program name>`. For example,
to run the program `bin/baz.dart` that's in the `bar` package,
use this command:

如果程式名和 package 名不同，
則需要使用 `<package 名>:<程式名>` 的形式來執行程式。
比如 `bin/baz.dart` 程式在名為 `bar` 的 package 中, 我們就可以這樣執行:

```terminal
$ dart run bar:baz
```

The `bin` directory is the only place with visible programs.
All other directories in the depended-on package are private.

放在 `bin` 目錄下的 Dart 檔案是可以被 Dart 命令列工具直接存取的，
依賴 package 中的其他目錄都是私有的。

### In the current package

### 在當前 package 中執行 Dart 程式

When the current directory matches the package name
(that is, you're in the directory that matches
the `name` property in the pubspec),
then you can omit the package name.
If the program name matches the package name
(that is, it's the main program),
then you can also omit the program name.

噹噹前的路徑與 package 名匹配時（即資料夾名稱與 pubspec 中的 `name` 屬性相同），
可以省略 package 名。
如果程式名與 package 名匹配（也就是它是 main 程式），
也可以省略程式名。

Here's the shortest form of `dart run`,
which runs the main program for the current package.
For example, if you're in the top directory of the `foo` package,
this command runs `bin/foo.dart`:

`dart run` 是執行 Dart 程式最短的形式，
它會運行當前 package 的主程式。
例如，如果你在名為 `foo` 的 package 根目錄執行下面命令，
實際上會執行 `bin/foo.dart` 檔案：

```terminal
$ dart run
```

If the program name doesn't match the package name,
then add a colon and the program name.
For example, this command runs `bin/baz.dart` in the current package:

如果程式名和 package 名不同，需要使用 `:<程式名>` 的形式來執行程式，
比如運行當前 package 中的 `bin/baz.dart` 檔案:

```terminal
$ dart run :baz
```

To run a program that's in the current package but not in the `bin` directory,
pass a relative path (as shown before):

要執行在當前 package 但不在 `bin` 目錄的程式時，透過傳遞相對路徑執行（如前文所示）：

```terminal
$ dart run tool/debug.dart
```

## Supplying arguments to main()

## 給 main() 傳遞引數

To supply [arguments to the `main()` function][args],
put them at the end of the command:

若你需要 [給 `main()` 函式傳遞引數][args]，在命令列後面新增引數即可：

```terminal
$ dart run tool/debug.dart arg1 arg2
```

When you're running the main program for the current package,
add the package name.
Here's an example of running `bin/foo.dart` with arguments
while you're in the top directory of the `foo` package:

在當前 package 中執行 `main` 程式需要新增 package 名，
比如要傳遞引數並執行位於 `foo` package 根目錄中的 `bin/foo.dart` 程式：

```terminal
$ dart run foo arg1 arg2
```

[args]: /guides/language/language-tour#the-main-function

## Debugging

## 除錯

To enable debugging, 
add one or more of these common debugging options
to your `dart run` command:

若要啟用除錯功能，
將下面的除錯選項加入到 `dart run` 命令中:

- To enable [`assert` statements][assert],
  add the `--enable-asserts` flag:

  要啟用 [`assert` 斷言][assert]，
  加入 `--enable-asserts` 命令列引數:

  ```terminal
  $ dart run --enable-asserts tool/debug.dart
  ```

- To enable debugging and performance analysis
  through [Dart DevTools](/tools/dart-devtools),
  add the `--observe` flag:

  若要使用 [Dart 開發者工具](/tools/dart-devtools) 
  來除錯和做效能分析，加入 `--observe` 命令列引數:

  ```terminal
  $ dart run --observe tool/debug.dart
  ```
  
  To learn more about debugging with Dart DevTools,
  see [Using DevTools with a command-line app][].

  瞭解更多使用 Dart 開發者工具進行除錯的資訊，
  請查閱文件: [在 Dart 命令列中使用開發者工具][Using DevTools with a command-line app]。

To learn more about other debugging options, run `dart run --help`.

執行 `dart run --help` 獲取更多資訊。

[assert]: /guides/language/language-tour#assert
[Using DevTools with a command-line app]: /tools/dart-devtools#using-devtools-with-a-command-line-app

## Enabling experimental features

## 開啟實驗性功能

To enable new features and enhancements that are currently in development,
use [experiment flags](/tools/experiment-flags).

如果在開發中想嘗試實驗性功能，可以使用 [實驗性功能開關](/tools/experiment-flags)。
