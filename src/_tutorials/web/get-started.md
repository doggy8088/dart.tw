---
title: "Get started: Web apps"
title: 起步：使用 Dart 開發 Web 應用
description: A guide to get you quickly writing web apps in Dart.
description: 幫助你快速入門使用 Dart 開發 Web 應用
js: [{url: 'https://dartpad.dev/inject_embed.dart.js', defer: true}]
---

Follow these steps to start using Dart to develop **web-only** apps.
If you want to write a **multi-platform** app, then
[try Flutter.]({{site.flutter}}/web)

下面的步驟將指引你使用 Dart 開發 web 應用。
如果你想編寫 **跨平臺** 應用，請
[嘗試 Flutter]({{site.flutter}}/web)。

Still here?
First you'll play with Dart in your browser, no download required.
Then you'll install Dart and build a small web app.

開始旅程？
你將先在瀏覽器中嘗試 Dart，無需下載。
之後你將會安裝 Dart 並建立一個小型 web 應用。

## 1. Play with a web app in DartPad

## 1. 在 DartPad 中開發 web 程式

With [DartPad][DartPad documentation]
you can experiment with the Dart language and APIs,
no download necessary.

透過 DartPad，
你不需要下載任何東西即可嘗試 Dart 程式語言和 API。

For example, here's an embedded DartPad that lets you play with
the code for a todo-list generator.
Click **Run** to run the app;
the UI output appears to the right of the code.
Try editing the source code—perhaps you'd like to add "horses"
to the list of pets.

例如，下面這個內嵌的 DartPad
可以讓你嘗試待辦列表產生器的程式碼。
點選 **Run** 執行程式；
輸出介面顯示在程式碼的右側。
嘗試編輯原始碼，
比如在 pets 列表中新增「horses」。

{{site.alert.note}}
  {% include dartpad-embedded-troubleshooting.md %}
{{site.alert.end}}

```dart:run-dartpad:mode-html:ga_id-play_with_a_web_app
{$ begin main.dart $}
import 'dart:html';

Iterable<String> thingsTodo() sync* {
  const actions = ['Walk', 'Wash', 'Feed'];
  const pets = ['cats', 'dogs'];

  for (final action in actions) {
    for (final pet in pets) {
      if (pet != 'cats' || action == 'Feed') {
        yield '$action the $pet';
      }
    }
  }
}

void addTodoItem(String item) {
  final listElement = LIElement()..text = item;
  todoList.children.add(listElement);
}

final UListElement todoList = querySelector('#todolist') as UListElement;

void main() {
  thingsTodo().forEach(addTodoItem);
}

{$ end main.dart $}
{$ begin index.html $}
<h2>A Simple To-Do List</h2>

<p>Things to do:</p>

<ul id="todolist">
</ul>
{$ end index.html $}
```

More information:

更多資訊：

* [DartPad documentation][]
  
  [DartPad 文件][DartPad documentation]

* [Dart language tour][]

  [Dart 語言概覽][Dart language tour]

* [Dart library tour][]

  [Dart 函式庫概覽][Dart library tour]

* [Tutorial introduction to using Dart for basic web programming][]

  [簡明 Dart web 開發指南][Tutorial introduction to using Dart for basic web programming]

## 2. Install Dart

## 2. 安裝 Dart

{% include get-sdk.md %}

## 3. Get CLI tools or an IDE (or both)

## 3. 獲取 CLI 工具或者 IDE（或兩者都有）

<i class="fas fa-terminal dark"></i>
If you like to use the command line, install [webdev][]:

如果你想使用命令列，請安裝 [webdev][]：

```terminal
$ dart pub global activate webdev
```

<i class="material-icons">web</i>
Although using an IDE is optional, we highly recommend using one.
For a list of available IDEs, see the
[overview of editors & debuggers][].

儘管使用 IDE 是可選的，但我們強烈建議使用 IDE。
有關可用的 IDE 列表，請檢視
[編輯器和偵錯程式的概覽][overview of editors & debuggers]。

## 4. Create a web app

## 4. 建立一個 web 應用

<i class="fas fa-terminal dark"></i>
To create a web app from the command line, use these commands:

透過在命令列輸入以下命令，建立一個 web 應用：

```terminal
$ dart create -t web-simple quickstart
```

<i class="material-icons">web</i>
To create the same web app from an IDE that has Dart integration,
create a project using the template named **Bare-bones Web App**.

透過已經整合 Dart 的 IDE 同樣可以建立一個 web 應用，
使用名為 **Bare-bones Web App** 的模版建立一個專案。

## 5. Run the app

## 5. 執行應用

<i class="fas fa-terminal dark"></i>
To run the app from the command line, use [webdev][] to build and serve the app:

要從命令列執行應用程式，可以使用 [webdev][] 建構和執行應用程式：

```terminal
$ cd quickstart
$ webdev serve
```

<i class="material-icons">web</i>
Or run the app from your IDE.

或者從 IDE 執行應用程式。

To view your app, use the Chrome browser
to visit the app's URL—for example, 
[localhost:8080](localhost:8080).

使用 Chrome 瀏覽器存取應用程式的 URL
即可檢視你的應用程式。
比如，[localhost:8080](localhost:8080)。

Whether you use an IDE or the command line,
[webdev serve][] builds and serves your app
using the Dart development compiler, [dartdevc][].
Startup is slowest the first time dartdevc builds and serves your app.
After that, assets are cached on disk and incremental builds are much faster.

