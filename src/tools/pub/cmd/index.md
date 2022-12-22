---
title: dart pub
title: dart pub 工具
description: The command-line interface for pub, a package management tool for Dart.
description: Pub 的命令列工具，是 Dart 的 package 管理工具。
---

The [pub package manager](/guides/packages) has a command-line interface
that works with either the
[`flutter` tool][flutter-cli] or the [`dart` tool][dart-cli].
With either tool, add the `pub` command followed by
a subcommand such as `get`:

```terminal
$ dart pub get    # Gets dependencies for a non-Flutter package
$ flutter pub get # Gets dependencies for a Flutter package
```

This site uses `dart pub <subcommand>` for its examples,
but if your current directory holds a Flutter app
or other Flutter-specific code,
use `flutter pub <subcommand>` instead.
For more information, see
[Using packages]({{site.flutter-docs}}/development/packages-and-plugins/using-packages)
on the [Flutter website]({{site.flutter}}).

[flutter-cli]: {{site.flutter-docs}}/reference/flutter-cli
[dart-cli]: /tools/dart-tool

{{site.alert.version-note}}
  The `dart pub` command debuted in Dart 2.10.
  Although you might still find examples of
  using the standalone `pub` command instead of
  `dart pub` or `flutter pub`,
  the standalone `pub` command is deprecated.
{{site.alert.end}}

If you encounter problems using the pub tool,
see [Troubleshooting Pub](/tools/pub/troubleshoot).


## List of subcommands

Detailed documentation exists for each of the following pub subcommands:

{% include pub-subcommands.md %}

## Overview of subcommands

Pub's subcommands fall into the following categories:

Pub 的命令可以分為以下幾類：

