---
title: Publishing packages
title: 釋出 package
description: Learn how to publish a Dart package to pub.dev.
description: 學習如何將 Dart package 釋出到 pub.dev 網站。
---

[The pub package manager][pub] isn't just for using other people's packages.
It also allows you to share your packages with the world. If you have a useful
project and you want others to be able to use it, use the `dart pub publish`
command.

[Pub package 管理工具][pub] 不但可以讓你使用其他人開發的 package，
而且能向全世界分享你自己製作的 package。如果你有一個有用的專案，
且希望別人能夠用到它，請使用 `dart pub publish` 命令。

{{site.alert.note}}

  To publish to a location other than pub.dev,
  or to prevent publication anywhere, use the `publish_to` field,
  as defined in the [pubspec][].

  釋出到除 pub.dev 以外的其他位置，或者不在任何地方釋出，
  使用 `publish_to` 引數，相當於在 [pubspec][] 中定義。

{{site.alert.end}}

## Publishing is forever

## 釋出是永久的

Keep in mind that publishing is forever. As soon as you publish your package,
users can depend on it. Once they start doing that, removing
the package would break theirs. To avoid that, the [pub.dev policy][policy]
disallows unpublishing packages except for very few cases.

切記，釋出過的 package 將會永久存在。只要你釋出了你的 package，使用者就能依賴它。
而依賴關係一旦建立，移除 package 將破壞他們的專案。
為了避免這種事發生，除了極少數情況，[pub.dev 政策][policy] 不允許撤回已經發布的 package。

You can always upload new versions of your package, but
old ones will continue to be available for users that aren't ready to
upgrade yet.

你可以一直上傳自己 package 的新版本，但是舊版本對於那些尚未準備好升級的使用者仍然可用。

