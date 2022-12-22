---
title: dart pub cache
description: Use dart pub cache to manage your system cache.
description: 使用 dart pub cache 命令管理你係統中的快取。
toc: false
---

_Cache_ is one of the commands of the [pub tool](/tools/pub/cmd).

_Cache_ 命令是 [Pub 工具](/tools/pub/cmd) 中的一個命令。

```nocode
$ dart pub cache add <package> [--version <constraint>] [--all]
$ dart pub cache repair
$ dart pub cache clean
```

The `dart pub cache` command works with the
[system cache](/tools/pub/glossary#system-cache).

## Adding a package to the system cache

## 快取一個 package 到本地

You can manually add a package to your system cache:

你可以手動快取一個 package 到本地：

```terminal
$ dart pub cache add <package>
```

## Reinstalling all packages in the system cache

## 重建所有 packages 的本地快取

You can perform a clean reinstallation of all packages in your system cache:

你可以將本地所有 package 的快取清空並重新下載：

```terminal
$ dart pub cache repair
```

This command can be useful when packages in your system cache
are somehow changed or broken.

當你的本地快取發生了意料之外的變化或損壞時，這個命令將非常有用。

For example, some editors make it easy to find implementation files
for packages in the system cache,
and you might accidentally edit one of those files.

例如，一部分編輯器可以很輕易地開啟本地快取中的檔案，
此時你有可能在不經意間對它們進行改動。

## Clearing the global system cache

## 清空全域的本地快取

You can empty the entire system cache
to reclaim extra disk space or remove problematic packages:

你可以使用以下命令清空快取，從而釋放空間或刪除有問題的 package 快取：

```terminal
$ dart pub cache clean
```

{{site.alert.version-note}}

  The `clean` subcommand was introduced in Dart 2.14.
  To clear your system cache with an older SDK,
  you can manually delete the [`PUB_CACHE`][] folder.

  `clean` 命令是在 Dart 2.14 引入的。
  若你需要在舊版本的 SDK 中清理快取，
  你可以手動刪除 [`PUB_CACHE`][] 資料夾。

{{site.alert.end}}

[`PUB_CACHE`]: /tools/pub/environment-variables

## Options

## 選項

For options that apply to all pub commands, see
[Global options](/tools/pub/cmd#global-options).

你可以查閱 [全域選項](/tools/pub/cmd#global-options) 獲取 Pub 命令所支援的命令選項。

### `--all`

Use `dart pub cache add --all` 
to install all matching versions of a library.

與 `dart pub add` 結合使用用於安裝某個庫的所有版本。

### `--version `_`<constraint>`_

Use with `dart pub cache add`
to install the version best matching the specified constraint. 
For example:

與 `dart pub add` 結合使用根據限制條件安裝最適合的版本。例如：

```terminal
$ dart pub cache add http --version "0.12.2"
```

If `--version` is omitted, pub installs the best of all known versions.

如果省略掉 `--version`，Pub 會從已知的版本中挑選一個最適合的進行安裝。

{{site.alert.info}}

  *Problems?*
  See [Troubleshooting Pub](/tools/pub/troubleshoot).

  **有疑問？** 請查閱 [Pub 疑難協助](/tools/pub/troubleshoot)。

{{site.alert.end}}