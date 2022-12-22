---
title: dart format
title: dart format 命令（用於格式化 Dart 程式碼的命令）
description: Command-line tool for formatting Dart source code.
description: 用於格式化 Dart 原始碼的命令列工具。
toc: false
---

Use the `dart format` command to replace the whitespace in your program
with formatting that follows
[Dart guidelines](/guides/language/effective-dart/style#formatting).
This is the same formatting that you can get
when using an IDE or editor that has Dart support.

使用 `dart format` 命令可以根據
[Dart 指南](/guides/language/effective-dart/style#formatting)
將你程式中多餘的空格替換掉。
這與你使用支援 Dart 的 IDE 或者編輯器格式化程式碼的效果相同。

Provide a list of files or directories to the `dart format` command.
For example, here's how to format all the Dart files
in or under the current directory:

`dart format` 命令接收一系列檔案或目錄作為引數。
例如，下面是如何格式化當前目錄下所有 Dart 檔案的範例：

```terminal
$ dart format .
```

To specify multiple files or directories,
use a space-delimited list.
The following command formats all Dart files under the `lib` directory,
plus one Dart file under the `bin` directory:

要指定多個檔案或目錄，請使用以空格符號分割的列表。
下面的命令將會對 `lib` 目錄中的所有 Dart 檔案，
以及一個 `bin` 目錄下的 Dart 檔案進行格式整理。

```terminal
$ dart format lib bin/updater.dart 
```

{{site.alert.warn}}
  By default, `dart format` **overwrites** the Dart files.
{{site.alert.end}}

If you don't want to overwrite the files,
add the `--output` or `-o` flag.
Use `-o show` or `-o json` to get the contents of the formatted files,
or `-o none` to see only which files would change.

如果你不想覆蓋檔案，請去掉 `-w` 選項。原始碼格式化後的結果將作為標準輸出顯示出來。

```terminal
$ dart format -o show bin/my_app.dart
```

To make the command have an exit code of `1`
if any formatting changes occur,
add the `--set-exit-if-changed` flag.
This exit code is often used with continuous integration (CI)
to indicate that a check should fail.

```terminal
$ dart format -o none --set-exit-if-changed bin/my_app.dart
```

For information on additional command-line options,
use the `dart help` command or see the documentation for the
[dart_style package.]({{site.pub-pkg}}/dart_style)

使用 `--help` 命令列引數或者查閱 [dart_style Package]({{site.pub-pkg}}/dart_style) 文件可以檢視更多命令列選項。

```terminal
$ dart help format
```

{% comment %}
[PENDING: Add info on commonly used options.]

[PENDING: Advocate using this! Perhaps steal the first paragraph from
dart_style's readme:

The dart_style package defines an automatic, opinionated formatter
for Dart code. It replaces the whitespace in your program with what
it deems to be the best formatting for it. Resulting code should
follow the Dart style guide and, more importantly, should look nice
to most human readers, most of the time.]
{% endcomment %}
