---
title: dart pub global
title: dart pub global 命令
description: Use dart pub global to run Dart scripts hosted on the pub.dev site from the command line.
description: 使用 dart pub global 命令從命令列執行 Pub.dev 網站上託管的 Dart 指令碼。
---

_Global_ is one of the commands of the [pub tool](/tools/pub/cmd).

_Global_ 命令是 [Pub 工具](/tools/pub/cmd) 中的一個命令。

Pub's `global` option allows you to run Dart scripts from the
command line when you are not currently inside a package.
After [activating a package](#activating-a-package), you can
[run scripts](#running-a-script) from that package's `bin` directory.
[Deactivating a package](#deactivating-a-package) removes it from
your list of globally available packages.

Pub 的 `global` 選項允許你在任意位置下從命令列執行 Dart 指令碼。
在 [啟用 Package](#activating-a-package) 後，
你可以 [執行](#running-a-script) 該 Package `bin` 目錄下的指令碼。
[停用 Package](#deactivating-a-package) 後你可以從全域可用的 Package 列表中將其移除。

For example, say you want to use [webdev][] to serve
your Dart web application from the command line.

例如，假設你想要透過命令使用 [webdev][] 來部署您的 Dart Web 應用。

```terminal
$ dart pub global activate webdev
$ webdev serve
```

If this doesn't work, you might need to
[set up your path](#running-a-script-from-your-path).

如果該操作無效，你可能需要 [設定你的路徑](#running-a-script-from-your-path)。

To run a Dart script from within a package, or from a
package that your package depends on, see [dart run](/tools/dart-run).

如果想要從 Package 中執行 Dart 指令碼，或者從 Package 所依賴的其它 Package 中執行指令碼，
請查閱 [dart run](/tools/dart-run) 命令。

## Activating a package

## Package 啟用

```nocode
dart pub global activate [--noexecutables] [--executable=<name>] [--overwrite] <package> [version-constraint]
```

Activate a package when you want to be able to run
one or more of its executable files from the command line.
You can activate packages that live on the
[pub.dev site]({{site.pub}}), a Git repository,
or your local machine.
Once you've activated a package, see [Running a
script](#running-a-script) to run scripts from the package's
`bin` directory.

當你想從命令列執行某個 Package 中的可執行物件時你需要先啟用它。該 Package 可以是在 [pub.dev 網站]({{site.pub}})、Git 儲存庫或者你當前裝置上。一旦你激活了 Package，就可以參閱 [Running a script](#running-a-script) 執行位於 Package `bin` 目錄下的指令碼。

When you activate a package you can specify an optional version
constraint.  See the [constraint](#options) flag for usage examples.

你可以在啟用 Package 時指定一個可選的版本限制引數。關於其使用範例請查閱 [版本限制引數](#options)。

### Activating a package on the pub.dev site

### 啟用 pub.dev 網站上的 Package

```terminal
$ dart pub global activate <pub.dev package>
```

Specify a package on the pub.dev site to activate it. For example:

啟用 pub.dev 網站上的一個 Package。例如：

```terminal
$ dart pub global activate markdown
```

### Activating a package with Git

### 啟用 Git 儲存庫中的 Package

```terminal
$ dart pub global activate --source git <Git URL>
$ dart pub global activate -sgit <Git URL>
```

Use `--source git` (or `-sgit`, for short) to activate
a package in a Git repository. The following examples,
which activate the `async_await` package on
[GitHub](https://github.com/), are equivalent:

使用 `--source git`（或 `-sgit` 簡寫）命令引數可以啟用位於 Git 儲存庫中的 Package。下面的兩個範例都可以用於啟用位於 [GitHub](https://github.com/) 網站上名為 `async_await` 的 Package。

```terminal
$ dart pub global activate --source git https://github.com/dart-lang/async_await.git
$ dart pub global activate -sgit https://github.com/dart-lang/async_await.git
```

Pub expects to find the package in the root of the Git repository.
To specify a different location, 
use the `--git-path` option with
a path relative to the repository root:

Pub 會嘗試在 Git 儲存庫的根目錄尋找 package。
你可以使用 `--git-path` 選項為 Pub 指定用於查詢的相對於儲存庫的路徑：

```terminal
$ dart pub global activate -sgit https://github.com/dart-lang/http.git --git-path pkgs/http/
```

### Activating a package on your local machine

### 啟用當前裝置上的 Package

```terminal
$ dart pub global activate --source path <path>
```

Use `activate --source path <path>` to activate a package on your local machine.
The following example activates the `stopwatch` package from the
`~/dart` directory:

使用 `activate --source path <path>` 命令引數啟用當前裝置上的 Package。下面的範例激活了位於 `~/dart` 目錄下名為 `stopwatch` 的 Package。

```terminal
$ dart pub global activate --source path ~/dart/stopwatch
```

### Updating an activated package

### 更新已經啟用的 Package

Once a package has been activated, you can upgrade it by activating the
package again.

你可以再次啟用一個已經啟用的 Package 以更新它。

## Running a script

## 執行指令碼

You can directly run a script from an activated package from the
command line. If you are unable to run the script directly,
you can also use `dart pub global run`.

你可以從命令列直接執行已啟用 Package 中的 指令碼。如果你不能直接執行指令碼，可以嘗試使用 `dart pub global run` 命令。

### Running a script from your PATH

### 執行指定路徑中的指令碼

To run a script directly from the command line, add the [system cache][] `bin`
directory to your `PATH` environment variable.

在從命令列直接執行指令碼前，你還需要將 [系統快取][system cache] 的 `bin` 檔案目錄新增至 PATH 路徑中。
For example, say you've activated the webdev package,
but you still can't run the command:

例如，假設你已經激活了名為 webdev 的套件，但是你依然不能執行它：

```terminal
$ dart pub global activate webdev
$ webdev serve
-bash: webdev: command not found
```

Verify that the `bin` directory for the system cache is in your path.
The following `PATH` variable, on macOS, includes the system cache:

此時你需要確認 Pub 系統快取的 `bin` 目錄是否已經新增至 PATH 路徑中。
例如在 macOS 上，使用下述命令可以檢視你的 `PATH` 路徑是否已經包含了
Pub 系統快取的 `bin` 目錄：

```terminal
$ echo $PATH
/Users/<user>/homebrew/bin:/usr/local/bin:/usr/bin:/bin:[!/Users/<user>/.pub-cache/bin!]
```

If this directory is missing from your `PATH`,
locate the file for your platform and add it.

如果 `PATH` 路徑中沒有新增 Pub 快取目錄，你需要根據你當前裝置的系統平台找到對應的 Pub 快取目錄並將之新增。

|-------------------+---------------------------|
|      Platform     |      Cache location       |

|        平台       |          快取位置           |

|-------------------|---------------------------|
| macOS or Linux | `$HOME/.pub-cache/bin`        |

| macOS 或 Linux | `$HOME/.pub-cache/bin`        |

| Windows<sup><strong>*</strong></sup> | `%LOCALAPPDATA%\Pub\Cache\bin` |
{:.table .table-striped}

<sup><strong>*</strong></sup> The exact location of the system cache
may vary for different versions of Windows.

<sup><strong>*</strong></sup> 對於不同版本的 Windows 系統，Pub 系統快取的確切位置可能有所不同。

You can now directly invoke the command:

現在你可以直接呼叫命令了：

{% prettify nocode tag=pre+code %}
$ cd web_project
$ [!webdev serve!]
{% endprettify %}

If the script still fails to run from the command line, the
package may not be [configured](#configuring-package-executables) for
this feature. You can still run the script using `dart pub global run`.

如果此時從命令列執行指令碼依然失敗，則 Package 可能沒有針對此功能進行 [配置](#configuring-package-executables)。但你仍然可以使用 `dart pub global run` 命令執行它。

### Running a script using `dart pub global run`

### 使用 `dart pub global run` 命令執行指令碼

```nocode
$ dart pub global run <package>:<executable> [args...]
```

Even if a script is not configured to be run from the command line,
you can still use `dart pub global run`.
The following command runs the `bin/bar.dart` script from the
`foo` package, passing in two arguments.

即使某個指令碼沒有配置成可以從命令列執行，你依然可以使用 `pub global run` 命令執行它。下述命令傳遞兩個引數從名為 `foo` 的 Package 執行 `bin/bar.dart` 指令碼。

```terminal
$ dart pub global run foo:bar arg1 arg2
```

### Configuring package executables

### 配置 Package 為可執行的

If you are not a package developer, you can skip this section.

如果你不是一個 Package 開發者，你可以跳過本節。

A package can expose some of its scripts as executables
that can be run directly from the command line. The script or scripts
must be listed in the
[`executables`](/tools/pub/pubspec#executables)
entry of the pubspec file.  For example, the following pubspec file
identifies `bin/helloworld.dart` as an executable for the helloworld
package:

Package 可以暴露它的一些指令碼以直接從命令列執行。
這些指令碼必須在 pubspec 檔案的 [`executables`](/tools/pub/pubspec#executables)
標籤下列出。例如，下述名為 helloworld 的 Package 在其 pubspec 檔案中將
`bin/helloworld.dart` 暴露為可以執行：

```yaml
name: helloworld

executables:
  helloworld:
```

Failing to list a script under the `executables` tag reduces the script's
usability: unlisted scripts can be executed using `dart pub global run`, but not
directly from the command line.

沒有在 `executables` 標籤下列出的指令碼可能會降低指令碼的易用性：這些指令碼雖然可以使用 `dart pub global run` 命令執行，但是不能直接從命令列執行。

## Deactivating a package

## 停用 Package

```terminal
$ dart pub global deactivate <package>
```

Use `deactivate` to remove a package from the list of available
global packages. For example:

使用 `deactivate` 命令引數可以將 Package 從全域可用的 Package 列表移除。例如：

```terminal
$ dart pub global deactivate markdown
```

You can no longer invoke the package's scripts using `dart pub global run`,
or at the command line.

此時你不能使用 `dart pub global run` 命令或從命令列執行該 Package 的指令碼。

## Listing active packages

## 列出啟用的 Package

```terminal
$ dart pub global list
```

Use `list` to list all currently active packages.

使用 `list` 列出當前所有已啟用的 Package。

## Options

## 選項

For options that apply to all pub commands, see
[Global options](/tools/pub/cmd#global-options).

你可以查閱 [全域選項](/tools/pub/cmd#global-options) 獲取 Pub 命令所支援的命令選項。

### `[version-constraint]`

Use `dart pub global activate <package> [version-constraint]`
to specify a specific version of the package.
For example, the following command pulls
the 0.6.0 version of the `markdown` package:

使用 `dart pub global activate` 拉取 Package 的指定版本。
例如，下述命令會拉取 `markdown` 這個 Package 的 0.6.0 版本：

```terminal
$ dart pub global activate markdown 0.6.0
```

If you specify a range, pub picks the best version that meets that
constraint. For example:

如果你指定的是一個範圍，Pub 則會在這個範圍內選取一個最適合的版本。例如：

```terminal
$ dart pub global activate foo <3.0.0
```

### `--no-executables`

Use `dart pub global activate <package> --no-executables`
to globally activate the specified package,
but not put any executables in `bin`.
You have to use `dart pub global run` to run any executables.

會在全域範圍內啟用 Package 但不會在 `bin` 目錄下產生任何檔案。
你必須使用 `dart pub global run` 來執行任意這些可執行的物件。

### `--executable=<name>` or `-x <name>`

### `--executable=<name>` 或 `-x <name>`

Use with `dart pub global activate`
to add the specified executable to your PATH.
You can pass more than one of these flags.

將指定的可執行物件新增至你的 PATH 路徑中。
你可以在一次命令執行中多次使用該選項以新增多個可執行物件到你的 PATH 路徑中。

For example, the following command adds `bar` and `baz`,
(but not any other executables that `foo` might define)
to your PATH.

例如，下述命令添加了 foo 中的 `bar` 和 `baz` 兩個可執行物件
（不包括 foo 中定義的其它可執行物件）到你的 PATH 中。

```terminal
$ dart pub global activate foo -x bar -x baz
```

### `--overwrite`

Use `dart pub global activate <package> --overwrite`
to overwrite any previously activated global executables
with the same name. If you don't specify this flag,
the preexisting executable will not be replaced.

預設情況下，如果執行的兩個全域 Package 名字衝突了，那麼會優先執行最先執行過的那個。
但是如果你指定該標識，那麼新執行的 Package 則會覆蓋之前執行的那些。

{{site.alert.info}}

  *Problems?*
  See [Troubleshooting Pub](/tools/pub/troubleshoot).

  **有疑問？** 請查閱 [Pub 疑難協助](/tools/pub/troubleshoot)。

{{site.alert.end}}

[system cache]: /tools/pub/glossary#system-cache
[webdev]: /tools/webdev
