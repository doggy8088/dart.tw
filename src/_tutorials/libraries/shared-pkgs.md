---
title: Install shared packages
title: 使用共享的 package
description: Packages are bundles of source code, tools, and resources that help you to organize and share code
description: Package 是用於在 Dart 語言裡打包程式碼、工具和資源的東西，可以幫助你更好的組織和分享程式碼
---

### Borrow and share code.

### 借用和分享程式碼。

<div class="mini-toc" markdown="1">
  <h4>What's the point?</h4>

  <h4>本文的重點是什麼？</h4>

  * The [pub.dev site]({{site.pub}}) is the primary public repository for Dart
    packages.

    [Pub.dev 網站]({{site.pub}})是 Dart package 主要的公共儲存庫。

  * Following a few conventions, such as having a valid pubspec.yaml file,
    makes your app a package.

    遵循一些規定，比如一個有效的 pubspec.yaml 檔案，
    使你的應用成為一個 package。

  * If you're developing a web or server-side app,
    use `dart create` to generate starting files.

    如果你正在開發一款 Web 或伺服器端應用，
    請使用 `dart create` 產生相關初始化檔案。

  * If you're developing a web or server-side app,
    use `dart pub get` to download packages.

    如果你正在開發一款 Web 或伺服器端應用，
    請使用 `dart pub get` 命令下載相關的 package。

  * If you're developing a mobile app, use Flutter's tools.

    如果你正在開發一款移動應用，請使用 Flutter 工具。
</div>

Once you can create and run a Dart app,
you're ready to leverage code written by other programmers.
Many interesting and useful packages of reusable Dart code
are available at the [pub.dev site]({{site.pub}}) repository.

當你可以建立和執行 Dart 應用時，你就已經準備複用其他程式設計師編寫的程式碼了。
[pub.dev 網站]({{site.pub}}) 儲存庫有許多可用的使用 Dart 程式碼編寫的 package。

This tutorial shows how to use `dart pub`—a package manager
that comes with Dart—to
install one of the packages in the repository,
the vector_math package.
You can follow these same steps to install any package hosted at
the [pub.dev site]({{site.pub}});
just change the package name when you get to that step.
This tutorial also describes some of the resources you can expect to find
in a well-built package.

本課程將向你展示如何使用 `dart pub` 命令
&mdash;Dart 的 package 管理命令&mdash;即用以安裝儲存庫中某個 package
（比如 vector_math package）的命令。
你可以使用這些步驟來安裝由 [pub.dev 網站]({{site.pub}}) 提供的任意一個 package；
當你想安裝其它你想要使用的 package 時只需修改這些步驟中的範例的 package 名稱即可。
本課程同時也會向你展示一些你可能會使用到的優秀的 package。

{{site.alert.flutter-note}}

  This page doesn't describe the tools you use with Flutter, but the
  concepts are the same, and you can share packages between
  your Flutter and web or server-side apps.
  For more information, see the
  [Flutter package documentation.]({{site.flutter-docs}}/development/packages-and-plugins/using-packages)

  本文不會向你展示那些只針對 Flutter 的 package，但它們的概念是一致的，
  你也可以將你的 package 分享為可以在 Flutter、Web 或伺服器端應用上使用。
  更多相關資訊，請查閱 [Flutter 的 package 文件。]({{site.flutter-docs}}/development/packages-and-plugins/using-packages)

{{site.alert.end}}

## About the pubspec.yaml file

## 關於 pubspec.yaml 檔案

To use an external package,
your app must itself be a package.
Any app with a valid pubspec.yaml file in its top-level directory
is a package and can therefore use external packages.

想要使用外部的 package，你的應用其本身也必須是一個 package。
任何在最上層目錄中包含有效 pubspec.yaml 檔案的應用都是一個 package，
從而可以使該應用使用外部的 package。

You can use the [`dart create`](/tools/dart-create) command to generate packages
with valid pubspec.yaml files and directory structures.
This command works either at the command line or (behind the scenes) in an IDE
such as IntelliJ or WebStorm.

