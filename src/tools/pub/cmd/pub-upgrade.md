---
title: dart pub upgrade
description: Use dart pub upgrade to get the latest versions of all dependencies used by your Dart app.
description: 使用 dart pub upgrade 命令來更新 Dart 應用的所有依賴項到最新版本。
---

_Upgrade_ is one of the commands of the [pub tool](/tools/pub/cmd).

_Upgrade_ 命令是 [Pub 工具](/tools/pub/cmd) 中的一個命令。

```nocode
$ dart pub upgrade [options] [dependencies]
```

Like [`dart pub get`](/tools/pub/cmd/pub-get),
`dart pub upgrade` gets dependencies.
The difference is that `dart pub upgrade` ignores any existing
[lockfile](/tools/pub/glossary#lockfile),
so that pub can get the latest versions of all dependencies.
A related command is [`dart pub outdated`](/tools/pub/cmd/pub-outdated),
which you can run to find out-of-date dependencies.

`dart pub upgrade` 命令與 [`dart pub get`](/tools/pub/cmd/pub-get)
命令一樣，都是用於獲取依賴項的。
不同的是 `dart pub upgrade` 命令會忽略掉任何已存在的
[lockfile](/tools/pub/glossary#lockfile) 檔案，
因此 Pub 可以獲取所有依賴項的最新版本。

Without any additional arguments, `dart pub upgrade` gets the latest
versions of all the dependencies listed in the
[`pubspec.yaml`](/tools/pub/pubspec) file in the current working
directory, as well as their [transitive
dependencies](/tools/pub/glossary#transitive-dependency).
For example:

在沒有指定其它引數的情況下，`dart pub upgrade` 命令會獲取當前工作目錄下
[`pubspec.yaml`](/tools/pub/pubspec) 檔案中所列出的所有依賴項的最新版本，
包括 [這些依賴項中內部依賴的其它依賴項](/tools/pub/glossary#transitive-dependency)。
例如：

```terminal
$ dart pub upgrade
Dependencies upgraded!
```

When `dart pub upgrade` upgrades dependency versions, it writes a lockfile to ensure that
[`dart pub get`](/tools/pub/cmd/pub-get) will use the same versions of those
dependencies. For application packages, check in the lockfile to
source control; this ensures the application has the exact same
versions of all dependencies for all developers and when deployed to
production. For library packages, don't check in the lockfile,
because libraries are expected to work with a range of dependency versions.

`dart pub upgrade` 命令會在更新依賴的版本時寫入一個 lockfile 檔案以確保後續使用
[`dart pub get`](/tools/pub/cmd/pub-get) 命令時使用的是相同的依賴版本。
對應用 Package 而言，簽入 lockfile 檔案以控制來源；
此操作可以確保當你將應用部署到生產環境時，
所有的開發者使用的依賴項都是完全相同的版本以避免衝突。
而對庫 Package 而言，則不要簽入 lockfile 檔案，
因為庫 Package 會使用到各種不同的依賴版本。

If a lockfile already exists, `dart pub upgrade` ignores it and generates a new
one from scratch, using the latest versions of all dependencies.

`dart pub upgrade` 命令會忽略掉已經存在的 lockfile 檔案並從 Scratch 中新建一個，
然後使用所有依賴項的最新版本。

See the [`dart pub get` documentation](/tools/pub/cmd/pub-get) for more information
on package resolution and the system package cache.

你可以查閱 [`dart pub get` 命令文件](/tools/pub/cmd/pub-get)
獲取更多關於 Package 解析以及系統 Package 快取的資訊。

## Upgrading specific dependencies

## 更新指定的依賴項

You can tell `dart pub upgrade` to upgrade specific dependencies to the
latest version while leaving the rest of the dependencies alone as much as
possible. For example:

你可以使用 `dart pub upgrade` 命令更新指定的依賴項到最新的版本同時儘可能地保持其餘依賴項不變。
例如：

```terminal
  $ dart pub upgrade test args
  Dependencies upgraded!
```

Upgrading a dependency upgrades its transitive dependencies to their latest
versions as well. Usually, no other dependencies are updated; they stay at the
versions that are locked in the lockfile. However, if the requested upgrades
cause incompatibilities with these locked versions, they are selectively
unlocked until a compatible set of versions is found.

更新一個依賴項也會將這個依賴項所依賴的其它依賴項更新到最新版本。
通常而言，與這個依賴項無關的其它依賴項不會被更新；
它們會保持處於 lockfile 檔案中的版本不變。
但是，如果該依賴的升級會導致這些鎖定的版本不相容的話，
則不相容的依賴會被解鎖，直到找到一個相容的版本後再重新鎖定。

## Getting a new dependency

## 獲取一個新的依賴項

If a dependency is added to the pubspec before `dart pub upgrade` is run,
it gets the new dependency and any of its transitive dependencies.
This shares the same behavior as `dart pub get`.

如果在執行 `dart pub upgrade` 命令前將一個依賴項新增至 pubspec 檔案，
則在執行該命令後會更新依賴項以及該依賴項所依賴的其它依賴項。
該行為與 `dart pub get` 命令一致。

## Removing a dependency

## 刪除一個依賴項

If a dependency is removed from the pubspec before `dart pub upgrade` is run,
the dependency is no longer available for importing.
Any transitive dependencies of the removed dependency are also removed,
as long as no remaining immediate dependencies also depend on them.
This is the same behavior as `dart pub get`.

如果在執行 `dart pub upgrade` 命令前將一個依賴項從 pubspec 檔案刪除，
則在執行該命令後會導致已經匯入使用的該依賴項相關程式碼不可用。
任何該依賴項所依賴的其它依賴項也會被同時刪除。
該行為與 `dart pub get` 命令一致。

## Upgrading while offline

## 離線更新

If you don't have network access, you can still run `dart pub upgrade`.
Because pub downloads packages to a central cache shared by all packages
on your system, it can often find previously downloaded packages
without needing to use the network.

即便沒有網路，你也可以執行 `dart pub upgrade` 命令。
因為 Pub 會將 Package 下載到系統的一個快取中心並與其它 Package 分享，
其它 Package 也可以從該快取中心獲取任意已經下載過的 Package，
如果你所需的 Package 是一個流行的 Package，
則你可以直接從該快取中依賴該 Package 而不需要使用網路。

However, by default, `dart pub upgrade` tries to go online if you
have any hosted dependencies,
so that pub can detect newer versions of dependencies.
If you don't want pub to do that, pass it the `--offline` flag.
In offline mode, pub looks only in your local package cache,
trying to find a set of versions that work with your package from what's already
available.

但是，預設情況下，`dart pub upgrade` 命令依然會盡可能地存取網路以獲取最新的依賴項版本。
如果你不希望其透過網路查詢依賴項，可以在該命令後加上 `--offline` 引數。
在離線模式下，Pub 只會從你的本地 Package 快取中查詢已經存在且能適用到你 Package 上的依賴項。

Keep in mind that pub generates a lockfile. If the
only version of some dependency in your cache happens to be old,
offline `dart pub upgrade` locks your app to that old version.
The next time you are online, you will likely want to
run `dart pub upgrade` again to upgrade to a later version.

記住 Pub 會產生一個 lockfile 檔案。
如果快取中某個依賴項只有一個版本且該版本是舊版本，
則在離線模式下 `dart pub upgrade` 命令會鎖定你的應用使用這些舊版本。
等你有網時，你可以再次執行 `dart pub upgrade` 命令將它們更新到最新版本。

## Options

## 選項

The `dart pub upgrade` command supports the
[`dart pub get` options](/tools/pub/cmd/pub-get#options), and more.
For options that apply to all pub commands, see
[Global options](/tools/pub/cmd#global-options).

`dart pub upgrade` 命令支援
[`dart pub get` 的命令選項](/tools/pub/cmd/pub-get#options) 以及更多的選項。
你可以查閱[全域選項](/tools/pub/cmd#global-options) 獲取 Pub 命令所支援的命令選項。

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

預設情況下，pub 會預編譯直接依賴的 package 的可執行檔案 (`precompile`)。
若你不需要預編譯，請使用 `--no-precompile`。

### `--null-safety`

Gets the packages that
[`dart pub outdated --mode=null-safety`][`dart pub outdated`]
lists as _resolvable_,
ignoring any upper-bound constraint in the `pubspec.yaml` file.
Also updates `pubspec.yaml` with the new constraints.
This command is similar to `--major-versions`.

獲取 [`dart pub outdated --mode=null-safety`][`dart pub outdated`]
列表中標記為 **resolvable** 的依賴，
忽略 `pubspec.yaml` 檔案中的任何上限。
同時 `pubspec.yaml` 檔案中的限制也會更新。
該命令與 `--major-versions` 類似。

{{site.alert.tip}}

  Commit the `pubspec.yaml` file before running this command,
  so that you can undo the changes if necessary.

  在運星這個命令之前先提交 `pubspec.yaml` 檔案，
  這樣就可以在需要的時候撤銷這些改動。

{{site.alert.end}}

### `--major-versions`

Gets the packages that [`dart pub outdated`][] lists as _resolvable_,
ignoring any upper-bound constraint in the `pubspec.yaml` file.
Also updates `pubspec.yaml` with the new constraints.

獲取 [`dart pub outdated`][] 列表中標記為 **resolvable** 的依賴，
忽略 `pubspec.yaml` 檔案中的任何上限。
同時 `pubspec.yaml` 檔案中的限制也會更新。

[`dart pub outdated`]: /tools/pub/cmd/pub-outdated

{{site.alert.tip}}

  Commit the `pubspec.yaml` file before running this command,
  so that you can undo the changes if necessary.

  在執行該命令前提交 `pubspec.yaml` 檔案的修改，這樣如果有需要時可以進行回滾。

{{site.alert.end}}

To check which dependencies will be upgraded,
you can use `dart pub upgrade --major-versions --dry-run`.

想檢視哪些依賴會被更新，可以執行
`dart pub upgrade --major-versions --dry-run`。

{{site.alert.info}}

  *Problems?*
  See [Troubleshooting Pub](/tools/pub/troubleshoot).

  **有疑問？** 
  請查閱 [Pub 疑難協助](/tools/pub/troubleshoot)。

{{site.alert.end}}
