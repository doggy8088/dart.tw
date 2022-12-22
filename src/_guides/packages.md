---
title: How to use packages
title: 如何使用 package
short-title: Packages
description: Learn more about pub, Dart's tool for managing packages.
description: 關於 pub 命令的更多介紹，這是 Dart 裡用於管理 package 的工具。
---

The Dart ecosystem uses _packages_ to manage shared software
such as libraries and tools.
To get Dart packages, you use the **pub package manager**.
You can find publicly available packages on the
[**pub.dev site**,]({{site.pub}})
or you can load packages from the local file system or elsewhere,
such as Git repositories.
Wherever your packages come from, pub manages version dependencies,
helping you get package versions that work with each other and
with your SDK version.

Dart 生態系統使用 _包_ 來管理**共享軟體**，比如：函式庫和工具。
我們使用 **Pub 包管理工具** 來獲取 Dart 套件。
在 [**Pub**]({{site.pub}}) 上，可以找到公開可用的套件。
或者從本地檔案系統或其他的位置，比如 Git 儲存庫，載入可用的套件。
無論包是從什麼途徑載入的， Pub 都會進行版本依賴管理，
從而幫助我們獲得版本相容的軟體包以及 SDK 。

Most [Dart-savvy IDEs][] offer support for using pub that
includes creating, downloading, updating, and publishing packages.
Or you can use [`dart pub` on the command line](/tools/pub/cmd).

大多數 [Dart-savvy IDEs][] 都支援 Pub 的使用，包括套件的建立，下載，更新和釋出。
同樣上述功能也可以在命令列上透過 [`dart pub`](/tools/pub/cmd) 來操作。
或者可以在命令列上使用pub。

At a minimum,
a Dart package is a directory containing a [pubspec file](/tools/pub/pubspec).
The pubspec contains some metadata about the package. 
Additionally, a package can contain dependencies (listed in the pubspec),
Dart libraries, apps, resources, tests, images, and examples.

Dart 包目錄中至少包含一個 [pubspec 檔案](/tools/pub/pubspec)。
pubspec 檔案記錄一些關於套件的元資料。
此外，套件還包含其他依賴項（在 pubspec 中列出），
Dart 函式庫，應用，資源，測試，圖片，以及範例。

To use a package, do the following:

透過以下步驟，參考使用包：

* Create a pubspec (a file named `pubspec.yaml` that
  lists package dependencies and includes
  other metadata, such as a version number).

  建立一個 pubspec （一個名為 `pubspec.yaml` 檔案，
  檔案列出依賴的套件以及包含的其他元資料，比如當前套件的版本）。

* Use [`dart pub get`][get] to retrieve your package's dependencies.

  使用 [`dart pub get`][get] 獲取當前所依賴的套件。

* If your Dart code depends on a library in the package, import the library.

  如果當前 Dart 程式碼相依套件中的某個庫，匯入 (import) 該函式庫。

## Creating a pubspec

## 建立 pubspec

The pubspec is a file named `pubspec.yaml`
that's in the top directory of your application.
The simplest possible pubspec lists only the package name:

pubspec 是一個名為 `pubspec.yaml` 的檔案，
檔案位於應用的根路徑。
最簡單的 pubspec 只需要列出套件名稱：

```yaml
name: my_app
```

Here is an example of a pubspec that declares dependencies on
two packages (`js` and `intl`) that are hosted on the pub.dev site:

下面是一個 pubspec 的範例，範例中宣告依賴了在
Pub 站點上託管的兩個 package（`js` 和 `intl`）：

```yaml
name: my_app

dependencies:
  js: ^0.6.0
  intl: ^0.17.0
```

For details on creating a pubspec,
see the [pubspec documentation](/tools/pub/pubspec)
and the documentation for the packages that you want to use.

有關建立 pubspec 的詳細內容，請參閱 [pubspec 文件](/tools/pub/pubspec)
以及使用套件的相關文件。

