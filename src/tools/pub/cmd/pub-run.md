---
title: pub run
title: pub run
description: Use pub run to run a Dart script in your package.
description: 使用 pub run 命令執行你 Package 中的 Dart 指令碼。
---

_Run_ is one of the commands of the [pub tool](/tools/pub/cmd).

_Run_ 命令是 [Pub 工具](/tools/pub/cmd) 中的一個命令。

{% prettify nocode tag=pre+code %}
$ pub run [--enable-asserts] <executable> [args...]
{% endprettify %}

Use this command to run a Dart script in your package,
or in one of its dependencies, from the command line.

使用該命令可以從命令列執行一個位於你 Package 中或 Package 依賴項中的指令碼。

To run an executable when you are not currently inside a package,
use the [pub global](/tools/pub/cmd/pub-global) command.

可以使用 [pub global](/tools/pub/cmd/pub-global) 命令來執行一個不存在於當前 Package 中的可執行物件。

## Running a script in your package's bin directory

## 執行位於你 Package bin 目錄下的指令碼

This is the simplest use case.

這是一個最簡單的使用範例。

From the root of a package that contains `foo.dart`
in the `bin` directory, run the app using the following command:

假設一個 Package 根目錄下包含 `bin` 目錄，且該目錄中包含有 `foo.dart` 檔案，則你可以使用下述命令執行該應用：

```terminal
$ pub run foo arg1 arg2
```

This command looks in your package's `bin` directory for the
specified script and invokes it, passing in any arguments.

該命令會查詢位於你 Package `bin` 目錄下的指定指令碼並以指定引數執行它。

## Running a script in another directory in your package

## 執行位於你 Package 其它目錄下的指令碼

To run a script inside a directory other than the top-level
bin directory (but within the package), prepend the path
to the name of the script.
For example, to run `foo.dart` in the `example/sub` directory:

如果你想執行位於 Package 其它目錄中的指令碼，只需要在指令碼前加上對應的目錄地址即可。例如，執行位於 `example/sub` 目錄下的 `foo.dart`：

```terminal
$ pub run example/sub/foo arg1 arg2
```

## Running a script in a dependency

## 執行位於依賴項中的指令碼

To run a script from the `bin` directory of a package that you depend on
in the pubspec, specify the package name.
For example, to run `bar.dart` in the foo package:

如果你想執行的指令碼不在當前 Package 中而是在當前 Package 所依賴的其它 Package 的 `bin` 目錄中，那麼你需要在執行該指令碼時指定 Package 的名稱。例如執行名為 foo 的 Package 中的 `bar.dart` 指令碼：

```terminal
$ pub run foo:bar arg
```

You can only run scripts out of another package's `bin` directory.
All other directories are private.

你只能執行位於其它 Package `bin` 目錄下的指令碼。而所有其它的目錄則都是私有的。

## Options

## 選項

For options that apply to all pub commands, see
[Global options](/tools/pub/cmd#global-options).

你可以查閱 [全域選項](/tools/pub/cmd#global-options) 獲取 Pub 命令所支援的命令選項。

<aside class="alert alert-info" markdown="1">
  *Problems?* See [Troubleshooting Pub](/tools/pub/troubleshoot).

  **有疑問？** 請查閱 [Pub 疑難協助](/tools/pub/troubleshoot)。
</aside>