* [Managing package dependencies](#managing-apps)

  [管理 Package 的依賴關係](#managing-apps)

* [Running command-line apps](#running-command-line-apps)

  [執行命令列應用](#running-command-line-apps)

* [Deploying packages and apps](#deploying-packages-and-apps)

  [部署 Package 和應用](#deploying-packages-and-apps)


<a id="managing-apps"></a>
### Managing package dependencies

### 管理 Package 的依賴關係

Pub provides a number of subcommands for managing the
[packages your code depends on](/tools/pub/dependencies).

Pub 提供了許多命令，這些命令用於管理那些
[你程式碼所依賴 Package](/tools/pub/dependencies)。

In this group, the most commonly used subcommands are `get` and
`upgrade`, which retrieve or upgrade dependencies used by a package.
Every time you modify a pubspec file,
run `dart pub get` or `flutter pub get`
to make sure the dependencies are up to date. Some IDEs
perform this step automatically on the creation of a project,
or any modification of the pubspec.

在這些命令中，最常用的是 `get` 命令和 `upgrade` 命令，
前者用於檢索 Package 的依賴項，後者用於更新 Package 的依賴項。
每一次你修改了 pubspec 檔案後都需要執行 `dart pub get` 或
`flutter pub get` 命令來確保你所用的依賴項是最新的。
一些 IDE 會在建立專案或修改了 pubspec 檔案後自動執行此操作。

[`cache`](/tools/pub/cmd/pub-cache)
<br> Manages pub's local package cache. Use this subcommand to add packages
  to your cache, or to perform a clean reinstall of all packages in
  your cache.

[`cache`](/tools/pub/cmd/pub-cache) 命令
<br>用於管理 Pub 的本地 Package 快取。使用該命令你可以將一個 Package 新增至快取，
或者清除所有快取的 Package 並重新安裝。

[`deps`](/tools/pub/cmd/pub-deps)
<br> Lists all dependencies used by the current package.

[`deps`](/tools/pub/cmd/pub-deps) 命令
<br>用於顯示當前 Package 使用的所有依賴項。

[`downgrade`](/tools/pub/cmd/pub-downgrade)
<br> Retrieves the lowest versions of all the packages that are
  listed as dependencies used by the current package. Used for testing
  the lower range of your package's dependencies.

[`downgrade`](/tools/pub/cmd/pub-downgrade) 命令
<br>用於檢索當前 Package 所依賴的其它 Package 的最低版本。
用於測試這些較低版本依賴項的 Package 在當前 Package 上的相容性。

[`get`](/tools/pub/cmd/pub-get)
<br> Retrieves the packages that are listed as the dependencies for
  the current package.
  If a `pubspec.lock` file already exists, fetches the version
  of each dependency (if possible) as listed in the lock file.
  Creates or updates the lock file, as needed.
  
[`get`](/tools/pub/cmd/pub-get) 命令
<br>用於檢索當前 Package 所依賴的其它 Package。
如果 `pubspec.lock` 檔案已經存在，
則根據該檔案中儲存的依賴項版本獲取對應的依賴項。
如有必要，將會建立或更新該檔案。

[`outdated`](/tools/pub/cmd/pub-outdated)
<br> Looks at every package that the current package depends on,
  determines which package dependencies are out of date,
  and gives you advice on how to update them.
  Use this subcommand when you want to update package dependencies.

[`outdated`](/tools/pub/cmd/pub-outdated) 命令
<br> 檢視當前軟體包所依賴的每個 package，確定哪些 package 的依賴項已過時，
併為您提供有關如何更新它們的建議。當您要更新 package 的依賴性時，請使用此命令。

[`upgrade`](/tools/pub/cmd/pub-upgrade)
<br> Retrieves the latest version of each package listed
  as dependencies used by the current package. If a `pubspec.lock`
  file exists, ignores the versions listed in the lock file and fetches
  the newest versions that honor the constraints in the pubspec.
  Creates or updates the lock file, as needed.

[`upgrade`](/tools/pub/cmd/pub-upgrade) 命令
<br>用於檢索當前 Package 所依賴的其它 Package 的最新版本。
如果 `pubspec.lock` 檔案已經存在，
則忽略其儲存的版本並以 pubspec 檔案中指定的最新版本為主。
如有必要，將會建立或更新該檔案。

### Running command-line apps

### 執行命令列應用

The [`global`](/tools/pub/cmd/pub-global) subcommand lets you 
make a package globally available, 
so you can run scripts from that package’s `bin` directory.
To run globally available scripts, you must
[add the system cache `bin` directory to your path][add-path].

[add-path]: /tools/pub/cmd/pub-global#running-a-script-from-your-path

### Deploying packages and apps

## 部署 Package 和應用

With pub you can publish packages and command-line apps.

使用 pub 命令你還可以釋出 Package 和命令列應用。

#### Packages

### Package

To share your Dart packages with the world, you can
use the [`publish`](/tools/pub/cmd/pub-lish) subcommand to upload the
package to the [pub.dev site]({{site.pub}}).
For information on allowing other users 
to modify and upload new versions of your package,
see [Uploaders](/tools/pub/publishing#uploaders).


你可以使用 [`publish`](/tools/pub/cmd/pub-lish) 命令將
Package 上傳至 [Pub 網站]({{site.pub}})以分享給全世界的開發者使用。
[`uploader`](/tools/pub/cmd/pub-uploader) 命令則可以允許指定使用者
修改 Package 和上傳新版本的 Package。

#### Command-line apps

#### 命令列應用

For any package that contains scripts (anything under the `bin/`
directory), consider adding the `executables` tag to the pubspec file.
When a script is listed under `executables`, users can run
[`dart pub global activate`](/tools/pub/cmd/pub-global#activating-a-package)
to make it directly available from the command line.

任何包含指令碼（即在 `bin/` 目錄下有任意檔案）的 Package，
可以在 pubspec 檔案中新增上 `executables` 標籤。
當一個指令碼標識為 `executables` 時，
使用者可以直接從命令列使用
[`pub global activate`](/tools/pub/cmd/pub-global#activating-a-package) 
命令執行它。

## Global options

## 全域選項

Several command-line options work with all of the pub subcommands.
These include:

有幾個命令列選線可以用於所有 pub 命令。它們包括：

### `--help` or `-h`

### `--help` 或 `-h`

Prints usage information.

顯示使用說明。

### `--trace`

Prints debugging information when an error occurs.

當出現錯誤時輸出除錯資訊。

### `--verbose` or `-v`

### `-verbose` 或 `-v`

Equivalent to `--verbosity=all`.

等同於 `--verbosity=all`。

### `--directory=<dir>` or `-C <dir>`

### `--directory=<dir>` 或 `-C <dir>`

Runs the command in the specified directory.

在指定的目錄下執行命令。

### `--[no-]color`

Adds color to the output for emphasis (`--color`).
The default depends on whether you're using this command at a terminal.
At a terminal, `--color` is the default,
otherwise, `--no-color` is the default.
Use `--no-color` to disable color in all environments.

在輸出內容中加入顏色來增強顯示效果 (使用 `--color`)。
預設值在不同程式設計環境中是不同的，取決於是否在終端中使用，
如果在終端中使用 `--color` 是預設的，
其他環境預設是 `--no-color`。
使用 `--no-color` 可以在所有環境中禁用色彩顯示。
