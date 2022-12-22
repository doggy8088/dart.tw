---
title: Concurrency in Dart
title: Dart 中的併發
description: Use isolates to enable parallel code execution on multiple processor cores.
description: 使用 isolates 在多核裝置上併發執行程式碼。
---

<?code-excerpt path-base="concurrency"?>

<style>
  img {
    padding: 15px 0;
  }
}

</style>

Dart supports concurrent programming with async-await, isolates, and
classes such as `Future` and `Stream`.
This page gives an overview of async-await, `Future`, and `Stream`,
but it's mostly about isolates.

Dart 透過 async-await、isolate 以及一些非同步型別概念
（例如 `Future` 和 `Stream`）支援了併發程式碼程式設計。
本篇文章會對 async-await、`Future` 和 `Stream`
進行簡略的介紹，而側重點放在 isolate 的講解上。

Within an app, all Dart code runs in an _isolate._
Each Dart isolate has a single thread of execution and
shares no mutable objects with other isolates.
To communicate with each other,
isolates use message passing.
Although Dart's isolate model is built with underlying primitives
such as processes and threads
that the operating system provides,
the Dart VM's use of these primitives
is an implementation detail that this page doesn't discuss.

在應用中，所有的 Dart 程式碼都在 **isolate** 中執行。
每一個 Dart 的 isolate 都有獨立的執行執行緒，它們無法與其他 isolate 共享可變物件。
在需要進行通訊的場景裡，isolate 會使用訊息機制。
儘管 Dart 的 isolate 模型設計是基於作業系統提供的處理序和執行緒等更為底層的原語進行設計的，
但在本篇文章中，我們不對其具體實現展開討論。

Many Dart apps use only one isolate (the _main isolate_),
but you can create additional isolates,
enabling parallel code execution on multiple processor cores.

大部分 Dart 應用只會使用一個 isolate（即 **主 isolate**），
同時你也可以建立更多的 isolate，從而在多個處理器核心上達成並行執行程式碼的目的。

{{site.alert.info}}

  **Platform note:**
  All apps can use async-await, `Future`, and `Stream`.
  Isolates are implemented only on the [Dart Native platform][];
  Dart web apps can use [web workers][] for similar functionality.

  **多平臺使用時注意：**
  所有的 Dart 應用都可以使用 async-await、`Future` 和 `Stream`。
  而 isolate 僅針對 [原生平台的使用][Dart Native platform] 進行實現。
  使用 Dart 建構的網頁應用可以 [使用 Web Workers][] 實現相似的功能。

{{site.alert.end}}

[Dart Native platform]: /overview#platform
[web workers]: https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Using_web_workers
[使用 Web Workers]: https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Workers_API/Using_web_workers


## Asynchrony types and syntax

## 非同步的型別和語法

If you’re already familiar with `Future`, `Stream`, and async-await,
then you can skip ahead to the [isolates section][].

如果你已經對 `Future`、`Stream` 和 async-await 比較熟悉了，
可以直接跳到 [isolate 部分][isolates section] 進行閱讀。

[isolates section]: #how-isolates-work


### Future and Stream types

### Future 和 Stream 型別

The Dart language and libraries use `Future` and `Stream` objects to
represent values to be provided in the future.
For example, a promise to eventually provide an `int` value
is typed as `Future<int>`.
A promise to provide a series of `int` values
has the type `Stream<int>`.

Dart 語言和庫透過 `Future` 和 `Stream` 物件，來提供會在當前呼叫的未來返回某些值的功能。
以 JavaScript 中的 Promise 為例，
在 Dart 中一個最終會返回 `int` 型別值的 promise，應當宣告為 `Future<int>`；
一個會持續返回一系列 `int` 型別值的 promise，應當宣告為 `Stream<int>`。

As another example, consider the dart:io methods for reading files.
The synchronous `File` method [`readAsStringSync()`][]
reads a file synchronously,
blocking until the file is either fully read or an error occurs.
The method then either returns an object of type `String`
or throws an exception.
The asynchronous equivalent, [`readAsString()`][],
immediately returns an object of type `Future<String>`.
At some point in the future,
the `Future<String>` completes with either a string value or an error.

