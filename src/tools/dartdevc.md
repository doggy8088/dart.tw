---
title: "dartdevc: The Dart development compiler"
title: "dartdevc：Dart 開發編譯器"
short-title: dartdevc
description: A development compiler for fast, modular compilation of Dart code to JavaScript.
description: 一個用於將 Dart 轉換為 JavaScript 的快速且模組化的開發編譯器。
---

The Dart development compiler (_dartdevc_)
compiles Dart as JavaScript AMD modules. These modules
work for web app development in modern browsers.

{{site.alert.note}}
  Dart 2.18 removes the `dartdevc` command-line tool from the Dart
  package, but retains the dartdevc compiler.
  
  Use [`webdev serve`](/tools/webdev#serve) to compile Dart code
  to modular JavaScript. This makes compilation faster and debugging easier.
{{site.alert.end}}

