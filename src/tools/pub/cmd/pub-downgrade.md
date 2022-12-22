---
title: dart pub downgrade
title: dart pub downgrade 命令
description: Use dart pub downgrade to get the lowest versions of all dependencies used by your Dart application.
description: 使用 dart pub downgrade 命令可以獲取你 Dart 應用所使用的所有依賴項的最低版本。
---

_Downgrade_ is one of the commands of the [pub tool](/tools/pub/cmd).

_Downgrade_ 命令是 [Pub 工具](/tools/pub/cmd) 中的一個命令。

```nocode
$ dart pub downgrade [--[no-]offline] [-n|--dry-run] [dependencies...] 
```

Without any additional arguments, `dart pub downgrade` gets the lowest versions of
all the dependencies listed in the [`pubspec.yaml`](/tools/pub/pubspec) file
in the current working directory, as well as their [transitive
dependencies](/tools/pub/glossary#transitive-dependency).
For example:

在沒有其它額外引數的情況下，`dart pub downgrade` 命令會獲取當前工作目錄下 [`pubspec.yaml`](/tools/pub/pubspec) 檔案中列出的所有依賴項以及它們 [間接依賴項](/tools/pub/glossary#transitive-dependency) 的最低版本。例如：

```terminal
$ dart pub downgrade
Resolving dependencies... (1.2s)
+ barback 0.13.0
+ collection 0.9.1
+ path 1.2.0
+ source_maps 0.9.0
+ source_span 1.0.0
+ stack_trace 0.9.1
Changed 6 dependencies!
```

The `dart pub downgrade` command creates a lockfile. If one already exists,
pub ignores that file and generates a new one from scratch, using the lowest
versions of all dependencies.

`dart pub downgrade` 命令會建立一個 lockfile 檔案。如果 lockfile 檔案已經存在，Pub 則會忽略該檔案並透過 Scratch 產生一個新的 lockfile 檔案，然後所有依賴項都會使用最低版本。

See the [`dart pub get` documentation](/tools/pub/cmd/pub-get) for more information
on package resolution and the system package cache.

請查閱 [`dart pub get` 命令文件](/tools/pub/cmd/pub-get) 獲取更多關於 Package 解析和系統 Package 快取的資訊。

## Downgrading specific dependencies

## 降級指定依賴項

It's possible to tell `dart pub downgrade` to downgrade specific dependencies to the
lowest version while leaving the rest of the dependencies alone as much as
possible. For example:

你可以指定 `pub downgrade` 命令只將某個依賴項的版本降至最低且不影響其餘依賴項。例如：

```terminal
$ dart pub downgrade test
Resolving dependencies...
  barback 0.15.2+2
  bot 0.27.0+2
  browser 0.10.0+2
  chrome 0.6.5
  collection 1.1.0
  path 1.3.0
  pool 1.0.1
  source_span 1.0.2
< stack_trace 0.9.2 (was 1.1.1)
  stagexl 0.10.2
< test 0.10.0 (was 0.11.4)
These packages are no longer being depended on:
- matcher 0.11.3
Changed 3 dependencies!
```

If you are downgrading a specific dependency, pub tries to find the
highest versions of any transitive dependencies that fit the new dependency
constraints. Any transitive dependencies are usually also downgraded
as a result.

如果你降低指定依賴項的版本，且該依賴項還有間接依賴項，那麼在版本變更後這些間接依賴項可能不適配降低後的新版依賴項。此時，Pub 會嘗試在新版本依賴項可接受的範圍內查詢版本最高的該依賴項所依賴的間接依賴項。因此，通常而言，降低某個依賴項的版本後，其間接依賴項的版本也會隨之降低。

## Getting a new dependency

## 獲取新的依賴項

If a dependency is added to the pubspec before `dart pub downgrade` is run,
it gets the new dependency and any of its transitive dependencies.
This shares the same behavior as `dart pub get`.

如果在執行 `dart pub downgrade` 命令前將某個依賴新增至 pubspec 檔案中，則在執行該命令後會將該新的依賴項以及其間接依賴的其它依賴項下載並將其放到 `.packages` 檔案中。這點與 `dart pub get` 命令一致。

## Removing a dependency

## 移除依賴項

If a dependency is removed from the pubspec before `dart pub downgrade` is run,
the dependency is no longer available for importing.
Any transitive dependencies of the removed dependency are also removed,
as long as no remaining immediate dependencies also depend on them.
This is the same behavior as `dart pub get`.

如果在 `dart pub downgrade` 命令前從 pubspec 檔案移除了某個依賴項，
則在執行該命令後代碼使用到該依賴項的相關匯入將變得不可用。
所有該依賴項依賴的間接依賴項也同時會被移除，
只要這些間接依賴項沒有沒其它的依賴項所依賴。
這點與 `dart pub get` 命令一致。

## Downgrading while offline

## 離線降級

If you don't have network access, you can still run `dart pub downgrade`.
Because pub downloads packages to a central cache shared by all packages
on your system, it can often find previously downloaded packages
without needing to use the network.

在沒有網路的情況下你也依然可以執行 `dart pub downgrade` 命令。因為 Pub 會將 Package 下載到一個統一的快取區並將其與系統上其它的 Package 進行共享，如果你所需的 Package 是一個使用頻率很高的 Package，那麼很有可能它已經被其它 Package 在使用時下載到統一快取區中了，此時你可以直接依賴使用它。

However, by default, `dart pub downgrade` tries to go online if you
have any hosted dependencies.
If you don't want pub to do that, pass it the `--offline` flag.
In offline mode, pub looks only in your local package cache,
trying to find a set of versions that work with your package from what's already
available.

但是，預設情況下，`dart pub downgrade` 命令會總是嘗試獲取線上的依賴版本，因此 Pub 可以確定依賴項是否有更新的版本。如果你不想 Pub 去線上檢查，可以使用 `--offline` 命令引數讓該命令在離線模式下執行。在離線模式下，Pub 只會從本地快取區查詢已經下載到的可用 Package。


## Options

## 選項 {#options}

## Options

`dart pub upgrade` 命令支援 [`pub get` 的命令選項](/tools/pub/cmd/pub-get#options)。

For options that apply to all pub commands, see
[Global options](/tools/pub/cmd#global-options).

你可以查閱 [全域選項](/tools/pub/cmd#global-options) 獲取 Pub 命令所支援的命令選項。

### `--[no-]offline`

{% include tools/pub-option-no-offline.md %}

### `--dry-run` or `-n`

Reports what dependencies would change but doesn't change any.

報告將要改變的依賴項，但不會真的改變它。

{{site.alert.info}}

  *Problems?*
  See [Troubleshooting Pub](/tools/pub/troubleshoot).

  **有疑問？** 請查閱 [Pub 疑難協助](/tools/pub/troubleshoot)。

{{site.alert.end}}