你可以使用 [`dart create`](/tools/dart-create) 命令來產生帶有
有效 pubspec.yaml 檔案和目錄結構的 package。
你可以使用命令列工具來呼叫 Stagehand 工具，
也可以使用類似 IntelliJ 或 WebStorm 這樣的 IDE 來間接使用 Stagehand 工具。

Run the `dart create` command with the `--help` flag
to see what kinds of template files it can generate:

現在你可以執行 `dart create` 命令，使用
`--help` 來檢視它可以產生的範本檔案：

```terminal
$ dart create --help
```

You'll see a list of templates, including various web and server-side apps.
One of the templates is named **console**.

你將會看到一系列的產生器，包括各種 Web 和 伺服器端應用的。
其中一個產生器叫 **console-full**。


Use the `dart create` command to
generate a command-line app named `vector_victor`:

使用 `dart create` 工具來產生一個叫做 `vector_victor` 的命令列應用：

```terminal
$ dart create -t console vector_victor 
$ cd vector_victor
```

The pubspec.yaml file contains the package specification written in YAML.
(Visit <a href="/tools/pub/pubspec">Pubspec Format</a>
for in-depth coverage.)
The contents of your pubspec.yaml file should look something like this:

pubspec.yaml 檔案包含了由 YAML 語言撰寫的 package 規格。
（存取 <a href="/tools/pub/pubspec">Pubspec 格式 </a> 獲取更多深入的介紹。）
而你的 pubspec.yaml 檔案看起來則應該是這樣的

<?code-excerpt "vector_victor/pubspec.yaml" to="test"?>
```yaml
name: vector_victor
description: A sample command-line application.
version: 1.0.0
# homepage: https://www.example.com

environment:
  sdk: '>=2.18.0 <3.0.0'

# dependencies:
#   path: ^1.8.0

dev_dependencies:
  lints: ^2.0.0
  test: ^1.16.0
```

## Name the package dependencies

## 依賴 package 的命名

To use an external library package,
you need to add the package to your
app's list of dependencies
in the pubspec.yaml file.
Each item in the dependencies list
specifies the name and version
of a package that your app uses.

為了能夠使用外部的 package，你需要將其新增到你應用 pubspec.yaml 檔案的依賴裡。
依賴中的每一項都指定了你應用所使用的 package 名稱以及版本。

Let's make the vector_victor app have a dependency
on the `vector_math` package,
which is available at the [pub.dev site]({{site.pub}}).

下面讓我們為 vector_victor 應用新增一個名為 vector_math 的 package，
這個 package 可以在 [pub.dev 網站]({{site.pub}}) 中找到。

Run the [`dart pub add`](/tools/pub/cmd/pub-add) command 
and specify `vector_math`
to add a dependency on the package:

執行 [`dart pub add`](/tools/pub/cmd/pub-add) 命令並指定
使用 `vector_math`，將其加入你的依賴中。

```terminal
$ dart pub add vector_math
Resolving dependencies... 
+ vector_math 2.1.3
Downloading vector_math 2.1.3...
Changed 1 dependency!
```

This will add `vector_math` to the
`dependencies` entry of your `pubspec.yaml`,
resulting in the following:

這個命令將會將 `vector_math` 加入你工程檔案中
`pubspec.yaml` 的 `dependencies` 部分:

<?code-excerpt "vector_victor/pubspec.yaml" from="/^dependencies/"?>
```yaml
dependencies:
  vector_math: ^2.1.3
```

You can also find your desired version on the
[`vector_math` page on pub.dev]({{site.pub-pkg}}/vector_math)
and add it manually to the dependency section.

For details of what version numbers mean
and how you can format them,
see [Pub versioning philosophy](/tools/pub/versioning).

你可以查閱 [Pub 版本管理](/tools/pub/versioning)
獲取更多有關版本號含義以及格式化的相關資訊。

The [pub.dev site]({{site.pub}})
is the primary public repository for Dart packages.
`dart pub` automatically checks that
website when resolving package dependencies.
To use one of the packages from that site,
you can specify it by its simple name,
as we have done here.

[pub.dev 網站]({{site.pub}}) 是 Dart package 主要的公共儲存庫。
`dart pub` 命令在解析 package 依賴時會自動去該網站進行檢查。
如果你想使用該網站的某個 package，
你可以像我們上面所說的那樣在 dependencies 中指定對應的 package 名稱。