讓我們用 dart:io 來舉另外一個例子。`File` 的同步方法 [`readAsStringSync()`][]
會以同步呼叫的方式讀取檔案，在讀取完成或者丟擲錯誤前保持阻塞。
這個會返回 `String` 型別的物件，或者丟擲例外。
而與它等效的非同步方法 [`readAsString()`][]，會在呼叫時立刻返回
`Future<String>` 型別的物件。
在未來的某一刻，`Future<String>` 會結束，並返回一個字串或錯誤。

[`readAsStringSync()`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-io/File/readAsStringSync.html
[`readAsString()`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-io/File/readAsString.html

#### Why asynchronous code matters

#### 為什麼非同步的程式碼如此重要？

It matters whether a method is synchronous or asynchronous
because most apps need to do more than one thing at a time.

Asynchronous computations are often the result of performing computations
outside of the current Dart code; 
this includes computations that don't complete immediately, 
and where you aren't willing to block your Dart code waiting for the result.
For example, an app might start an HTTP request,
but need to update its display or respond to user input
before the HTTP request completes.
Asynchronous code helps apps stay responsive.

大部分應用需要在同一時刻做很多件事。
例如，應用可能會發起一個 HTTP 請求，
同時在請求返回前對使用者的操作做出不同的介面更新。
非同步的程式碼會有助於應用保持更高的可互動狀態。

These scenarios include operating system calls like
non-blocking I/O, performing an HTTP request, or communicating with a browser. 
Other scenarios include waiting for computations
performed in another Dart isolate as described below, 
or maybe just waiting for a timer to trigger. 
All of these processes either run in a different thread, 
or are handled by the operating system or the Dart runtime, 
which allows Dart code to run concurrently with the computation.

非同步場景包括呼叫系統 API，例如非阻塞的 I/O 操作、HTTP 請求或與瀏覽器互動。
還有一些場景是利用 Dart 的 isolate 進行計算，或等待一個計時器的觸發。
這些場景要麼是在不同的執行緒執行，要麼是被系統或 Dart 執行時處理，
讓 Dart 程式碼可以在計算時同步執行。

### The async-await syntax

### async-await 語法

The `async` and `await` keywords provide
a declarative way to define asynchronous functions
and use their results.

`async` 和 `await` 關鍵字是用宣告來定義非同步函式和獲取它們的結果的方式。

Here’s an example of some synchronous code
that blocks while waiting for file I/O:

下面是一段同步程式碼呼叫檔案 I/O 時阻塞的例子：

<?code-excerpt "lib/sync_number_of_keys.dart"?>
```dart
void main() {
  // Read some data.
  final fileData = _readFileSync();
  final jsonData = jsonDecode(fileData);

  // Use that data.
  print('Number of JSON keys: ${jsonData.length}');
}

String _readFileSync() {
  final file = File(filename);
  final contents = file.readAsStringSync();
  return contents.trim();
}
```

Here’s similar code, but with changes (highlighted) to make it asynchronous:

下面是類似的程式碼，但是變成了 **非同步呼叫**：

<?code-excerpt "lib/async_number_of_keys.dart" replace="/async|await|readAsString\(\)/[!$&!]/g; /Future<\w+\W/[!$&!]/g;"?>
{% prettify dart tag=pre+code %}
void main() [!async!] {
  // Read some data.
  final fileData = [!await!] _readFileAsync();
  final jsonData = jsonDecode(fileData);

  // Use that data.
  print('Number of JSON keys: ${jsonData.length}');
}

[!Future<String>!] _readFileAsync() [!async!] {
  final file = File(filename);
  final contents = [!await!] file.[!readAsString()!];
  return contents.trim();
}
{% endprettify %}

The `main()` function uses the `await` keyword in front of `_readFileAsync()`
to let other Dart code (such as event handlers) use the CPU
while native code (file I/O) executes.
Using `await` also has the effect of
converting the `Future<String>` returned by `_readFileAsync()` into a `String`.
As a result, the `contents` variable has the implicit type `String`.

`main()` 函式在呼叫 `_readFileAsync()` 前使用了 `await` 關鍵字，
讓原生程式碼（檔案 I/O）執行的同時，其他的 Dart 程式碼（例如事件處理器）能繼續執行。
使用 `await` 後，`_readFileAsync()` 呼叫返回的 `Future<String>` 型別也轉換為了 `String`。
從而在將結果 `content` 賦予變數時，隱含轉換為 `String` 型別。

{{site.alert.note}}

  The `await` keyword works only in functions that
  have `async` before the function body.

  `await` 關鍵字僅在函式體前定義了 `async` 的函式中有效。

{{site.alert.end}}

As the following figure shows,
the Dart code pauses while `readAsString()` executes non-Dart code,
in either the Dart virtual machine (VM) or the operating system (OS).
Once `readAsString()` returns a value, Dart code execution resumes.

如下圖所示，無論是在 Dart VM 還是在系統中，
Dart 程式碼都會在 `readAsString()` 執行非 Dart 程式碼時暫停。
在 `readAsString()` 返回值後，Dart 程式碼將繼續執行。

![Flowchart-like figure showing app code executing from start to exit, waiting for native I/O in between](/guides/language/concurrency/images/basics-await.png)

If you’d like to learn more about using `async`, `await`, and futures,
visit the [asynchronous programming codelab][].

如果你想了解更多關於 `async`、`await` 和 `Future` 的內容，可以存取
[非同步程式設計 codelab][asynchronous programming codelab] 進行學習。

[asynchronous programming codelab]: /codelabs/async-await


## How isolates work

## Isolate 的工作原理

Most modern devices have multi-core CPUs.
To take advantage of all those cores,
developers sometimes use shared-memory threads running concurrently.
However, shared-state concurrency is
[error prone](https://en.wikipedia.org/wiki/Race_condition#In_software) and
can lead to complicated code.

現代的裝置通常會使用多核 CPU。開發者為了讓程式在裝置上有更好的表現，
有時會使用共享內容的執行緒來併發執行程式碼。
然而，狀態的共享可能會 [產生競態條件，從而造成錯誤](https://baike.baidu.com/l/kex6qKvt)，
也可能會增加程式碼的複雜度。

Instead of threads, all Dart code runs inside of isolates.
Each isolate has its own memory heap,
ensuring that none of the state in an isolate is accessible from
any other isolate.
Because there’s no shared memory, you don’t have to worry about
[mutexes or locks](https://en.wikipedia.org/wiki/Lock_(computer_science)).

Dart 程式碼並不在多個執行緒上執行，取而代之的是它們會在 isolate 內執行。
每一個 isolate 會有自己的堆記憶體，從而確保 isolate 之間互相隔離，無法互相存取狀態。
由於這樣的實現並不會共享記憶體，所以你也不需要擔心
[互斥鎖和其他鎖](https://baike.baidu.com/l/My2bXiba)。

Using isolates, your Dart code can perform multiple independent tasks at once,
using additional processor cores if they’re available.
Isolates are like threads or processes,
but each isolate has its own memory and a single thread running an event loop.

在使用 isolate 時，你的 Dart 程式碼可以在同一時刻進行多個獨立的任務，並且使用可用的處理器核心。
Isolate 與執行緒和處理序近似，但是每個 isolate 都擁有獨立的記憶體，以及執行事件迴圈的獨立執行緒。

### The main isolate

### 主 isolate

You often don’t need to think about isolates at all.
A typical Dart app executes all its code in the app's main isolate,
as shown in the following figure:

在一般場景下，你完全無需關心 isolate。通常一個 Dart 應用會在主 isolate 下執行所有程式碼，
如下圖所示：

![A figure showing a main isolate, which runs `main()`, responds to events, and then exits](/guides/language/concurrency/images/basics-main-isolate.png)

Even single-isolate programs can execute smoothly
by using async-await to wait for asynchronous operations to complete
before continuing to the next line of code.
A well-behaved app starts quickly,
getting to the event loop as soon as possible.
The app then responds to each queued event promptly,
using asynchronous operations as necessary.

就算是隻有一個 isolate 的應用，只要透過使用 async-await 來處理非同步操作，也完全可以流暢執行。
一個擁有良好效能的應用，會在快速啟動後儘快進入事件迴圈。
這使得應用可以透過非同步操作快速響應對應的事件。

### The isolate life cycle

### Isolate 的生命週期

As the following figure shows,
every isolate starts by running some Dart code,
such as the `main()` function.
This Dart code might register some event listeners—to 
respond to user input or file I/O, for example.
When the isolate's initial function returns,
the isolate stays around if it needs to handle events.
After handling the events, the isolate exits.

如下圖所示，每個 isolate 都是從執行 Dart 程式碼開始的，比如 `main()` 函式。
執行的 Dart 程式碼可能會註冊一些事件監聽，例如處理使用者操作或檔案讀寫。
當 isolate 執行的 Dart 程式碼結束後，如果它還需要處理已監聽的事件，那麼它依舊會繼續被保持。
處理完所有事件後，isolate 會退出。

![A more general figure showing that any isolate runs some code, optionally responds to events, and then exits](/guides/language/concurrency/images/basics-isolate.png)


### Event handling

### 事件處理

In a client app, the main isolate’s event queue might contain
repaint requests and notifications of tap and other UI events.
For example, the following figure shows a repaint event,
followed by a tap event, followed by two repaint events.
The event loop takes events from the queue in first in, first out order.

在客戶端應用中，主 isolate 的事件佇列內，可能會包含重繪的請求、點選的通知或者其他介面事件。
例如，下圖展示了包含四個事件的事件佇列，佇列會按照先進先出的模式處理事件。

![A figure showing events being fed, one by one, into the event loop](/guides/language/concurrency/images/event-loop.png)

Event handling happens on the main isolate after `main()` exits.
In the following figure, after `main()` exits,
the main isolate handles the first repaint event.
After that, the main isolate handles the tap event,
followed by a repaint event.

如下圖所示，在 `main()` 方法執行完畢後，事件佇列中的處理才開始，此時處理的是第一個重繪的事件。
而後主 isolate 會處理點選事件，接著再處理另一個重繪事件。

![A figure showing the main isolate executing event handlers, one by one](/guides/language/concurrency/images/event-handling.png)

If a synchronous operation takes too much processing time,
the app can become unresponsive.
In the following figure, the tap-handling code takes too long,
so subsequent events are handled too late.
The app might appear to freeze,
and any animation it performs might be jerky.

如果某個同步執行的操作花費了很長的處理時間，應用看起來就像是失去了響應。
在下圖中，處理點選事件的程式碼比較耗時，導致緊隨其後的事件並沒有及時處理。
這時應用可能會產生卡頓，所有的動畫都無法流暢播放。

![A figure showing a tap handler with a too-long execution time](/guides/language/concurrency/images/event-jank.png)

In client apps, the result of a too-lengthy synchronous operation is often
[janky (non-smooth) UI animation][jank].
Worse, the UI might become completely unresponsive.

在一個客戶端應用中，耗時過長的同步操作，通常會導致 [卡頓的動畫][jank]。
而最糟糕的是，應用介面可能完全失去響應。

[jank]: {{site.flutter-docs}}/perf/rendering-performance


### Background workers

### 後臺執行物件

If your app’s UI becomes unresponsive due to 
a time-consuming computation—[parsing a large JSON file][json], 
for example—consider offloading that computation to a worker isolate,
often called a _background worker._
A common case, shown in the following figure,
is spawning a simple worker isolate that
performs a computation and then exits.
The worker isolate returns its result in a message when the worker exits.

如果你的應用受到耗時計算的影響而出現卡頓，例如 [解析較大的 JSON 檔案][json]，
你可以考慮將耗時計算轉移到單獨工作的 isolate，通常我們稱這樣的 isolate 為 **後臺執行物件**。
下圖展示了一種常用場景，你可以產生一個 isolate，它將執行耗時計算的任務，並在結束後退出。
這個 isolate 工作物件退出時會把結果返回。

[json]: {{site.flutter-docs}}/cookbook/networking/background-parsing

![A figure showing a main isolate and a simple worker isolate](/guides/language/concurrency/images/isolate-bg-worker.png)

Each isolate message can deliver one object,
which includes anything that’s transitively reachable from that object.
Not all object types are sendable, and
the send fails if any transitively reachable object is unsendable.
For example, you can send an object of type `List<Object>` only if
none of the objects in the list is unsendable.
If one of the objects is, say, a `Socket`, then
the send fails because sockets are unsendable.

每個 isolate 都可以透過訊息通訊傳遞一個物件，這個物件的所有內容都需要滿足可傳遞的條件。
並非所有的物件都滿足傳遞條件，在無法滿足條件時，訊息傳送會失敗。
舉個例子，如果你想傳送一個 `List<Object>`，你需要確保這個列表中所有元素都是可被傳遞的。
假設這個列表中有一個 `Socket`，由於它無法被傳遞，所以你無法傳送整個列表。

For information on the kinds of objects that you can send in messages,
see the API reference documentation for the [`send()` method][].

你可以查閱 [`send()` 方法][`send()` method] 的文件來確定哪些型別可以進行傳遞。

A worker isolate can perform I/O
(reading and writing files, for example), set timers, and more.
It has its own memory and
doesn’t share any state with the main isolate.
The worker isolate can block without affecting other isolates.

Isolate 工作物件可以進行 I/O 操作、設定定時器，以及其他各種行為。
它會持有自己記憶體空間，與主 isolate 互相隔離。
這個 isolate 在阻塞時也不會對其他 isolate 造成影響。

[`send()` method]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-isolate/SendPort/send.html


## Code examples

## 程式碼範例

This section discusses some examples
that use the `Isolate` API
to implement isolates.

本節將重點討論使用 `Isolate` API 實現 isolate 的一些範例。

{{site.alert.flutter-note}}

  If you're using Flutter on a non-web platform,
  then instead of using the `Isolate` API directly,
  consider using the [Flutter `compute()` function][].
  The `compute()` function is a simple way to
  move a single function call to a worker isolate.

  **Flutter 開發提示：**
  如果你在非 Web 平臺上使用 Flutter 進行開發，那麼與其直接使用 `Isolate` API，
  可以考慮使用 [Flutter 提供的 `compute()` 方法][Flutter `compute()` function]。
  `compute()` 方法能以簡單的方式將一個函式的呼叫封裝至 isolate 工作物件內。

{{site.alert.end}}

[Flutter `compute()` function]: {{site.flutter-docs}}/cookbook/networking/background-parsing#4-move-this-work-to-a-separate-isolate


### Implementing a simple worker isolate

### 實現一個簡單的 isolate 工作物件

This section shows the implementation for a
main isolate and the simple worker isolate that it spawns.
The worker isolate executes a function and then exits,
sending the main isolate a single message as it exits.
(The [Flutter `compute()` function][] works in a similar way.)

本節將展示一個主 isolate 與它產生的 isolate 工作物件的實現。
Isolate 工作物件會執行一個函式，完成後結束物件，並將函式結果傳送至主 isolate。
（[Flutter 提供的 `compute()` 方法][Flutter `compute()` function] 也是以類似的方式工作的。）

This example uses the following isolate-related API:

下面的範例將使用到這些與 isolate 相關的 API：

* [`Isolate.spawn()`][] and [`Isolate.exit()`][]

  [`Isolate.spawn()`][] 和 [`Isolate.exit()`][]

* [`ReceivePort`][] and [`SendPort`][]

  [`ReceivePort`][] 和 [`SendPort`][]

[`Isolate.exit()`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-isolate/Isolate/exit.html
[`Isolate.spawn()`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-isolate/Isolate/spawn.html
[`ReceivePort`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-isolate/ReceivePort-class.html
[`SendPort`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-isolate/SendPort-class.html

Here’s the code for the main isolate:

主 isolate 的程式碼如下：

<?code-excerpt "lib/simple_worker_isolate.dart (main)"?>
```dart
void main() async {
  // Read some data.
  final jsonData = await _parseInBackground();

  // Use that data
  print('Number of JSON keys: ${jsonData.length}');
}

// Spawns an isolate and waits for the first message
Future<Map<String, dynamic>> _parseInBackground() async {
  final p = ReceivePort();
  await Isolate.spawn(_readAndParseJson, p.sendPort);
  return await p.first as Map<String, dynamic>;
}
```

The `_parseInBackground()` function contains the code that
_spawns_ (creates and starts) the isolate for the background worker,
and then returns the result:

`_parseInBackground()` 方法包含了 **產生** 後臺 isolate 工作物件的程式碼，並返回結果：

1. Before spawning the isolate, the code creates a `ReceivePort`,
   which enables the worker isolate
   to send messages to the main isolate.

   在產生 isolate 之前，程式碼建立了一個 `ReceivePort`，
   讓 isolate 工作物件可以傳遞資訊至主 isolate。 

2. Next is the call to `Isolate.spawn()`,
   which creates and starts the isolate for the background worker.
   The first argument to `Isolate.spawn()` is the function that
   the worker isolate executes: `_readAndParseJson`.
   The second argument is the `SendPort`
   that the worker isolate can use to send messages to the main isolate.
   The code doesn't _create_ a `SendPort`;
   it uses the `sendPort` property of the `ReceivePort`.

   接下來是呼叫 `Isolate.spawn()`，產生並啟動一個在後台執行的 isolate 工作物件。
   該方法的第一個引數是 isolate 工作物件執行的函式參考：`_readAndParseJson`。
   第二個引數則是 isolate 用來與主 isolate 傳遞訊息的 `SendPort`。
   此處的程式碼並沒有 **建立** 新的 `SendPort`，
   而是直接使用了 `ReceivePort` 的 `sendPort` 屬性。

3. Once the isolate is spawned, the main isolate waits for the result.
   Because the `ReceivePort` class implements `Stream`,
   the [`first`][] property is an easy way to get
   the single message that the worker isolate sends.

   Isolate 初始化完成後，主 isolate 即開始等待它的結果。
   由於 `ReceivePort` 實現了 `Stream`，你可以很方便地使用
   [`first`][] 屬性獲得 isolate 工作物件返回的單個訊息。

[`first`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-async/Stream/first.html

The spawned isolate executes the following code:

初始化後的 isolate 會執行以下程式碼：

<?code-excerpt "lib/simple_worker_isolate.dart (spawned)"?>
```dart
Future<void> _readAndParseJson(SendPort p) async {
  final fileData = await File(filename).readAsString();
  final jsonData = jsonDecode(fileData);
  Isolate.exit(p, jsonData);
}
```

The relevant statement is the last one, which exits the isolate,
sending `jsonData` to the passed-in `SendPort`.
Message passing using `SendPort.send` normally involves data copying,
and thus can be slow.
However, when you send the data using `Isolate.exit()`,
then the memory that holds the message in the exiting isolate isn’t copied,
but instead is transferred to the receiving isolate.
The sender will nonetheless perform a verification pass to ensure
the objects are allowed to be transferred.

在最後一句程式碼後，isolate 會退出，
將 `jsonData` 透過傳入的 `SendPort` 傳送。
在 isolate 之間傳遞訊息時，通常會發生資料複製，
然而，當你使用 `Isolate.exit()` 傳送資料時，
isolate 中持有的訊息並沒有發生複製，
而是直接轉移到了接收的 isolate 中。

{{site.alert.version-note}}

  `Isolate.exit()` was added in 2.15.
  Previous releases support only explicit message passing,
  using `Isolate.send()` as shown in the next section's example.

  `Isolate.exit()` 在 Dart 2.15 中被引入。
  在先前的 Dart 版本中，僅支援透過 `Isolate.send()` 進行顯式的訊息傳遞，
  下一個小節的範例中將進行說明。

{{site.alert.end}}

The following figure illustrates the communication between
the main isolate and the worker isolate:

下圖說明了主 isolate 和 isolate 工作物件之間的通訊流程：

![A figure showing the previous snippets of code running in the main isolate and in the worker isolate](/guides/language/concurrency/images/isolate-api.png)


### Sending multiple messages between isolates

### 在 isolate 之間傳送多次訊息內容

If you need more communication between isolates,
then you need to use the [`send()` method][] of `SendPort`.
One common pattern, which the following figure shows,
is for the main isolate to send a request message to the worker isolate,
which then sends one or more reply messages.

如果你想在 isolate 之間建立更多的通訊，那麼你需要使用
`SendPort` 的 [`send()` 方法][`send()` method]。
下圖展示了一種常見的場景，主 isolate 會發送請求訊息至 isolate 工作物件，
然後它們之間會繼續進行多次通訊，進行請求和回覆。

![A figure showing the main isolate spawning the isolate and then sending a request message, which the worker isolate responds to with a reply message; two request-reply cycles are shown](/guides/language/concurrency/images/isolate-custom-bg-worker.png)

For examples of sending multiple messages,
see the following [isolate samples][]:

下方列舉的 [isolate 範例][isolate samples]
包含了傳送多次訊息的使用方法：

* [send_and_receive.dart][],
  which shows how to send a message from
  the main isolate to the spawned isolate.
  It’s otherwise similar to the preceding example.

  [send_and_receive.dart][] 展示瞭如何從主 isolate 傳送訊息至產生的 isolate。
  與前面的範例較為接近。

* [long_running_isolate.dart][],
  which shows how to spawn a long-running isolate that
  receives and sends multiple times.

  [long_running_isolate.dart][] 展示瞭如何產生一個長期執行、
  且多次傳送和接收訊息的 isolate。

{% assign samples = "https://github.com/dart-lang/samples/tree/master/isolates" %}

[isolate samples]: {{ samples }}
[send_and_receive.dart]: {{ samples }}/bin/send_and_receive.dart
[long_running_isolate.dart]: {{ samples }}/bin/long_running_isolate.dart


## Performance and isolate groups

## 效能和 isolate 組

When an isolate calls [`Isolate.spawn()`][],
the two isolates have the same executable code
and are in the same _isolate group_.
Isolate groups enable performance optimizations such as sharing code;
a new isolate immediately runs the code owned by the isolate group.
Also, `Isolate.exit()` works only when the isolates
are in the same isolate group.

當一個 isolate 呼叫了 [`Isolate.spawn()`][]，
兩個 isolate 將擁有同樣的執行程式碼，並歸入同一個 **isolate 組** 中。
Isolate 組會帶來效能最佳化，例如新的 isolate 會執行由 isolate 組持有的程式碼，即共享程式碼呼叫。
同時，`Isolate.exit()` 僅在對應的 isolate 屬於同一組時有效。

In some special cases,
you might need to use [`Isolate.spawnUri()`][],
which sets up the new isolate with a copy of the code
that's at the specified URI.
However, `spawnUri()` is much slower than `spawn()`,
and the new isolate isn't in its spawner's isolate group.
Another performance consequence is that message passing
is slower when isolates are in different groups.

某些場景下，你可能需要使用 [`Isolate.spawnUri()`][]，
使用執行的 URI 產生新的 isolate，並且包含程式碼的副本。
然而，`spawnUri()` 會比 `spawn()` 慢很多，
並且新產生的 isolate 會位於新的 isolate 組。
另外，當 isolate 在不同的組中，它們之間的訊息傳遞會變得更慢。

[`Isolate.spawnUri()`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-isolate/Isolate/spawnUri.html

{{site.alert.flutter-note}}

  Flutter doesn't support `Isolate.spawnUri()`.

  Flutter 不支援 `Isolate.spawnUri()`。

{{site.alert.end}}

{% comment %}
TODO:
* After publishing:
  * Figure out how to save an editable version of the source that has the right fonts. (The SVG files don't like the custom fonts; otherwise, I would've used SVGs instead of PNGs.)
* Maybe:
  * Add a new macro & style for flutter notes?
  * Add the following text somewhere in this page (or in the FAQ):
    * Sometimes Dart is called a _single-threaded language._
    * Dart code executes in a predictable sequence that can’t be interrupted by other Dart code.
  * Add a figure up high in the doc? (if so, what?)
{% endcomment %}
