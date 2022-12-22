---
title: dart doc
title: dart doc 命令
description: API reference generation tool.
description: Dart 程式語言的 API 參考文件產生工具。
toc: false
---

The `dart doc` command (previously called `dartdoc`)
creates API reference documentation
from Dart source code.
You can add descriptions to the generated documentation
by using [documentation comments][],
which can contain [Markdown][] formatting.
For guidance on writing doc comments,
see the [documentation part of Effective Dart][effective doc].

`dart doc` 命令可以從你 Dart 原始碼中產生 API 參考文件。
您可以使用 [文件註釋][documentation comments]
向產生的文件新增說明，該說明支援 [MarkDown][] 格式。
你可以查閱 [高效 Dart 的文件部分][effective doc]
獲取更多關於如何撰寫文件註釋的資訊。

{{site.alert.note}}

  To generate documentation, 
  you must first run [`dart pub get`](/tools/pub/cmd/pub-get)
  and your package must pass [`dart analyze`](/tools/dart-analyze)
  without errors.

  想要產生文件，你需要先執行
  [`dart pub get`](/tools/pub/cmd/pub-get)，
  並且 package 必須在 [`dart analyze`](/tools/dart-analyze)
  靜態分析中未出現錯誤才能正常產生。

{{site.alert.end}}

Run `dart doc` from the root directory of your package. 
For example:

從你 Package 的根目錄中執行 `dartdoc` 命令。例如：

```terminal
$ cd my_app
$ dart pub get
$ dart doc .
Documenting my_app...
...
Success! Docs generated into /Users/me/projects/my_app/doc/api
```

By default, 
the documentation files are static HTML files,
placed in the `doc/api` directory. 
You can create the files in a different directory
with the `--output` flag.

產生的文件預設為 HTML 檔案，儲存在 `doc/api` 目錄下。
你可以使用 `--output` 引數設定輸出目錄。

For information on command-line options, 
use the `help` command:

使用 `help` 命令可以檢視更多命令列選項：

```terminal
$ dart help doc
```

[documentation comments]: /guides/language/language-tour#documentation-comments
[effective doc]: /guides/language/effective-dart/documentation
[Markdown]: {{site.pub-pkg}}/markdown

{% comment %}
[PENDING: Add more help, info on commonly used options, links to examples of use.]
{% endcomment %}
