---
title: Migrating to null safety
title: 遷移至空安全
description: How to move your existing Dart code to the world of null safety
description: 將你現有的程式碼帶到空安全的世界
---

This page describes how and when to migrate your code to [null safety][].
Here are the basic steps for migrating each package that you own:

本文將介紹如何將你的程式碼遷移至 [空安全][null safety]。
以下是對你的 package 逐個遷移的基本步驟：

1. [**Wait**](#step1-wait) for the packages
   that you depend on to migrate.

   [**等待**](#step1-wait) 你依賴的 package 遷移完成。

2. [**Migrate**](#step2-migrate) your package's code,
   preferably using the interactive migration tool.

   [**遷移**](#step2-migrate) 你的 package 的程式碼，最好使用互動式的遷移工具。

3. [**Statically analyze**](#step3-analyze) your package's code.

   [**靜態分析**](#step3-analyze) package 的程式碼。

4. [**Test**](#step4-test) to make sure your changes work.

   [**測試**](#step4-test) 你的程式碼，確保可用。

5. If the package is already on pub.dev,
   [**publish**](#step5-publish) the null-safe version
   as a **prerelease** version.

   如果你已經在 pub.dev 上釋出了你的 package，
   可以將遷移完成的空安全版本以 **預釋出** 版本進行 [**釋出**](#step5-publish)。

{{site.alert.tip}}

  If your application or library is large, check out
  [Gradual null safety migration for large Dart projects][].

  如果你的應用或庫過大，請參考
  [漸進遷移大型 Dart 專案至空安全][Gradual null safety migration for large Dart projects]。

{{site.alert.end}}

{{site.alert.info}}

  **Migrating an app is technically the same as migrating a package.**
  Before migrating an app,
  make sure that all of your dependencies are ready.

  **理論上，遷移應用和遷移 package 的過程一致。**
  在遷移應用之前，確保你所依賴的 package 全部遷移完成後，再進行遷移。

{{site.alert.end}}

For an informal look at the experience of using the migration tool, watch this video:

如果你想預覽遷移工具的體驗，可以檢視以下影片：

<iframe width="560" height="315" src="https://www.youtube.com/embed/eBr5tlumwlg" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

[null safety]: /null-safety
[Gradual null safety migration for large Dart projects]: https://medium.com/dartlang/gradual-null-safety-migration-for-large-dart-projects-85acb10b64a9

**可互動的遷移工具讓你可以簡化遷移至空安全的過程。**

## 1. Wait to migrate {#step1-wait}

## 1. 等待遷移 {#step1-wait}

We strongly recommend migrating code in order, 
with the leaves of the dependency graph being migrated first.
For example, if package C depends on package B, which depends on package A,
then A should be migrated to null safety first, then B, then C.

我們強烈建議你按順序遷移程式碼，先遷移依賴關係中的處於最末端的依賴。
例如，如果 C 依賴了 B，B 依賴了 A，那麼應該按照 A -> B -> C 的順序進行遷移。

![Illustration of C/B/A sentence](/null-safety/null-safety-migration-order.png){:width="454px"}<br>

Although you [_can_ migrate][Unsound null safety]
before your dependencies support null safety,
you might have to change your code when your dependencies migrate.
For example, if you predict that a function will take a nullable parameter but
the package migrates it to be non-nullable,
then passing a nullable argument becomes a compile error.

雖然你在你的所有依賴遷移完成前就 [**可以** 進行遷移][Unsound null safety]，
但在它們遷移完成後，你可能需要再對你的程式碼進行調整。
例如，如果你推測一個函式可以接受一個可空的引數，但依賴的 package 遷移後變為了非空，
在傳遞可空的引數時便會出現編譯錯誤。

{{site.alert.info}}

  **You can—and should—migrate your package before
  packages that depend on it are migrated.**
  Your null-safe package is usable by packages and apps that
  don't use null safety yet,
  as long as they use Dart 2.12 or later.
  For example, the Dart and Flutter core libraries are null safe,
  and they're still usable by apps that haven't migrated to null safety.

  **你應當在其他依賴於你的 package 進行遷移之前，對你的 package 進行遷移。**
  你已遷移的 package 對於未進行遷移的package 和應用而言，
  仍然可用（前提是使用的 Dart 2.12 或更高的版本）。
  例如，Dart 和 Flutter 的核心庫現已完全遷移至空安全，而尚未進行遷移的應用仍然可以使用。

{{site.alert.end}}

This section tells you how to
check and update your package's dependencies,
with the help of the `dart pub outdated` command in null-safety mode.
The instructions assume your code is under **source control**,
so that you can easily undo any changes.

該節會講述如何在空安全模式下，使用 `dart pub outdated` 檢查並更新你的依賴。
如果你的程式碼應用了 **版本管理**，你可以隨時回滾所有的改動。

### Switch to the latest stable Dart release

### 切換至最新的 beta 版本

Switch to the **latest stable release**
of either the Dart SDK or the Flutter SDK.

**切換至最新的穩定版本** 的 Dart SDK 或 Flutter SDK。
你可以根據你的使用情況，參考以下的方式獲取 SDK：

Check that you have Dart 2.12 or later:

檢視你的 Dart 版本是否為 2.12 或更高：

  ```terminal
$ dart --version
```

### Check dependency status

### 檢查所有依賴的遷移狀態

Get the migration state of your package's dependencies,
using the following command:

透過以下命令檢查你的 package 的遷移狀態：

```terminal
$ dart pub outdated --mode=null-safety
```

If the output says that all the packages support null safety,
then you can start migrating.
Otherwise, use the **Resolvable** column to find
null-safe releases, if they exist.

如果你看到所有依賴都已支援空安全，就意味著你可以開始遷移了。
否則請使用 **Resolvable** 列內列舉的已遷移至空安全的版本。

{{site.alert.info}}

  **Why do all dependencies need to support null safety?**
  When all of an app's direct dependencies support null safety,
  you can _run the app_ with sound null safety.
  When all the dev dependencies support null safety,
  you can _run tests_ with sound null safety.
  You might also need null-safe dev dependencies for other reasons,
  such as code generation.

  **為何所有的依賴都需要支援空安全？**
  當應用的所有依賴都支援空安全時，你可以在健全的空安全下 **執行應用**。
  同樣，當開發期依賴也已支援時，你可以在健全的空安全下 **進行測試**。
  你可能會因為需要產生程式碼，而使用已遷移到空安全的開發期依賴。

{{site.alert.end}}

Here's an example of the output for a simple package.
The green checkmarked version for each package supports null safety:

這是一個簡單的 package 的輸入範例。
每個 package 的綠色對勾代表著對應版本已支援空安全：

![Output of dart pub outdated](/null-safety/pub-outdated-output.png)

The output shows that all of the package's dependencies
have resolvable prereleases that support null safety.

上面的輸出說明了所有依賴的 package 都有可使用的已支援空安全的預釋出版本。

If any of your package's dependencies _don't_ yet support null safety,
we encourage you to reach out to the package owner.
You can find contact details on the package page on [pub.dev][].

如果你的 package 的依賴中，有一些 **尚未** 支援空安全，
我們推薦你聯絡對應依賴的作者。
你可以在 [pub.flutter-io.cn][pub.dev] 對應 package 的頁面，找到作者的聯絡資訊。

[pub.dev]: {{site.pub}}


### Update dependencies

### 升級依賴

Before migrating your package's code,
update its dependencies to null-safe versions:

在遷移你的 package 的程式碼之前，請將它的依賴項升級至空安全版本。

1. Run `dart pub upgrade --null-safety` to upgrade to the latest versions
   supporting null safety.
   **Note:** This command changes your `pubspec.yaml` file.

   執行命令 `dart pub upgrade --null-safety` 將依賴升級至支援空安全的最新版本。
   **注意：** 該命令會更改你的 `pubspec.yaml` 檔案。

2. Run `dart pub get`.

   執行命令 `dart pub get`。

## 2. Migrate {#step2-migrate}

## 2. 遷移 {#step2-migrate}

Most of the changes that your code needs to be null safe
are easily predictable.
For example, if a variable can be `null`,
[its type needs a `?` suffix][nullable type]. 
If a named parameter shouldn't be nullable,
mark it [`required`][required]
or give it a [default value][].

你的程式碼裡大部分需要更改的程式碼，都是可以輕易推導的。
例如，如果一個變數可以為空，[它的型別需要 `?` 字尾][nullable type]。
一個不可以為空的命名引數，需要使用 [`required` 標記][required]，
或者給定其一個 [預設值][default value]。

You have two options for migrating:

針對遷移，你有兩個選項可以選擇：

* [Use the migration tool][migration tool],
  which can make most of the easily predictable changes for you.

  [使用遷移工具][migration tool]，它可以幫你處理大多數可推導的變更。

* [Migrate your code by hand.](#migrating-by-hand)

  [自己動手，豐衣足食。](#migrating-by-hand)

{{site.alert.tip}}

  For additional help while migrating code, check the
  [null safety FAQ][].

  若你在遷移過程中需要額外的幫助，請檢視 [空安全常見問題][null safety FAQ]。

{{site.alert.end}}

[nullable type]: /null-safety#creating-variables
[required]: /null-safety/understanding-null-safety#required-named-parameters
[default value]: /guides/language/language-tour#default-parameters
[migration tool]: #migration-tool
[null safety FAQ]: /null-safety/faq


### Using the migration tool {#migration-tool}

### 使用遷移工具 {#migration-tool}

The migration tool takes a package of null-unsafe Dart code
and converts it to null safety.
You can guide the tool's conversion by
adding [hint markers][] to your Dart code.

遷移工具會帶上一個非空安全的 package ，將它轉換至空安全。
你可以先在程式碼中新增 [提示標記][hint markers] 來引導遷移工具的轉換。

[hint markers]: #hint-markers

Before starting the tool, make sure you're ready:

開始轉換前，請做好如下的準備：

* Use the latest beta release of the Dart SDK.

  使用最新的 Dart SDK 穩定版本。

* Use `dart pub outdated --mode=null-safety` to make sure that
  all dependencies are null safe and up-to-date.

  執行 `dart pub outdated --mode=null-safety`
  以確保所有依賴為最新且空安全。

Start the migration tool by running the `dart migrate` command
in the directory that contains the package's `pubspec.yaml` file:

在包含 `pubspec.yaml` 的目錄下，執行 `dart migrate` 命令，啟動遷移工具。

```terminal
$ dart migrate
```

If your package is ready to migrate,
then the tool produces a line like the following:

如果你的 package 可以進行遷移，工具會輸出類似以下的內容：

```terminal
View the migration suggestions by visiting:

  http://127.0.0.1:60278/Users/you/project/mypkg.console-simple?authToken=Xfz0jvpyeMI%3D
```

Visit that URL in a Chrome browser
to see an interactive UI
where you can guide the migration process:

使用 Chrome 瀏覽器存取 URL，你可以看到一個互動式的介面，引導你進行遷移：

![Screenshot of migration tool](/null-safety/migration-tool.png)

For every variable and type annotation,
you can see what nullability the tool infers.
For example, in the preceding screenshot,
the tool infers that the `ints` list (previously a list of `int`)
in line 1 is nullable, and thus should be a list of `int?`.

你可以在工具中看到其推斷的所有變數和型別註解。
例如，在上面的截圖中，工具推斷第一行的 `ints` 列表元素可能為空，
所以應該變為 `int?`（先前為 `int`）。

#### Understanding migration results

#### 理解遷移的結果

To see the reasons for each change (or non-change),
click its line number in the **Proposed Edits** pane.
The reasons appear in the **Edit Details** pane.

若要了解每個變化（或者未變化）的原因，點選 **Proposed Edits** 視窗中的行數，
原因會出現在 **Edit Details** 視窗中。

For example, consider the following code,
from before null safety:

舉個例子，假設我們有如下的非空安全的程式碼：

```dart
var ints = const <int>[0, null];
var zero = ints[0];
var one = zero + 1;
var zeroOne = <int>[zero, one];
```

The default migration when this code is outside a function
(it's different within a function)
is backward compatible but not ideal:

當這些程式碼處在函式外時，預設的遷移改動是向後相容的，但並不理想
（在函式內時會稍有不同）：

```dart
var ints = const <int?>[0, null];
var zero = ints[0];
var one = zero! + 1;
var zeroOne = <int?>[zero, one];
```

By clicking the **line 3** link,
you can see the migration tool's reasons for
adding the `!`.
Because you know that `zero` can't be null,
you can improve the migration result.

點選 **line 3** 連結，你可以看到遷移工具新增 `!` 的原因。
而因為你知道 `zero` 不會為空，所以你可以改進遷移結果。

#### Improving migration results {#hint-markers}

#### 改進遷移的結果 {#hint-markers}

When analysis infers the wrong nullability,
you can override its proposed edits by inserting temporary hint markers:

當分析結果推導了錯誤的可空性時，你可以新增臨時的提示標記來改變建議的編輯：

* In the **Edit Details** pane of the migration tool,
  you can insert hint markers using the
  **Add `/*?*/` hint** and **Add `/*!*/` hint** buttons.

  在遷移工具的 **Edit Details** 窗格中，你可以透過 **Add `/*?*/` hint** 和
  **Add `/*!*/` hint** 按鈕來新增提示標記。

  These buttons add comments to your file immediately,
  and there's **no Undo**.
  If you don't want a hint that the tool inserted,
  you can use your usual code editor to remove it.

  按下這些按鈕，相應的標記會立刻新增到程式碼中，並且 **無法撤銷**。
  如果你想刪除標記，可以和平常一樣使用程式碼編輯器刪除它。

* You can use an editor to add hint markers,
  even while the tool is still running.
  Because your code hasn't opted into null safety yet,
  you can't use new null-safety features.
  You can, however, make changes like refactoring
  that don't depend on null-safety features.

  就算遷移工具正在執行，你也可以使用編輯器新增提示標記。
  由於你的程式碼還未遷移到空安全，所以無法使用空安全的新特性。
  但是你可以進行與空安全無關的改動，例如重構。

  When you've finished editing your code,
  click **Rerun from sources** to pick up your changes.

  當你完成編輯後，點選 **Rerun from sources** 進行更改。

The following table shows the hint markers that you can use
to change the migration tool's proposed edits.

下方的表格展示了可以使用的提示標記。

|------------------+--------------------------------------------------------------------------|
| <t>Hint marker</t><t>提示標記</t> | <t>Effect on the migration tool</t><t>對遷移工具的影響</t> |
|------------------|--------------------------------------------------------------------------|
| <code><em>expression</em>&nbsp;/*!*/</code> | <t>Adds a `!` to the migrated code, casting _expression_ to its underlying non-nullable type.</t><t>新增 `!` 至程式碼中，將 **表示式** 轉換為其基礎型別對應的不可空的型別。</t> |
| <code><em>type</em> /*!*/</code> | <t>Marks _type_ as non-nullable.</t><t>將 **型別** 標記為非空。</t> |
| `/*?*/`          | <t>Marks the preceding type as nullable.</t><t>將前面的型別標記為可空。</t> |
| `/*late*/`       | <t>Marks the variable declaration as `late`, indicating that it has late initialization.</t><t>將變數宣告標記為 `late`，表示其不會第一時間進行初始化。</t> |
| `/*late final*/` | <t>Marks the variable declaration as `late final`, indicating that it has late, one-time initialization.</t><t>將變數宣告標記為 `late final`，表示其不會第一時間進行初始化，且初始化後不可改變。</t> |
| `/*required*/`   | <t>Marks the parameter as `required`.</t><t>將引數標記為 `required`。</t> |
{:.table .table-striped}

A single hint can have ripple effects elsewhere in the code.
In the example from before,
manually adding a `/*!*/` marker where `zero` is assigned its value (on line 2)
makes the migration tool infer the type of `zero` as `int` instead of `int?`.
This type change can affect code that directly or indirectly uses `zero`.

一個提示也可能產生蝴蝶效應，影響其他的程式碼。
在先前的例子中，如果在 `zero` 被賦值的位置（第二行）新增一個 `/*!*/` 標記，
遷移工具就會推斷 `zero` 的型別是 `int` 而非 `int?`。
這就會影響到直接或間接使用了 `zero` 的程式碼。

```dart
var zero = ints[0]/*!*/;
```

With the above hint, the migration tool changes its proposed edits,
as the following code snippets show.
Line 3 no longer has a `!` after `zero`,
and in line 4 `zeroOne` is inferred to be
a list of `int`, not `int?`.

透過添加了以上的提示，遷移工具將調整建議的更改，如下面的程式碼所示。
第三行的 `zero` 後面不再有 `!`，第四行的 `zeroOne` 也被推斷為 `int` 列表而不是 `int?`。

<table markdown="1">
<tr>
<th><t>First migration</t><t>首次遷移</t></th>
<th><t>Migration with hint</t><t>新增提示後的遷移</t></th>
</tr>
<tr markdown="1">
  <td markdown="1">
```dart
var ints = const <int?>[0, null];
var zero = ints[0];
var one = zero! + 1;
var zeroOne = <int?>[zero, one];
```
  </td>
  <td markdown="1">
```dart
var ints = const <int?>[0, null];
var zero = ints[0]/*!*/;
var one = zero + 1;
var zeroOne = <int>[zero, one];
```
  </td>
</tr>
</table>

#### Opting out files

#### 只遷移部分檔案

Although we recommend migrating all at once,
sometimes that isn't practical,
especially in a large app or package.
To opt out a file or directory,
click its green checkbox.
Later, when you apply changes,
each opted out file will be unchanged
except for a 2.9 [version comment][].

儘管我們希望你能一次性完成遷移工作，但對於大體量的應用或 package 而言並不是簡單的事。
如果你想只遷移部分檔案，請將暫時不遷移的檔案前方的綠色勾選框取消勾選。
稍後應用遷移更改時，這些檔案會加上 Dart 2.9 [版本註釋][version comment]，
其他內容保持不變。

For more information about incremental migration, see
[Unsound null safety][].

更多有關漸進遷移空安全的內容，請閱讀 [非健全的空安全][Unsound null safety]。

[version comment]: /guides/language/evolution#per-library-language-version-selection


#### Applying changes

#### 應用更改

When you like all of the changes
that the migration tool proposes, click **Apply migration**.
The migration tool deletes the hint markers and
saves the migrated code.
The tool also updates the minimum SDK constraint in the pubspec,
which opts the package into null safety.

當你覺得遷移工具提示的更改部分可以應用了，點選 **Apply migration**。
遷移工具會刪除所有的提示標記，儲存遷移後的程式碼。
同時，遷移工具也會更改 pubspec 的 SDK 限制，將 package 遷移至空安全。

The next step is to [statically analyze your code](#step3-analyze).
If it's valid, then [test your code](#step4-test).
Then, if you've published your code on pub.dev,
[publish a null-safe prerelease](#step5-publish).

下一步就是對程式碼進行 [**靜態分析**](#step3-analyze)。
如果一切正常，下一步就是 [測試你的程式碼](#step4-test)。
最後，如果你已經將 package 釋出至 pub.flutter-io.cn，
[釋出空安全的預覽版本](#step5-publish)。

### Migrating by hand

### 手動遷移

If you prefer not to use the migration tool,
you can migrate manually.

如果你不想使用遷移工具，你也可以手動進行遷移。

We recommend that you **first migrate leaf libraries**—libraries 
that don't import other files from the package.
Then migrate libraries that directly depend on the leaf libraries.
End by migrating the libraries that have the most
intra-package dependencies.

我們推薦你 **優先遷移最下層的函式庫** —— 指的是沒有匯入其他 package 的函式庫。
接著遷移直接依賴了下層庫的依賴庫。
最後再遷移依賴項最多的函式庫。

For example, say you have a `lib/src/util.dart` file
that imports other (null-safe) packages and core libraries,
but that doesn't have any `import '<local_path>'` directives.
Consider migrating `util.dart` first,
and then migrating simple files that depend only on `util.dart`.
If any libraries have cyclic imports
(for example, A imports B which imports C, and C imports A),
consider migrating those libraries together.

舉個例子，假設你的 `lib/src/util.dart` 匯入了其他（空安全）的 package 和核心庫，
但它沒有包含任何 `import '<本地路徑>'` 的參考。
那麼你應當優先考慮遷移 `util.dart`，然後遷移依賴了 `util.dart` 的檔案。
如果有一些迴圈參考的函式庫（例如 A 參考了 B，B 參考了 C，C 參考了 A），
建議同時對它們進行遷移。

To migrate a package by hand, follow these steps:

手動對 package 進行遷移時，請參考以下步驟：

1. Edit the package's `pubspec.yaml` file,
   setting the minimum SDK constraint to at least `2.12.0`:

   編輯 package 的 `pubspec.yaml` 檔案， 
   將最低 SDK 版本設定到至少為 `2.12.0`：

   ```yaml
   environment:
     sdk: '>=2.12.0 <3.0.0'
   ```

2. Regenerate the [package configuration file][]:

   重新產生 [package 的配置檔案][package configuration file]：

   ```terminal
   $ dart pub get
   ```

   [package configuration file]: https://github.com/dart-lang/language/blob/master/accepted/2.8/language-versioning/package-config-file-v2.md

   Running `dart pub get` with a lower SDK constraint of at least `2.12.0`
   sets the default language version of
   every library in the package to a minimum of 2.12,
   opting them all in to null safety.

   在版本最低是 `2.12.0` 的 SDK 上執行 `dart pub get` 時，
   會將每個 package 的預設 SDK 最低版本設定為 2.12，
   並且預設它們已經遷移至空安全。

3. Open the package in your IDE. <br>
   You're likely to see a lot of analysis errors.
   That's OK.

   在你的 IDE 上開啟package 。<br>
   你也許會看到很多錯誤，沒關係，讓我們繼續。

4. Migrate the code of each Dart file,
   using the analyzer to identify static errors. <br>
   Eliminate static errors by adding `?`, `!`, `required`, and `late`,
   as needed.

   利用分析器來辨析靜態錯誤，逐個遷移 Dart 檔案。<br>
   按需新增 `?`、`!`、`required` 以及 `late` 來消除靜態錯誤。

See [Unsound null safety][]
for more help on migrating code by hand.

想獲得更多手動遷移的幫助，請前往 [非健全的空安全][Unsound null safety]。

[Unsound null safety]: /null-safety/unsound-null-safety


## 3. Analyze {#step3-analyze}

## 3. 分析 {#step3-analyze}

Update your packages
(using `dart pub get` in your IDE or on the command line).
Then use your IDE or the command line
to perform [static analysis][] on your code:

更新你的 package（在 IDE 或命令列工具中使用 `dart pub get`）
後在 IDE 或命令列工具中對你的程式碼進行 [靜態分析][static analysis]：

```terminal
$ dart pub get
$ dart analyze     # or `flutter analyze`
```

[static analysis]: /guides/language/analysis-options


## 4. Test {#step4-test}

## 4. 測試 {#step4-test}

If your code passes analysis, run tests:

如果你的程式碼通過了分析，接下來可以開始測試：

```terminal
$ dart test       # or `flutter test`
```

You might need to update tests that expect null values.

你可能需要更新使用了空值作為預期使用案例的測試程式碼。

If you need to make large changes to your code,
then you might need to remigrate it.
If so, revert your code changes before using the migration tool again.

如果你需要對程式碼作出大量的更改，那麼你可能需要重新對程式碼進行遷移。
這時請先回滾程式碼更改，再執行遷移工具進行遷移。

## 5. Publish {#step5-publish}

## 5. 釋出 {#step5-publish}

We encourage you to publish packages—possibly as prereleases—as 
soon as you migrate:

我們希望你完成遷移後儘快將其釋出，可以作為預覽版：

* [Set the package version to indicate a breaking change.](#package-version)

  [調整 package 的版本，表示該版本包含了破壞性的改動。](#package-version)

* [Update the SDK constraints and package dependencies.](#check-your-pubspec)

  [更新 SDK 的限制和依賴的 package 的版本。](#check-your-pubspec)

* [Publish the package](/tools/pub/publishing).
  If you don't consider this version to be a stable release, 
  then [publish the package as a prerelease][].

  [釋出 package](/tools/pub/publishing)。
  如果你不想釋出穩定版本，你可以
  [釋出為預釋出版本][publish the package as a prerelease]。

[publish the package as a prerelease]: /tools/pub/publishing#publishing-prereleases


### Update the package version {#package-version}

### 更新 package 的版本 {#package-version}

### Package 的版本

Update the version of the package
to indicate a breaking change:

你可以修改版本以表示該版本包含了破壞性的改動：

* If your package is already at `1.0.0` or greater,
  increase the major version.
  For example, if the previous version is `2.3.2`,
  the new version is `3.0.0`.

  如果你的 package 版本已經大於或等於 `1.0.0`，請提升主版本。
  例如，上一個版本為 `2.3.2`，那麼新版本應該為 **`3.0.0`**。

* If your package hasn't reached `1.0.0` yet,
  _either_ increase the minor version _or_ update the version to `1.0.0`.
  For example, if the previous version is `0.3.2`,
  the new version is either `0.4.0` or `1.0.0`.

  如果你的 package 的版本還未高於 `1.0.0`，
  你可以 **提升次版本**，也可以 **提升至 `1.0.0`**。
  例如，上一個版本為 `0.3.2`，那麼新版本可以是 **`0.4.0`** 或 **`1.0.0`**。

### Check your pubspec

### 檢查你的 pubspec

Before you publish a stable null safety version of a package,
we strongly recommend following these pubspec rules:

在你釋出穩定版本的空安全 package 前，我們強烈建議你遵循以下 pubspec 的規則：

* Set the Dart lower SDK constraint to the lowest stable version
  that you've tested against (at least `2.12.0`).

  將 SDK 的最低限制設定為你測試過的最低穩定版本（至少是 `2.12.0`）。

* Use stable versions of all direct dependencies.

  所有的直接依賴都使用穩定版本。

## Welcome to null safety

## 歡迎使用空安全

If you made it this far,
you should have a fully migrated, null-safe Dart package.
If all of the packages you depend on are migrated too,
then your program is sound with respect to null-reference errors.

如果你走到了這一步，你應該已經完全將你的 Dart package 遷移至空安全了。
當你的所有依賴也都完成了遷移，那麼你的程式就是健全的，同時可以正確處理空參考的錯誤。

From all of the Dart team, *thank you* for migrating your code.

謹代表 Dart 團隊的所有成員，**感謝你** 遷移你的程式碼。