無論使用 IDE 還是命令列，
[webdev serve][] 建構和執行你的程式
都是透過 Dart 開發編譯器 [dartdevc][]。
第一次建構和執行應用程式時，啟動速度很慢。
之後的建構會很快，因為資源會被快取在磁碟並且使用增量建構。

Once your app has compiled, the browser should display
"Your Dart app is running."

一旦應用程式編譯完成，
瀏覽器會顯示
「Your Dart app is running.」

![Launched bare-bones app](/assets/img/bare-bones-web-app.png){:width="500"}


## 6. Add custom code to the app

## 6. 新增自訂程式碼

Let's customize the app you just created.

讓我們自訂剛才建立的應用。

 1. Copy the `thingsTodo()` function from the DartPad above
    to the `web/main.dart` file.

    從 DartPad 將 `thingsTodo()` 函式複製到 `web/main.dart` 檔案中。

 2. Add the `newLI()` function (as shown below).
    It creates a new `LIElement` containing the specified `String`.

    新增 `newLI()` 函式（如下所示）。
    它接收 `String` 型別的引數
    並建立一個新的 `LIElement`。

    {% prettify dart tag=pre+code %}
    Iterable<String> thingsTodo() sync* { ... }

    [!LIElement newLI(String itemText) => LIElement()..text = itemText;!]

    void main() { ... }
    {% endprettify %}

 3. In the `main()` function, initialize the `output` element using
    `thingsTodo()`:

    在 `main()` 函式中，初始化 `output` 元素透過
    `thingsTodo()`：

    {% prettify dart tag=pre+code %}
    Iterable<String> thingsTodo() sync* { ... }

    LIElement newLI(String itemText) => LIElement()..text = itemText;

    void main() {
      querySelector('#output')?[!.children.addAll(thingsTodo().map(newLI));!]
    }
    {% endprettify %}

 4. Save your changes.

    儲存你的修改。

 5. The webdev tool automatically rebuilds your app.
    Refresh the app's browser window.
    Now your simple Dart app has a todo list!
    It should look something like this:<br>
    ![Running the revised app](/assets/img/bare-bones-todo.png){:width="500"}

    webdev 工具會自動重新建構你的應用程式。
    在瀏覽器重新整理應用。
    現在你簡易的 Dart 程式已經有了一個待辦列表！
    如下圖所示：<br>
    ![Running the revised app](/assets/img/bare-bones-todo.png){:width="500"}

 6. Optionally, improve the formatting by editing `web/styles.css`,
    then reload the app to check your changes.

    你可以選擇美化一下應用的樣式透過編輯 `web/styles.css`，
    然後重新載入應用程式來檢查你的改動。

    {% prettify css tag=pre+code %}
    #output {
      padding: 20px;
      [!text-align: left;!]
    }
    {% endprettify %}

## 7. Use Dart DevTools to inspect the app

## 7. 使用 Dart 開發者工具檢查應用

Use Dart DevTools to set breakpoints, view values and types,
and step through your app's Dart code.
For setup details and a walkthrough, see
[Debugging Dart Web Apps][].

使用 Dart 開發者工具設定斷點，
檢視值和型別，
逐行執行你的 Dart 程式碼。
有關設定細節和示範，請檢視
[除錯 Dart Web 應用][Debugging Dart Web Apps]。

{{site.alert.info}}

  **Feeling lost? Don't worry!** This was a whirlwind introduction to Dart and
  web programming that left out many details. For a gentler approach, try a
  [low-level HTML tutorial for Dart][].

  **感覺沒有理解? 別擔心!** 本文只是粗略的介紹了 Dart 和 web 程式設計，
  其中忽略了很多細節。
  更詳細的內容可以查閱
  [Dart 的初級 HTML 課程][low-level HTML tutorial for Dart]。

{{site.alert.end}}

## What next?

## 接下來做什麼？

Check out these resources:

檢索這些資源：

* Tutorials and codelabs for Dart
  * [Tutorials](/tutorials)
  * [Codelabs](/codelabs)
* Dart language, libraries, and conventions
  * [Sample code](/samples)
  * [Language tour](/guides/language/language-tour)
  * [Library tour](/guides/libraries/library-tour)
  * [Effective Dart](/guides/language/effective-dart)
* Tools & libraries
  * [Dart SDK](/tools/sdk)
  * [Dart tools](/tools)
  * [IDEs](/tools#ides-and-editors)
  * [Web libraries and packages](/web/libraries)

If you get stuck, find help at [Community and support.](/community)

如果你在這一步無法繼續進行，可以從 [社群和幫助](/community) 中查詢幫助。

[dartdevc]: /tools/dartdevc
[DartPad documentation]: /tools/dartpad
[Dart language tour]: /guides/language/language-tour
[Dart library tour]: /guides/libraries/library-tour
[Dart tools]: /tools
[Debugging Dart Web Apps]: /web/debugging
[low-level HTML tutorial for Dart]: /tutorials/web/low-level-html
[overview of editors & debuggers]: /tools#ides-and-editors
[Tutorial introduction to using Dart for basic web programming]: /tutorials/web/low-level-html/connect-dart-html
[webdev]: /tools/webdev
[webdev serve]: /tools/webdev#serve