## Install the package dependencies

## 安裝依賴的 package

If you're using a Dart-savvy editor or `dart pub` to edit `pubspec.yaml`,
it might automatically install the packages your app depends on.

如果你使用適配了 Dart 語言開發的編輯器或者
`dart pub` 命令了編輯 `pubspec.yaml` 檔案，
其可能會在你編輯了該檔案後自動下載安裝相關依賴的 package。

If not, do it yourself by running
[dart pub get](/tools/pub/cmd/pub-get):

否則，你只能手動地執行
[dart pub get](/tools/pub/cmd/pub-get) 命令進行下載安裝：

```terminal
$ dart pub get
Resolving dependencies...
+ vector_math 2.1.3
Changed 1 dependency!
```

The `dart pub get` command installs the
packages in your app's dependencies list.
Each package can contain libraries and other assets.
Pub works recursively;
if an included package has dependencies, those packages are installed as well.
Pub caches the files for each package your app depends on,
pointing to them from the `.dart_tool/package_config.json` file.

`dart pub get` 命令會安裝你應用依賴列表中的 package。
而每一個 package 可能還會包含其它的函式庫或資源，Pub 同樣會將它們依次安裝；
如果一個依賴的 package 已經安裝過，則會直接使用。
Pub 會快取你應用依賴過的每一個 package 並將其快取至
`.dart_tool/package_config.json` 的檔案中。

Pub creates a file called `pubspec.lock`
that identifies the specific versions of the packages that were installed.
This helps to provide a stable development environment.
Later you can modify the version constraints and use `dart pub upgrade`
to update to new versions as needed.

Pub 會建立一個名為 `pubspec.lock` 的檔案來標識哪些 package 的哪些版本已經安裝過。
此舉可以為開發者提供一個穩定的開發環境。
你也可以修改 package 的版本並使用 `dart pub upgrade` 命令來更新 package。

## What did you get (and not get)?

## 你可以從中獲取（或不可獲取）什麼？

Besides the Dart libraries,
the `vector_math` package has other resources that might be useful to you
that do not get installed into your app directory.
Let's take a step back for a moment to look at what
you got and where it came from.

除了 Dart 庫以外，`vector_math` package
可能包含其它對你有用但不會安裝到你應用目錄的資源。
讓我們後退一步看看你在獲取依賴時得到了什麼以及它們從何而來。

To see the contents of the `vector_math` package,
visit the
<a href="https://github.com/google/vector_math"
target="_blank" rel="noopener">Dart vector math repository</a>
on GitHub.
Although many files and directories are in the repository,
only one, `lib`, was installed when you ran pub get.

存取 Github 儲存庫
<a href="https://github.com/google/vector_math" target="_blank" rel="noopener">Dart 數學向量儲存庫</a>
來檢視 `vector_math` package 的具體內容。
儘管該儲存庫中有大量的檔案和目錄，
但是隻有 `lib` 目錄下的檔案會在你執行 pub get 命令時安裝。