For already published packages that are no longer relevant or being maintained,
you can [mark them as discontinued](#discontinue).

對於那些已經發布，但不再相關或不再維護的 package，你可以 [把他們標記為終止](#discontinue)。

## Preparing to publish

## 準備釋出

When publishing a package, it's important to follow the [pubspec
format][pubspec] and
[package layout conventions][].
Some of these are required in order for others to be able to use your package.
Others are suggestions to help make it easier for users to understand and work
with your package. In both cases, pub tries to help you by pointing out what
changes will help make your package play nicer with the Dart ecosystem. There
are a few additional requirements for uploading a package:

釋出一個 package 時，遵守 [pubspec 格式][pubspec] 和
[package 佈局慣例][package layout conventions] 很重要。
為了其他人能使用你的 package，有些要求是必須的，其他則是一些有助於使用者更好理解和使用的建議。
對於以上兩者，pub 都會指出具體的改變方法，
幫助你的 package 在 Dart 生態系統中展現的更好。對於上傳 package 有一些額外的要求：

* You must include a `LICENSE` file.
  We recommend the [BSD 3-clause license][],
  which the Dart and Flutter teams typically use.
  However, you can use any license that's appropriate for your package.
  You must also have the legal right to
  redistribute anything that you upload as part of your package.

  你的 package 必須包含一個 `LICENSE` 檔案。
  我們推薦 [BSD 3-clause 許可證][BSD 3-clause license]，
  也就是 Dart 和 Flutter 團隊所使用的開源許可證。
  同時，對於你所上傳的 package 任意部分，你必須擁有重新發布的合法權利。

* Your package must be smaller than 100 MB after gzip compression. If
  it's too large, consider splitting it into multiple packages, using a
  `.pubignore` file to remove unnecessary content, or cutting down
  on the number of included resources or examples.

  透過 gzip 壓縮後，你的 package 必須小於 100 MB。
  如果它所佔空間過大，考慮將它分割為幾個小的 package、
  使用 `.pubignore` 移除不需要的檔案，或者減少包含資源或例項的數量。

* Your package should depend only on hosted dependencies (from the default pub
  package server) and SDK dependencies (`sdk: flutter`). These restrictions
  ensure that dependencies of your packages cannot become unavailable in the
  future.

  你的 package 應該僅依賴於已被託管的依賴項（來自預設的 pub 包服務）
  和屬於 SDK 的依賴項（`sdk: flutter`）。
  這些限制條件確保了你的 package 的依賴項在未來依然可用。

* You must have a [Google Account][],
  which pub uses to manage package upload permissions.
  Your Google Account can be associated with a Gmail address or
  with any other email address.

  你必須有一個 [Google 賬戶][Google Account]，pub 將使用它來管理 package 上傳許可權。
  你的 Google 賬戶可以與 Gmail 或其他任何郵箱地址關聯。

### Important files

### 重要的檔案

Pub uses the contents of a few files to create a page for your
package at `pub.dev/packages/<your_package>`. Here are the files that
affect how your package's page looks:

Pub 在 `pub.dev/packages/<your_package>` 裡，
使用幾個檔案的內容來建立你的 package 頁面。
以下就是會影響到頁面效果的幾個檔案：

* **README.md:** The `README.md` file
  is the main content featured in your package's page.
  The file's contents are rendered as [Markdown.][Markdown]

  **README.md:** `README.md` 檔案是你 package 頁面的主要組成部分。
  它的內容將會按 [Markdown][Markdown] 格式渲染。

  For guidance on how to write a great README, see
  [Writing package pages](/guides/libraries/writing-package-pages).

  想按照規範編寫一個超棒的 README 嗎，請參閱 [編寫 package 介紹頁](/guides/libraries/writing-package-pages)。

* **CHANGELOG.md:** Your package's `CHANGELOG.md` file, if found,
  is also featured in a tab on your package's page,
  so that developers can read it right from pub.dev.
  The file's contents are rendered as [Markdown.][Markdown]

  **CHANGELOG.md:** 如果你 package 的 `CHANGELOG.md` 檔案存在，
  同樣會作為頁面的一個選項卡展示。開發者可透過 pub.dev 閱讀它。
  它的內容將會按 [Markdown][Markdown] 格式渲染。

* **The pubspec:** Your package's `pubspec.yaml` file is used to fill out
  details about your package on the right side of your package's page, like its
  description, homepage, etc.

  **The pubspec:** 你 package 的 `pubspec.yaml` 檔案被用於
  填寫關於 package 本身的細節，例如它的描述，主頁等等。
  這些資訊將被展現在頁面的右側。

### Advantages of using a verified publisher {#verified-publisher}

### 使用已驗證釋出者的優點 {#verified-publisher}

You can publish packages using either a verified publisher (recommended)
or an independent Google Account.
Using a verified publisher has the following advantages:

你可以使用已驗證釋出者（推薦）或一個獨立的 Google 賬戶來發布 package。
使用已驗證釋出者有以下幾個優點：

* The consumers of your package know that the publisher domain has been verified.

  你 package 的使用者知道釋出者域名已經過驗證。

* You can avoid having pub.dev display your personal email address.
  Instead, pub.dev displays the publisher domain and contact address.

  你可以避免讓 pub.dev 展示你的個人郵箱，pub.dev 將展示釋出者域名和聯絡地址作為代替。

* A verified publisher badge <img src="/assets/img/verified-publisher.svg" alt="pub.dev verified publisher logo"> is displayed next to your package name on both search pages and individual package pages.

  一個已驗證釋出者徽章
  <img src="/assets/img/verified-publisher.svg" alt="pub.dev verified publisher logo">
  將在你的 package 名字旁邊展示，
  不論是在搜尋頁面還是單獨的 package 頁面。

### Creating a verified publisher {#create-verified-publisher}

### 建立一個已驗證釋出者 {#create-verified-publisher}

To create a verified publisher, follow these steps:

想要建立一個已驗證釋出者，跟隨以下步驟：

1. Go to [pub.dev.]({{site.pub}})

   存取 [pub.dev]({{site.pub}})。

1. Log in to pub.dev using a Google Account.

   用一個 Google 賬戶登入 pub.dev。

1. In the user menu in the top-right corner, select **Create Publisher**.

   在右上角的使用者選單中，選擇 **建立釋出者**。

1. Enter the domain name that you want to associate with your publisher (for example,
   `dart.dev`), and click **Create Publisher**.

   輸入你想要與釋出者關聯的域名（例如，`dart.dev`），然後點選 **建立釋出者**。

1. In the confirmation dialog, select **OK**.

   在確認彈框中，選擇 **好**。

1. If prompted, complete the verification flow, which opens the [Google
   Search Console.](https://search.google.com/search-console/about)
   
   如果收到提示，在開啟的 [Google 搜尋控制檯](https://search.google.com/search-console/about) 中完成確認流程。
   
   * When adding DNS records, it may take a few hours before the Search Console
   reflects the changes.
     
     新增 NDS 記錄，可能需要幾個小時讓搜尋控制檯確認改動。
     
   * When the verification flow is complete, return to step 4.
   
     確認流程完成後，返回第四步。

## Publishing your package

## 釋出你的 package 

Use the [`dart pub publish`][] command to publish your package for the first time,
or to update it to a new version.

使用 [`dart pub publish`][] 命令來首次釋出你的 package，或者把它升級到一個新版本。

### Performing a dry run

### 示範一次試執行

To test how `dart pub publish` will work, you can perform a dry run:

為了測試 `dart pub publish` 命令會如何工作，你可以示範一次試執行（不會真的釋出，只顯示效果）：

```terminal
$ dart pub publish --dry-run
```

Pub makes sure that your package follows the
[pubspec format][pubspec] and
[package layout conventions][],
and then uploads your package to [pub.dev.]({{site.pub}}) Pub also shows you all of
the files it intends to publish. Here's an example of publishing a package
named `transmogrify`:

Pub 會確認你的 package 符合 [pubspec 格式][pubspec]
和 [package 佈局慣例][package layout conventions]，
然後把它上傳至 [pub.dev]({{site.pub}})。
Pub 也會提前向你展示所有準備釋出的檔案。
如下是一個範例，展示了將要釋出的名為 `transmogrify` 的 package：

{:.console-output}
```nocode
Publishing transmogrify 1.0.0
    .gitignore
    CHANGELOG.md
    README.md
    lib
        transmogrify.dart
        src
            transmogrifier.dart
            transmogrification.dart
    pubspec.yaml
    test
        transmogrify_test.dart

Package has 0 warnings.
```

### Publishing

### 釋出

When you're ready to publish your package, remove the `--dry-run` argument:

當你已經準備好釋出你的 package 後，移除 `--dry-run` 引數：

```terminal
$ dart pub publish
```

{{site.alert.note}}

  The pub command currently doesn't support publishing a new package directly to a
  verified publisher. As a temporary workaround, publish new packages to a Google Account,
  and then [transfer the package to a publisher](#transferring-a-package-to-a-verified-publisher).

  pub 命令目前不支援把一個新的 package 直接釋出至已驗證釋出者。
  可先把新 package 釋出至一個 Google 賬戶，作為一個臨時空間，
  然後再 [把 package 轉移至已驗證釋出者](#transferring-a-package-to-a-verified-publisher)。

  Once a package has been transferred to a publisher,
  you can update the package using `dart pub publish`.

  一旦這個 package 被轉移到已驗證釋出者，你就可以透過 `dart pub publish` 命令來更新它。

{{site.alert.end}}

After your package has been successfully uploaded to pub.dev, any pub user can
download it or depend on it in their projects. For example, if you just
published version 1.0.0 of your `transmogrify` package, then another Dart
developer can add it as a dependency in their `pubspec.yaml`:

在你的 package 成功上傳至 pub.dev 之後，任何使用者都能夠下載或在專案中依賴它。
例如，如果你剛剛釋出了你的 `transmogrify` package 1.0.0 版本，
那麼另一個 Dart 開發者將可以把它作為一項依賴新增到 `pubspec.yaml` 檔案中：

```yaml
dependencies:
  transmogrify: ^1.0.0
```

### Transferring a package to a verified publisher

### 把 package 轉移至已驗證釋出者

To transfer a package to a verified publisher,
you must be an [uploader](#uploaders) for the package
and an admin for the verified publisher.

想要把 package 轉移至已驗證釋出者，
你必須是 package 的一個[上傳者](#uploaders)，以及已驗證釋出者的管理員。

{{site.alert.note}}

  This process isn't reversible. Once you transfer a package to a publisher,
  you can't transfer it back to an individual account.

  這個過程是不可撤回的。一旦你把 package 轉移至已驗證釋出者，
  你將無法再把它轉移回一個單獨的賬戶。

{{site.alert.end}}

Here's how to transfer a package to a verified publisher:

以下是如何把 package 轉移至已驗證釋出者：

1. Log in to [pub.dev]({{site.pub}}) with a Google Account that's listed as
   an uploader of the package.

   用一個作為 package 上傳者之一的 Google 賬戶登入 [pub.dev]({{site.pub}})。

1. Go to the package details page (for example,
   `{{site.pub-pkg}}/http`).

   存取 package 詳情頁面（例如，`{{site.pub-pkg}}/http`）。

1. Select the **Admin** tab.

   選擇 **管理員** 選項卡。

1. Enter the name of the publisher, and click **Transfer to Publisher**.

   輸入已驗證釋出者的名稱，然後點選 **轉移至已驗證釋出者**。

## What files are published?

## 哪些檔案會被髮布？

**All files** under the package root directory are
included in the published package,
with the following exceptions:

在你 package 中的 **所有檔案** 都會被包含在釋出的 package 中，
除了以下幾個例外：

* Any _hidden_ files or directories—that is,
  files with names that begin with dot (`.`)

  所有 **隱藏** 檔案和資料夾——即檔名以 `.` 開頭的檔案

* Any directories with the name `packages`

  所有名字中包含 `packages` 的資料夾

* Files and directories ignored by a `.pubignore` or `.gitignore` file

  `.pubignore` 和 `.gitignore` 檔案忽略的所有檔案

If you want different ignore rules for `git` and `dart pub publish`,
then overrule the `.gitignore` file in a given directory by
creating a `.pubignore` file.
(If a directory contains both a `.pubignore` file and a `.gitignore` file,
then  `dart pub publish` doesn't read that directory's `.gitignore` file.)
The format of `.pubignore` files is the same as the
[`.gitignore` file format][git-ignore-format].

如果你需要 `git` 和 `dart pub publish` 有不同的規則，
你可以建立 `.pubignore` 對 `.gitignore` 進行重載。
（如果一個資料夾既有 `.pubignore` 又有 `.gitignore` 檔案，
則 `dart pub publish` 不會採用 `.gitignore` 的規則）
`.pubignore` 檔案的格式與 [`.gitignore` 檔案格式][git-ignore-format] 相同。

To avoid publishing unwanted files,
follow these practices:

如果你不想釋出一些檔案，請參考以下步驟：

* Either delete any files that you don't want to include,
  or add them to a `.pubignore`  or `.gitignore` file.

  刪除它們，或者把它們加入 `.pubignore` 或 `.gitignore` 檔案。

* When uploading your package,
  carefully examine the list of files that
  `dart pub publish` says it's going to publish.
  Cancel the upload if any undesired files appear in that list.

  執行 `dart pub publish` 上傳前仔細檢查檔案列表。
  如果發現了不需要的檔案，則取消這次上傳。

{{site.alert.note}}

  Most packages don't need a  `.pubignore` file.
  More information about useful scenarios for this
  can be found in this [StackOverflow answer][pubignore-when].

  大部分 package 不會需要一個 `.pubignore` 檔案，
  這個檔案的應用場景可以在 [這個回答][pubignore-when] 裡找到。

{{site.alert.end}}

## Platform support

## 平台支援

The [pub.dev site]({{site.pub}}) detects which platforms a package supports,
displaying these platforms on the package page.
Users of pub.dev can filter searches by platform.

pub.dev 會檢測 package 支援哪些平台，並呈現到 package 的頁面上。
使用者可以過濾查詢特定平台的 package。

To change the automatically generated list of supported platforms,
[specify supported platforms][] in the pubspec.

若要改變自動檢測產生的支援平台列表，則需要在 pubspec 檔案中
[指定平台][specify supported platforms]。

[specify supported platforms]: /tools/pub/pubspec#platforms


## Uploaders

## 上傳者

Whoever publishes the first version of a package automatically becomes
the first and only person authorized to upload additional versions of that package.

釋出 package 第一個版本的人，將自動成為唯一有權對其進行更新的人。

To allow or disallow other people to upload versions, either:

要允許或取消其他人更新版本，可以從下列兩種方法中任選一種：

* Manage authorized uploaders on the admin page for the package:
  `https://pub.dev/packages/<package>/admin`.

  管理頁面管理授權的 package 上傳者：
  `https://pub.dev/packages/<package>/admin`。

* Transfer the package to a [verified publisher][];
  all members of a publisher are authorized to upload.

  將 package 轉給一個 [已驗證的釋出者][verified publisher]，
  這個釋出者的所有成員都有上傳的權利。
  
## Locating the package publisher

## 釋出者展示

If a package has a verified publisher,
then the pub.dev page for that package displays the publisher domain.

如果 package 有了已驗證釋出者，
pub.dev 頁面將展示已驗證釋出者域名。

For packages published without a publisher,
the publisher is not disclosed for privacy reasons
(the Publisher field just says "unverified uploader").

如果 package 的釋出者沒有選擇認證，
出於隱私原因，我們不會披露釋出者的資訊。
(Publisher 會顯示 "unverified uploader"，
即：未經驗證的上傳者)。

## Publishing prereleases

## 以預發行的方式釋出

As you work on a package, consider publishing it as a prerelease.
Prereleases can be useful when *any* of the following are true:

如果你正專注於開發一個 package，考慮將它以預發行的方式釋出。
預發行將在以下 **任意情況下** 下變得實用：

* You're actively developing the next major version of the package.

  你正在活躍的開發該 package 的下一個主版本。

* You want beta testers for the next release candidate of the package.

  你想要為該 package 下一個候選的釋出版做 beta 測試。

* The package depends on an unstable version of the Dart or Flutter SDK.

  該 package 依賴於 Dart 或 Flutter SDK 的一個不穩定版本。

As described in [semantic versioning][semver], to make a prerelease of a version
you append a suffix to the version. For example, to make a prerelease of
version `2.0.0` you might use the version `2.0.0-dev.1`. Later, when you
release version `2.0.0`, it will take precedence over all `2.0.0-XXX` prereleases.

正如在 [版本號語義][semver] 中描述的那樣，要製作一個版本的預釋出，
你需要為其新增一個字尾。例如，要給 `2.0.0` 版本做一個預釋出，
你可能會使用 `2.0.0-dev.1` 作為版本號。
接下來，當你釋出 `2.0.0` 正式版後，它將優先於所有諸如 `2.0.0-XXX` 的預釋出。

Because pub prefers stable releases when available, users of a prerelease package
might need to change their dependency constraints.
For example, if a user wants to test prereleases of version 2.1, then
instead of `^2.0.0` or `^2.1.0` they might specify `^2.1.0-dev.1`.

當穩定釋出版可用時，pub 會主動選擇它們，所以使用預釋出的使用者可能需要改變它們的依賴約束。
例如，如果一個使用者想要測試 2.1 版本的預釋出，
那麼他可能需要指明是 `^2.1.0-dev.1` 版本，而不是 `^2.0.0` 或者 `^2.1.0` 版本。

{{site.alert.note}}

  If a stable package in the dependency graph depends on a prerelease,
  then pub chooses that prerelease instead of a stable release.

  如果一個穩定版的 package 在依賴關係中依賴於一個預釋出，那麼 pub 將選擇預釋出而非穩定釋出。

{{site.alert.end}}

When a prerelease is published to pub.dev,
the package page displays links to both the prerelease and the stable release.
The prerelease doesn't affect the analysis score, show up in search results,
or replace the package `README.md` and documentation.

當一個預釋出被髮布在 pub.dev，package 主頁會同時展示預釋出和穩定釋出的連結。
預釋出不會影響分析評分，不會出現在搜尋結果裡，也不會代替 package 的 `README.md` 檔案和說明文件。

## Publishing previews

## 釋出預覽版本

Previews can be useful when **all** of the following are true:

在以下條件都滿足時，預覽版是非常有用的版本：

* The next stable version of the package is complete.

  下一個穩定版本是完整功能的版本。

* That package version depends on an API or feature in the Dart SDK that
  hasn't yet been released in a stable version of the Dart SDK.

  在最新的穩定版 Dart SDK 中，沒有釋出目前版本使用的 API。

* You know that the API or feature that the package depends on is
  API-stable and won't change before it reaches the stable SDK.

  你的 package 所依賴的 API 或功能在釋出到穩定版 SDK 前不會再改變。

As an example, consider a new version of `package:args` that has
a finished version `2.0.0` but that
depends on a feature in Dart `2.12.0-259.8.beta`,
where Dart SDK version `2.12.0` stable hasn't been released yet.
The pubspec might look like this:

舉個例子，假設 `package:args` 的 `2.0.0` 版本是已經完成的版本，
但它依賴了 Dart `2.12.0-259.8.beta` 的功能，這時 `2.12.0` 的 SDK 尚未釋出。
它的 pubspec 如下：

```
name: args
version: 2.0.0

environment:
  sdk: '>=2.12.0-259.8.beta <3.0.0'
```

When this package is published to pub.dev,
it's tagged as a preview version,
as illustrated by the following screenshot,
where the stable version is listed as
`1.6.0` and the preview version is listed as `2.0.0`.

當這個 package 釋出到 pub.dev 上時，會被標記為預覽版，如下圖所示，
`1.6.0` 是正式版而 `2.0.0` 是預覽版。

![Illustration of a preview version](preview-version.png){:width="600px"}<br>

When Dart `2.12.0` stable is released,
pub.dev updates the package listing to display
`2.0.0` as the stable version of the package.

當 `2.12.0` 的穩定版 SDK 釋出後，pub.dev 會更新 package 列表，
此時 `2.0.0` 會顯示為穩定版本。

If all of the conditions at the beginning of this section are true,
then you can ignore the following warning from `dart pub publish`:

如果上面的所有條件都滿足，那麼你可以在執行 `dart pub publish` 時忽略以下的警告：

   *"Packages with an SDK constraint on a pre-release of the Dart SDK should
   themselves be published as a pre-release version. If this package needs Dart
   version 2.12.0-0, consider publishing the package as a pre-release
   instead."*


## Retracting a package version {#retract}

## 撤回 package 的某個版本 {#retract}

To prevent new package consumers from adopting a recently
published version of your package, you can retract that package version
within 7 days of publication.
The retracted version can be restored again within 7 days of retraction.

出於某些特殊情況，若你需要一個新版本被使用者使用，
你可以在釋出後 7 天內撤回該軟體包版本。
撤回的版本可以在撤回後的 7 天內再次恢復。

A retracted package version isn't deleted. It appears in the version
listing of the package on pub.dev in the **Retracted versions** section. Also, the
detailed view of that package version has a **RETRACTED** badge.

被撤回的版本不會被刪除，它會顯示在 pub.dev 上的 **Retracted versions** 區域。
同時，對應版本的詳細資訊內會有一個 **RETRACTED** 標籤。

Before retracting a package,
consider publishing a new version instead.
Retracting a package causes churn and can have a negative impact on package users.

在撤回版本之前，你可以考慮直接釋出一個新版本。
撤回版本可能會對使用者造成混亂和負面的使用體驗。

If you accidentally publish a new version with either
a _missing dependency constraint_
or a _dependency constraint that is too lax_, 
then retracting the package version might be the only solution.
Publishing a newer version of your package is
insufficient to stop the version solver from picking the old version,
which might be the only version pub can choose.
By retracting the package version that has
incorrect dependency constraints, you force users to either
upgrade other dependencies or get a dependency conflict.

如果你不小心釋出了 **未有效限制依賴版本** 的新版本，
那麼撤回可能是唯一的選擇。
釋出新版本對於這樣的情況來說是無效的，因為 pub 依然能解析到新版。
撤回未有效限制依賴版本的版本，可以讓使用者在嘗試依賴時報錯，或者升級到更新的版本。

However, if your package merely contains a minor bug,
then retraction is probably not necessary.
Publishing a newer version with the bug fixed and a
description of the fixed bug in `CHANGELOG.md`
helps users to understand what happened.
And publishing a newer version is less disruptive to package users.

然而，如果你的新版本僅僅是包含了一個小 bug，便無需撤回版本。
釋出一個修復了 bug 的新版，並且在 `CHANGELOG.md` 中標註內容，
可以幫助使用者瞭解到問題所在。
釋出新版也會讓使用者的使用體驗更好。

{{site.alert.version-note}}

  Package retraction was introduced in Dart 2.15.
  In pre-2.15 SDKs, the pub version solver ignores the retracted status.

  版本撤回在 Dart 2.15 中引入。
  在早於 2.15 的 SDK 中，pub 版本解析會忽略版本的撤回狀態。

{{site.alert.end}}

### How to use a retracted package version

### 如何使用已撤回的版本

If a package depends on a package version that later is retracted,
it can still use that version as long as that version is in
the dependent package's `pubspec.lock` file.
To depend on a specific version that's already retracted,
the dependent package must pin the version in the
`dependency_overrides` section of the `pubspec.yaml` file.

如果一個 package 的對應版本已被撤回，在 `pubspec.lock` 標明它被依賴時仍然能被使用。
如果你想依賴某個撤回的版本，你可以在 `pubspec.yaml` 檔案中的
`dependency_overrides` 部分固定對應版本的使用。

### How to retract or restore a package version

### 如何撤回或恢復 package 的某個版本

To retract or restore a package version,
first sign in to pub.dev using a Google Account
that's either an uploader or a [verified publisher][] admin for the package.
Then go to the package's **Admin** tab,
where you can retract or restore recent package versions.

想要撤回或恢復 package 的某個版本，首先你需要使用 Google 帳號登入到
pub.dev，該帳號需要是該 package 的上傳者或
[認證的釋出者][verified publisher] 管理。
接著進入到 package 頁面上的 **Admin** 標籤欄，進行撤回和恢復版本操作。

## Marking packages as discontinued {#discontinue}

## 把 package 標記為終止 {#discontinue}

Although packages always remain published, it can be useful to signal to
developers that a package is no longer being actively maintained.
For this, you can mark a package as **discontinued**.
A discontinued package remains published and viewable on pub.dev,
but it has a clear **DISCONTINUED** badge and
doesn't appear in pub.dev search results.

儘管 package 的釋出總是會被保留，在需要時，把它標記為不再活躍的維護將會對開發者有幫助。
為了達到這一點，你可以將一個 package 標記為 **終止**。
終止的 package 曾經的釋出依然留存在 pub.dev 上，並可以被看到，
但是它有一個清楚的 **終止** 徽章，而且不會出現在搜尋結果中。

To mark a package as discontinued, first sign in to pub.dev using a Google Account
that's either an uploader or a [verified publisher][] admin for the package.
Then go to the package's **Admin** tab,
where you can mark the package as discontinued.
If you change your mind, you can remove the discontinued mark at any time.

要把 package 標記為終止，首先你需要使用 Google 帳號登入到
pub.dev，該帳號需要是該 package 的上傳者或
[認證的釋出者][verified publisher] 管理。
接著進入到 package 頁面上的 **Admin** 標籤欄，在其中將其標記為終止。
如果你回心轉意想繼續維護，可在任意時候移除終止標記。


[Create a verified publisher]: {{site.pub}}/create-publisher
[BSD 3-clause license]: https://opensource.org/licenses/BSD-3-Clause
[Google Account]: https://support.google.com/accounts/answer/27441
[Markdown]: {{site.pub-pkg}}/markdown
[package layout conventions]: /tools/pub/package-layout
[policy]: {{site.pub}}/policy
[pub]: /guides/packages
[`dart pub publish`]: /tools/pub/cmd/pub-lish
[pubspec]: /tools/pub/pubspec
[semver]: https://semver.org/spec/v2.0.0-rc.1.html
[verified publisher]: /tools/pub/verified-publishers
[git-ignore-format]: https://git-scm.com/docs/gitignore#_pattern_format
[pubignore-when]: https://stackoverflow.com/a/69767697
