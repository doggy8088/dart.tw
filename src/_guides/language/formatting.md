---
title: Formatting code
title: 程式碼格式化
description: Use `dart format` to format your code, and follow Effective Dart guidelines for what `dart format` doesn't cover.
description: 使用 `dart format` 來格式化你的程式碼，並且透過 Dart 高效指南 (Effective Dart) 進一步調整 `dart format` 沒有包括的內容。
toc: false
---

As [Effective Dart][] says, when it comes to things like formatting,
arguments about which is better are subjective and impossible to resolve.
What we do know is that being consistent is objectively helpful.
If two pieces of code look different it should be because
they _are_ different in some meaningful way.
When a bit of code stands out and catches your eye, it should do so for a useful reason.

正如 [高效 Dart 語言指南][Effective Dart] 所說，
在面對程式碼格式化的問題時，哪個引數更好是一種主觀感受，
無法完全解決。

Fortunately, you can use the [`dart format` tool][dart format]—from the 
command line or in your favorite [Dart-savvy IDE][ide]—to 
perform most of the drudge work of formatting your code.
For example, here's how to format all the Dart files
under the current directory's `bin`, `lib`, and `test` directories:

幸運的是，您可以在命令列或可以使用 Dart 的 IDE 中使用
[`dart format` 工具][dart format] 來進行繁雜的大部分格式化工作。
舉個例子，下面是對當前目錄下的 `bin`、`lib` 和 `test` 資料夾下的
所有 Dart 檔案進行格式化的方法：

```terminal
$ dart format bin lib test
```

However, dart format can't do it all.
To avoid making changes that might be unsafe, `dart format` affects only whitespace.
For additional guidance, see the Effective Dart
[style guidelines][], especially the [formatting guidelines][]. 

然而，dart format 並不是面面俱到。
為了避免造成一些破壞性改動，
`dart format` 僅僅會對空格（包含換行和縮排）進行操作。
若需獲得更多的指導，請檢視高效 Dart 語言指南的 [風格指南][style guidelines]，
特別是其中的 [格式化指南][formatting guidelines] 部分。

More information:

更多資訊：

* [`dart format`][dart format]
* [Effective Dart: Style][style guidelines]

  [高效 Dart 語言指南：程式碼風格][style guidelines]

[dart format]: /tools/dart-format
[Effective Dart]: /guides/language/effective-dart
[formatting guidelines]: /guides/language/effective-dart/style#formatting
[ide]: /tools/#ides-and-editors
[style guidelines]: /guides/language/effective-dart/style