<div>
  <hr>
  <div class="row">
    <div class="col-lg-3">
    <img class="scale-img-max" src="/tutorials/images/libraries-folder.png"
         alt="Dart libraries directory"/>
    </div>
    <div class="col-lg-7">
      <em>Dart libraries:</em>
      <em>Dart 庫：</em>
      The lib directory contains one or more Dart libraries,
      which can be imported into your Dart programs.

      lib 目錄包含一個或多個可以安裝到你 Dart 程式的 Dart 庫。
    </div>
  </div>
  <hr>
  <div class="row">
    <div class="col-lg-3">
    <img class="scale-img-max" src="/tutorials/images/housekeeping-files.png"
         alt="Housekeeping files"/>
    </div>
    <div class="col-lg-7">
      <em>Housekeeping files:</em>
      <em>管理檔案：</em>
      When using a package written by someone else,
      the <code>README.md</code> file is a good place to start.
      It should contain important information about the package,
      such as its intent, contents, samples, and instructions.
      The <code>LICENSE</code> file provides
      copyright and rules-of-use information.
      All of these files are in the package repository;
      the contents of some,
      such as <code>README.md</code>,
      are also displayed in pub.dev.
      These files aren't installed when you install a package.

      當使用別人開發的 package 時，README 檔案是瞭解這個 package 的最好地方。
      它會包含與這個 package 相關的重要資訊，
      比如開發 package 時的想法、其相關內容、範例以及使用說明。
      LICENSE 檔案則包含了版權資訊以及使用規則資訊。
      所有檔案均在 package 的程式碼儲存庫中，且在你安裝 package 時它們不會被下載。
      而 <code>README.md</code> 中的內容同樣也會展示在 pub.dev 網站上。
    </div>
  </div>
  <hr>
  <div class="row">
    <div class="col-lg-3">
    <img class="scale-img-max" src="/tutorials/images/other-folders.png"
         alt="Document, scripts, tests, and other resources"/>
    </div>
    <div class="col-lg-7">
      <em>Other resources:</em>
      <em>其它資源：</em>
      Along with Dart libraries,
      a package might also contain other resources
      such as example code, tests, scripts, and documentation.
      If a package contains these resources,
      they should be in the directories as specified in the pub.

      一個 package 可能會包含庫以外的其它資源，
      比如範例程式碼、測試、指令碼以及文件。
      如果 package 中包含諸如此類別的資訊，
      它們會存放在 Pub 指定的目錄中。
<a href="/tools/pub/package-layout">conventions</a>.
    </div>
  </div>
  <hr>
</div>

## Import libraries from a package

## 從 package 中匯入庫

Now that you've installed the package,
you can import its libraries and use them in your app.

現在你已經安裝了 package，
你可以在你的應用中匯入和使用 package 中的函式庫。

As with the SDK libraries,
use the **import** directive to use code from an installed library.
The Dart SDK libraries are built in and
are identified with the special `dart:` prefix.
For external libraries installed by pub,
use the `package:` prefix.

與 SDK 庫一樣，使用 `import` 關鍵字匯入使用安裝了的函式庫中的程式碼。
Dart SDK 函式庫是內建的且由特殊的 `dart:` 字首標識。
如果你使用由 pub 命令安裝的外部庫，請使用 `package:` 字首。

1. Get the import details for the package's main library:

   獲取 package 中主要庫的匯入流程：

   {: type="a"}
   1. Go to the [`vector_math` page on pub.dev.]({{site.pub-pkg}}/vector_math)

      開啟 [pub.dev 網站中 `vector_math` package 的網頁。]({{site.pub-pkg}}/vector_math)

   2. Click the **Installing** tab.

      點選 **Installing** 標籤。

   3. Copy the **import** line. It should look something like this:

      複製有 **import** 的這一行程式碼。其看起來像下面這樣：

      <?code-excerpt "vector_victor/lib/vector_victor.dart (import)"?>
      ```dart
      import 'package:vector_math/vector_math.dart';
      ```

2. In your vector_victor app, edit `lib/vector_victor.dart`,
   so that it imports the `vector_math` library and uses some of its API.
   For inspiration, look at the
   [`vector_math` API
   docs]({{site.pub-api}}/vector_math/latest),
   which you can find from the pub.dev site entry.

   在你的 vector_victor 應用中，編輯 `lib/vector_victor.dart` 檔案，
   由此它匯入 vector_math 庫並使用了它的一些 API。
   你可以閱讀
   [vector_math API 文件]({{site.pub-api}}/vector_math/latest)
   獲取更多相關資訊。

   {{site.alert.note}}

     You specify a filename, not a library name,
     when you import a library from a package.

     當你從 package 中匯入一個函式庫時，你指定的是檔名而不是庫名。

   {{site.alert.end}}


## Other resources

## 其它資源

* Dart developers share packages at the [pub.dev site]({{site.pub}}).
  Look there for packages that might be useful to you,
  or share your own Dart packages.

  Dart 開發者們在 [pub.dev site]({{site.pub}}) 分享它們開發的 package。
  你可以在那裡查詢你想要使用的 package 或者分享你自己開發的 package。

* See the [pub package documentation](/guides/packages)
  for more information on using and sharing packages.

  你也可以查閱 [pub package 文件](/guides/packages)
  獲取更多有關如何使用和分享 package 的資訊。
