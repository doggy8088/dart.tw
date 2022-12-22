---
title: dart pub get
title: dart pub get 命令
description: Use dart pub get to retrieve the dependencies used by your Dart application.
description: 使用 dart pub get 命令檢索 Dart 應用使用的依賴項。
---

_Get_ is one of the commands of the [pub tool](/tools/pub/cmd).

_Get_ 命令是 [Pub 工具](/tools/pub/cmd) 中的一個命令。

```
$ dart pub get [options]
```

This command gets all the dependencies listed in the
[`pubspec.yaml`](/tools/pub/pubspec) file in the current working
directory, as well as their
[transitive dependencies](/tools/pub/glossary#transitive-dependency).
For example:

該命令獲取所有在當前工作目錄下的 [`pubspec.yaml`](/tools/pub/pubspec)
檔案中列出的依賴項，以及這些依賴項的 [間接依賴項](/tools/pub/glossary#transitive-dependency)。
例如：

```terminal
$ dart pub get
Resolving dependencies...
Got dependencies!
```

If the [system cache](/tools/pub/glossary#system-cache)
doesn't already contain the dependencies, `dart pub get`
updates the cache,
downloading dependencies if necessary.
To map packages back to the system cache,
this command creates a `package_config.json` file 
in the `.dart_tool/` directory.

如果 [Pub 的系統快取](/tools/pub/glossary#system-cache) 中還沒有該依賴項，
則 `dart pub get` 命令會在必要時下載該依賴項並更新快取。
該命令會在 `.dart_tool/` 資料夾下建立一個
`package_config.json` 檔案並將之對映到 Pub 的系統快取中。

Once the dependencies are acquired, they may be referenced in Dart code.
For example, if a package depends on `test`:

一旦依賴獲取完畢，就可以在 Dart 程式碼中參考。
例如，假如 package 依賴了名為 `test` 的 package，則可以使用該 package 下的資源：

```dart
import 'package:test/test.dart';
```

When `dart pub get` gets new dependencies, it writes a
[lockfile](/tools/pub/glossary#lockfile) to ensure that future
gets will use the same versions of those dependencies.
Application packages should check in the lockfile to source control;
this ensures the application will use the exact same versions
of all dependencies for all developers and when deployed to production.
Library packages should not check in the lockfile, though, since they're
expected to work with a range of dependency versions.

`dart pub get` 命令獲取新依賴項後會寫入一個 [lockfile](/tools/pub/glossary#lockfile) 檔案
以確保下次執行該命令時會使用相同的依賴項版本。
應用型的 package 應該總是簽入該 lockfile 檔案以控制來源；
從而確保在將 package 部署到生產環境時所有的依賴項對於所有開發者而言都是相同的版本。
庫型別的 package 則不需要簽入 lockfile 檔案，因為它們可能需要使用到不同的依賴項版本。

If a lockfile already exists, `dart pub get` uses the versions of dependencies
locked in it if possible. If a dependency isn't locked, pub gets the
latest version of that dependency that satisfies all the [version
constraints](/tools/pub/glossary#version-constraint).
This is the primary difference between `dart pub get` and
[`dart pub upgrade`](/tools/pub/cmd/pub-upgrade), which always tries to
get the latest versions of all dependencies.

如果 lockfile 已經存在，`dart pub get` 命令會盡可能地使用鎖定的依賴項版本。
如果某個依賴項沒有被鎖定，則 pub 會獲取所有
[限定的版本](/tools/pub/glossary#version-constraint) 中最新的那個依賴項版本。
這是 `dart pub get` 命令與 [`dart pub upgrade`](/tools/pub/cmd/pub-upgrade) 命令最大的不同點，
後者總是會去嘗試使用依賴項的最新版本。

## Package resolution

## Package 解析

By default, pub creates a `package_config.json` file
in the `.dart_tool/` directory that maps from package names to location URIs.

預設情況下，pub 會在 `.dart_tool/` 資料夾下建立一個
`.packages` 檔案用於對映 package 名到位置 URI。

{{site.alert.note}}

  Don't check the generated `.dart_tool/` directory into your repo;
  add it to your repo's `.gitignore` file.
  For more information, 
  see [What not to commit](/guides/libraries/private-files).

  不要將儲存庫中由 pub 自動產生的 `.dart_tool` 目錄新增至版本管理中；
  請將它新增至 Git 儲存庫的 `.gitignore` 檔案中。
  更多資訊請查閱 [你不應該提交哪些檔案](/guides/libraries/private-files)。

{{site.alert.end}}


## Getting a new dependency

## 獲取一個新的依賴項

If a dependency is added to the pubspec and then `dart pub get` is run,
it gets the new dependency and any of its transitive dependencies.
However, pub won't change the versions of any already-acquired
dependencies unless that's necessary to get the new dependency.

如果在執行 `dart pub get` 命令前將某個依賴新增至 pubspec 檔案中，
則在執行該命令後會更新依賴項以及其間接依賴的其它依賴項。
但是，pub 不會更改哪些已經存在的依賴項除非有必要獲取它們的新版本。

## Removing a dependency

## 移除一個依賴項

If a dependency is removed from the pubspec and then `dart pub get` is run,
the dependency is no longer available for importing.
Any transitive dependencies of the removed dependency are also removed,
as long as no remaining immediate dependencies also depend on them.
Removing a dependency never changes the versions of any
already-acquired dependencies.

如果在 `dart pub get` 命令前從 pubspec 檔案移除了某個依賴項，
則在執行該命令後代碼使用到該依賴項的相關匯入將變得不可用。
所有該依賴項依賴的間接依賴項也同時會被移除，
只要這些間接依賴項沒有沒其它的依賴項所依賴。
移除某個依賴項不會對已經獲得的依賴項版本產生任何影響。

## The system package cache

## 系統 Package 快取

Dependencies downloaded over the internet, such as those from Git and the
[pub.dev site]({{site.pub}}), are stored in a
[system-wide cache](/tools/pub/glossary#system-cache).
This means that if multiple packages use the same version of the
same dependency, it only needs to be
downloaded and stored locally once.

依賴項透過網路從類似 Git 儲存庫和 [Pub 網站]({{site.pub}}) 下載並存儲在一個
[系統級的快取](/tools/pub/glossary#system-cache) 中。
這意味著如果多個 Package 使用了相同依賴項的相同版本，
它就不再需要透過網路下載，而僅僅只需從本地快取獲取即可。

By default, the system package cache is located in the `.pub-cache`
subdirectory of your home directory (on macOS and Linux),
or in `%LOCALAPPDATA%\Pub\Cache` (on Windows;
the location might vary depending on the Windows version).
You can configure the location of the cache by setting the
[`PUB_CACHE`](/tools/pub/environment-variables)
environment variable before running pub.

預設情況下，Pub 快取儲存在你的使用者目錄（macOS 和 Linux）或
`%APPDATA%\Pub\Cache` 目錄下的 `.pub-cache` 子目錄中
（Windows，不同版本的 Windows 作業系統可能會不一樣）。
你可以在執行 Pub 相關命令前透過 [`PUB_CACHE`](/tools/pub/environment-variables) 
系統環境變數配置你想要的快取儲存目錄。

## Getting while offline

## 離線檢索

If you don't have network access, you can still run `dart pub get`.
Because pub downloads packages to a central cache shared by all packages
on your system, it can often find previously downloaded packages
without needing to use the network.

在沒有網路的情況下你也依然可以執行 `dart pub get` 命令。
因為 pub 會將 Package 下載到一個統一的快取區並將其與系統上其它的 package 進行共享，
如果你所需的 package 是一個使用頻率很高的 package，
那麼很有可能它已經被其它 package 在使用時下載到統一快取區中了，此時你可以直接依賴使用它。

However, by default, `dart pub get` tries to go online if you
have any hosted dependencies,
so that pub can detect newer versions of dependencies.
If you don't want pub to do that, pass it the `--offline` flag.
In offline mode, pub looks only in your local package cache,
trying to find a set of versions that work with your package from what's already
available.

但是，預設情況下，`dart pub get` 命令會總是嘗試獲取線上的依賴版本，
因此 pub 可以確定依賴項是否有更新的版本。
如果你不想 pub 去線上檢查，可以使用 `--offline` 命令引數讓該命令在離線模式下執行。
在離線模式下，pub 只會從本地快取區查詢已經下載到的可用 Package。

Keep in mind that pub generates a lockfile. If the
only version of some dependency in your cache happens to be old,
offline `dart pub get` locks your app to that old version.
The next time you are online, you will likely want to
run [`dart pub upgrade`](/tools/pub/cmd/pub-upgrade) to upgrade to a later version.

切記 pub 會產生一個 lockfile 檔案。如果快取中某些依賴專案有且只有一個版本且非常舊，
離線模式下執行 `dart pub get` 命令則依然會使用那些舊的版本。
下次當你有可用的網路時，可以使用
[`dart pub upgrade`](/tools/pub/cmd/pub-upgrade) 命令將其更新到最新版本。

## Options

## 選項

For options that apply to all pub commands, see
[Global options](/tools/pub/cmd#global-options).

你可以查閱 [全域選項](/tools/pub/cmd#global-options) 獲取 pub 命令所支援的命令選項。

### `--[no-]offline`

{% include tools/pub-option-no-offline.md %}

### `--dry-run` or `-n`

### `--dry-run` 或 `-n`

Reports the dependencies that would be changed,
but doesn't make the changes. This is useful if you
want to analyze updates before making them.

打印出可能變化的依賴，但不會實際作出更改。
如果你想要在變更前進行分析，該命令非常有用。

### `--[no-]precompile`

By default, pub precompiles executables
in immediate dependencies (`--precompile`).
To prevent precompilation, use `--no-precompile`.

預設情況下，pub 將預編譯直接依賴的 package 中的可執行檔案 (`--precompile`)。
若你不需要預編譯，請使用 `--no-precompile`。

### `--legacy-packages-file`

If you're using Dart 2.18 and use any third-party tools
that rely on the discontinued `.packages` file,
use `--legacy-packages-file` to generate a `.packages` file.
Support for this flag will be removed in Dart 2.19.

如果你正在使用 Dart 2.18 並且使用了一些依賴於已經停止支援了的
`.packages` 檔案，請使用 `--legacy-packages-file` 來產生一個
`.packages` 檔案，這個命令列標記將會在 Dart 2.19 中被移除。

{{site.alert.info}}

  *Problems?*
  See [Troubleshooting Pub](/tools/pub/troubleshoot).

  **有疑問？** 請查閱 [Pub 疑難協助](/tools/pub/troubleshoot)。

{{site.alert.end}}