## Getting packages

## 獲取套件

Once you have a pubspec, you can run [`dart pub get`][get] from the top 
directory of your application:

專案中一旦擁有了 pubspec 檔案，就可以在專案根目錄中執行
[`dart pub get`][get] 命令：

```terminal
$ cd <path-to-my_app>
$ dart pub get
```

This process is called _getting the dependencies_.

上面的操作即 **獲取依賴**。

The `dart pub get` command
determines which packages your app depends on,
and puts them in a central [system cache](/tools/pub/glossary#system-cache).
If your app depends on a published package, pub downloads that package from the
[pub.dev site.]({{site.pub}})
For a [Git dependency](/tools/pub/dependencies#git-packages),
pub clones the Git repository.
Transitive dependencies are included, too.
For example, if the `js` package depends on the `test` package, `pub`
grabs both the `js` package and the `test` package.

`dart pub get` 命令確定當前應用所依賴的套件，
並將它們儲存到中央 [系統快取](/tools/pub/glossary#system-cache)
(central system cache) 中。
如果當前應用依賴了一個公開套件， Pub 會從 [Pub 站點]({{site.pub}}) 獲取該套件。
對於一個 [Git 依賴](/tools/pub/dependencies#git-packages)，
Pub 會 Clone 該 Git 儲存庫。同樣包括套件的相關依賴也會被下載。
例如，如果 `js` 套件相依 `test` 套件，`pub` 會同時獲取 `js` 套件和 `test` 套件。

Pub creates a
`package_config.json` file (under the `.dart_tool/` directory)
that maps each package name that your app depends on
to the corresponding package in the system cache.

Pub 會建立一個 `package_config.json` 檔案（位於 `.dart_tool/` 目錄下），
該檔案將應用程式所依賴的每個套件名稱相應的對映到系統快取中的套件。

## Importing libraries from packages

## 從套件中匯入庫

To import libraries found in packages, 
use the `package:` prefix:

使用 `package:` 字首，匯入套件中的函式庫：

```dart
import 'package:js/js.dart' as js;
import 'package:intl/intl.dart';
```

The Dart runtime takes everything after `package:`
and looks it up within the `package_config.json` file for
your app.

Dart 執行時會抓取 `package:` 之後的內容，
並在應用程式的 `package_config.json` 檔案中查詢它。

You can also use this style to import libraries from within your own package.
Consider the following pubspec file, which declares a dependency on
the (fictional) `transmogrify` package:

也可以使用此方式從自己的套件中匯入庫。
思考下面的 pubspec 檔案，該檔案聲明瞭對 `transmogrify` 套件（虛構的套件名稱）的依賴：

{% prettify yaml tag=pre+code %}
name: my_app
dependencies:
  transmogrify:
{% endprettify %}

Let's say that the `transmogrify` package is laid out as follows:

假如 `transmogrify` 這個 package 的佈局如下：

```nocode
transmogrify/
  lib/
    transmogrify.dart
    parser.dart
  test/
    parser/
      parser_test.dart
```

The `parser_test.dart` file can import `parser.dart` like this:

`parser_test.dart` 就可以透過下面的方式匯入 `parser.dart`：

```dart
import 'package:transmogrify/parser.dart';
```


## Upgrading a dependency

## 升級依賴

The first time you get a new dependency for your package,
pub downloads the latest version of it that's compatible with
your other dependencies.
It then locks your package to *always* use that version by
creating a **lockfile**.
This is a file named `pubspec.lock` that pub creates
and stores next to your pubspec. 
It lists the specific versions of each dependency (immediate and transitive) 
that your package uses.

第一次獲取依賴時，Pub 會下載依賴及其相容的最新版本。
然後透過建立 **lockfile** 鎖定依賴，以始終使用這個版本。
Pub 會在 pubspec 旁建立並存儲一個名為 `pubspec.lock` 檔案。
它列出了使用的每個相依套件的指定版本（當前包或傳遞套件的版本）。

If your package is an application package,
you should check this file into
[source control](/guides/libraries/private-files).
That way, everyone working on your app uses the same versions
of all of its dependencies.
Checking in the lockfile also ensures that your deployed app
uses the same versions of code.

如果套件是一個應用程式套件，那麼應該將此檔案加入到 [原始檔管理](/guides/libraries/private-files)。
這樣，在應用上開發的每個人都能夠使用所有相同版本的套件。
同樣加入到 lockfile 可以保證部署的應用使用的是同一版本的程式碼。

When you're ready to upgrade your dependencies to the latest versions,
use the [`dart pub upgrade`][upgrade] command:

如果已經準備更新依賴到最新版本，使用 [`dart pub upgrade`][upgrade] 命令：

```terminal
$ dart pub upgrade
```

The `dart pub upgrade` command tells pub to regenerate the lockfile,
using the newest available versions of your package's dependencies.
If you want to upgrade only one dependency,
you can specify the package to upgrade:

`dart pub upgrade` 命令用於重新產生 lockfile 檔案，並使用最新可用版本的相依套件。
如果僅升級某個依賴，可以在命令中指定需要升級的包：

```terminal
$ dart pub upgrade transmogrify
```

That command upgrades `transmogrify` to the latest version
but leaves everything else the same.

上面的命令升級 `transmogrify` 到最新版本，但維持其它套件不變。

The `dart pub upgrade` command can't always upgrade every package
to its latest version,
due to conflicting version constraints in the pubspec.
To identify out-of-date packages that require editing the pubspec,
use [`dart pub outdated`][outdated].

`dart pub upgrade`] 命令並非總是可以將所有的 package 更新到最新版本，
原因是 pubspec 檔案中的一些 package 之間有版本限制的衝突。
想要確定 pubspec 裡已經過時且需要編輯的 package，
請使用 [`dart pub outdated`][outdated] 命令。

## More information

## 更多內容

The following pages have more information about packages and
the pub package manager.

以下連結的頁面是關於套件及 Pub 包管理的更多內容。


### How to

### 如何使用

* [Creating packages](/guides/libraries/create-library-packages)

  [建立包](/guides/libraries/create-library-packages)

* [Publishing packages](/tools/pub/publishing)

  [釋出包](/tools/pub/publishing)

### Reference

### 參考

* [Pub dependencies](/tools/pub/dependencies)
   
  [Pub 依賴](/tools/pub/dependencies)

* [Pub environment variables](/tools/pub/environment-variables)

  [Pub 環境變數](/tools/pub/environment-variables)

* [Pub glossary](/tools/pub/glossary)

  [Pub 術語](/tools/pub/glossary)

* [Pub package layout conventions](/tools/pub/package-layout)

  [Pub 套件的佈局約定](/tools/pub/package-layout)

* [Pub versioning philosophy](/tools/pub/versioning)
  
  [Pub 版本哲學](/tools/pub/versioning)

* [Pubspec format](/tools/pub/pubspec)

  [Pubspec 格式](/tools/pub/pubspec)

### Pub subcommands

### Pub 子命令

The `dart pub` tool provides the following subcommands:

{% include pub-subcommands.md %}

For an overview of all the `dart pub` subcommands,
see the [pub tool documentation](/tools/pub/cmd).

有關所有 `pub` 命令的概述，
參見 [pub 工具文件](/tools/pub/cmd)。

### Troubleshooting

### 故障排除

[Troubleshooting pub](/tools/pub/troubleshoot) gives solutions to problems that
you might encounter when using pub.

[Pub 故障排除](/tools/pub/troubleshoot) 提供使用中可能遇到問題的解決方法。

[Dart-savvy IDEs]: /tools#ides-and-editors
[get]: /tools/pub/cmd/pub-get
[upgrade]: /tools/pub/cmd/pub-upgrade
[outdated]: /tools/pub/cmd/pub-outdated
