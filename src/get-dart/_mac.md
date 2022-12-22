[Install Homebrew,](https://brew.sh)
and then run the following commands:

[安裝 Homebrew](https://brew.sh)
後執行以下命令：

```terminal
$ brew tap dart-lang/dart
$ brew install dart
```

{{site.alert.important}}

  Make sure the **Homebrew `bin` directory is in your `PATH`**. 
  Setting up the path correctly makes it easier to use Dart SDK commands
  such as `dart run` and `dart format`. 
  For help configuring your path, 
  consult the [Homebrew FAQ.](https://docs.brew.sh/FAQ)

  請確認 **Homebrew 的 `bin` 目錄已包含在 `PATH` 中**。
  正確設定路徑有助於 `dart run` 和 `dart format`
  這樣的 Dart SDK 命令正常執行。
  若你需要設定 PATH 的幫助，請閱讀
  [Homebrew 常見問題](https://docs.brew.sh/FAQ)。

{{site.alert.end}}

To upgrade when a new release of Dart is available:

想要更新到 Dart SDK 可用的更新版本，執行以下命令：

```terminal
$ brew upgrade dart
```

To switch between locally installed Dart releases, 
first install the version you want to switch to if you haven't.
For example, to install Dart 2.12:

想要切換本地 Dart SDK 的版本，
在你沒有安裝之前，需要先行安裝對應版本。
例如，以下的命令將安裝 Dart 2.12：

```terminal
$ brew install dart@2.12
```

Then to switch between versions, 
unlink the current version and link the desired version.

接著切換版本時，取消當前版本的連結，並連結目標版本。

```terminal
$ brew unlink dart@<old> && brew unlink dart@<new> && brew link dart@<new>
```

To see which versions of Dart you've installed:

執行以下命令檢視已經安裝的 Dart 版本：

```terminal
$ brew info dart
```
