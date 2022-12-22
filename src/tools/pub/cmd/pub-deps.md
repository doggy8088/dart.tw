---
title: dart pub deps
title: dart pub deps
description: Use dart pub deps to print a dependency graph for a package.
description: 使用 dart pub deps 命令可以將 Package 的依賴項打印出來。
---

_Deps_ is one of the commands of the [pub tool](/tools/pub/cmd).

_Deps_ 命令是 [Pub 工具](/tools/pub/cmd) 中的一個命令。

```nocode
$ dart pub deps [--style=<style>] [--[no-]dev] [--executables]
```

This command prints the dependency graph for a package.
The graph includes both the
[immediate dependencies](/tools/pub/glossary#immediate-dependency)
that the package uses (as specified in the pubspec), as well as the
[transitive dependencies](/tools/pub/glossary#transitive-dependency)
pulled in by the immediate dependencies.

該命令可以將 Package 的依賴圖示列印輸出到控制檯。
該圖示中包括 Package 宣告在 pubspec 檔案中的 
[直接依賴](/tools/pub/glossary#immediate-dependency) 
以及這些直接依賴所依賴的
[間接依賴](/tools/pub/glossary#transitive-dependency)。

The dependency information is printed as a tree by default.

依賴資訊預設以樹狀的形式列印輸出。

For example, the pubspec for the markdown_converter example specifies
the following dependencies:

例如，markdown_converter 這個範例的 pubspec 檔案中聲明瞭如下依賴資訊：

```yaml
dependencies:
  barback: ^0.15.2
  markdown: ^0.7.2
```

Here's an example of the `dart pub deps` output for markdown_converter:

當你執行 `dart pub deps` 命令時則會看到 markdown_converter 的依賴圖示如下：

```terminal
$ dart pub deps
markdown_converter 0.0.0
|-- barback 0.15.2+6
|   |-- collection 1.1.2
|   |-- path 1.3.6
|   |-- pool 1.1.0
|   |   '-- stack_trace...
|   |-- source_span 1.2.0
|   |   '-- path...
|   '-- stack_trace 1.4.2
|       '-- path...
'-- markdown 0.7.2
```

## Options

## 選項

For options that apply to all pub commands, see
[Global options](/tools/pub/cmd#global-options).

你可以查閱 [全域選項](/tools/pub/cmd#global-options) 獲取 Pub 命令所支援的命令選項。

### `--style=<style>` or `-s <style>`

### `--style=<style>` 或 `-s <style>`

The specified style determines the output format:

指定的樣式輸出格式。

* `tree`
<br> Prints dependency information as a tree. This is the 
default format.

  `tree`<br>
以樹狀的形式列印依賴資訊。這是預設格式。

* `list`
<br>  Prints dependency information as a list.

  `list`
  <br>以列表的形式列印依賴資訊。

* `compact`
<br> Prints dependency information as a compact list.

  `compact`
<br> 以緊湊列表的形式列印依賴資訊。

### `--[no-]dev`

By default, prints all dependencies, 
including dev dependencies (`--dev`).
To remove dev dependencies, use `--no-dev`.

列印所有套件相依資訊，包括開發依賴。
如果你不想列印開發依賴，使用 `--no-dev`。

### `--executables`

Prints all available executables.

列印所有可用的可執行檔案。

### `--json`

Generates output in JSON format.

以 JSON 格式輸出。

{{site.alert.info}}

  *Problems?*
  See [Troubleshooting Pub](/tools/pub/troubleshoot).

  **有疑問？**請查閱 [Pub 疑難協助](/tools/pub/troubleshoot)。

{{site.alert.end}}
