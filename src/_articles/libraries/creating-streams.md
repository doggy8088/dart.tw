---
title: Creating streams in Dart
title: 在 Dart 裡使用 Stream
description: A stream is a sequence of results; learn how to create your own.
description: Stream 是一個結果序列，本篇帶你學會如何建立自己的 Stream。
original-date: 2013-04-08
date: 2021-05-16
category: libraries
---

<style>
.comment {color:red;}
</style>

_Written by Lasse Nielsen <br>
April 2013 (updated May 2021)_

The dart:async library contains two types
that are important for many Dart APIs:
[Stream]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-async/Stream-class.html) and
[Future.]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-async/Future-class.html)
Where a Future represents the result of a single computation,
a stream is a _sequence_ of results.
You listen on a stream to get notified of the results
(both data and errors)
and of the stream shutting down.
You can also pause while listening or stop listening to the stream
before it is complete.

dart:async 庫中有兩個型別，它們對許多 Dart API 來說都非常重要：
[Stream]({{site.dart_api}}/{{site.data.pkg-vers.SDK.channel}}/dart-async/Stream-class.html) 
和 [Future]({{site.dart_api}}/{{site.data.pkg-vers.SDK.channel}}/dart-async/Future-class.html)。
Future 用於表示單個運算的結果，而 Stream 則表示多個結果的序列。
你可以監聽 Stream 以獲取其結果（包括資料和錯誤）或其關閉事件。
也可以在 Stream 完成前對其暫停或停止監聽。

But this article is not about _using_ streams.
It's about creating your own streams.
You can create streams in a few ways:

但是本篇文章並非闡述 **如何使用** Stream，
而是向你介紹如何建立 Stream。
你可以透過以下幾種方式建立 Stream。

* Transforming existing streams.

  轉換現有的 Stream。

* Creating a stream from scratch by using an `async*` function.

  使用 `async*` 函式建立 Stream。

* Creating a stream by using a `StreamController`.

  使用 `StreamController` 產生 Stream。

This article shows the code for each approach
and gives tips to help you implement your stream correctly.

本文將向你展示每種方式的程式碼並且會給你一些有用的提示，
這些提示可以幫助你正確建立 Stream。

For help on using streams, see
[Asynchronous Programming: Streams](/tutorials/language/streams).

可以查閱　[非同步程式設計：使用 Stream](/tutorials/language/streams) 
獲取更多關於 Stream 的資訊。

## Transforming an existing stream

## 轉換現有的 Stream

The common case for creating streams is that you already have a stream,
and you want to create a new stream based on the original stream's events.
For example you might have a stream of bytes that
you want to convert to a stream of strings by UTF-8 decoding the input.
The most general approach is to create a new stream that
waits for events on the original stream and then
outputs new events. Example:

我們在建立 Stream 時常見的情形是根據現有 Stream 的事件建立一個新的 Stream。
比如你已經有了一個可以提供位元組事件的 Stream，
然後你想將該 Stream 變為一個可以提供字串的 Stream，
並且該 Stream 中的字串還經過 UTF-8 編碼。
對於這種情況，常用的辦法是建立一個新的 Stream 去等待獲取原 Stream 的事件，
然後再將新 Stream 中的事件輸出。例如：

<?code-excerpt "misc/lib/articles/creating-streams/line_stream_generator.dart (split into lines)"?>
```dart
/// Splits a stream of consecutive strings into lines.
///
/// The input string is provided in smaller chunks through
/// the `source` stream.
Stream<String> lines(Stream<String> source) async* {
  // Stores any partial line from the previous chunk.
  var partial = '';
  // Wait until a new chunk is available, then process it.
  await for (final chunk in source) {
    var lines = chunk.split('\n');
    lines[0] = partial + lines[0]; // Prepend partial line.
    partial = lines.removeLast(); // Remove new partial line.
    for (final line in lines) {
      yield line; // Add lines to output stream.
    }
  }
  // Add final partial line to output stream, if any.
  if (partial.isNotEmpty) yield partial;
}
```

For many common transformations,
you can use `Stream`-supplied transforming methods
such as `map()`, `where()`, `expand()`, and `take()`.

你可以使用 `Stream` 類提供的轉換類方法，比如 
`map()`、`where()`、`expand()` 和 `take()` 來應對大多數常見的轉換需求。

For example, assume you have a stream, `counterStream`,
that emits an increasing counter every second.
Here's how it might be implemented:

例如，假設你有一個名為 `counterStream` 的 Stream，
用於每秒列印輸出一個自增的整數。其實現過程可能如下：

<?code-excerpt "misc/lib/articles/creating-streams/stream_controller.dart (basic usage)"?>
```dart
var counterStream =
    Stream<int>.periodic(const Duration(seconds: 1), (x) => x).take(15);
```

To quickly see the events, you can use code like this:

你可以使用下面的程式碼區塊速檢視事件：

<?code-excerpt "misc/lib/articles/creating-streams/stream_controller.dart (basic for each)"?>
```dart
counterStream.forEach(print); // Print an integer every second, 15 times.
```

To transform the stream events, you can invoke a transforming method
such as `map()` on the stream before listening to it.
The method returns a new stream.

你可以在監聽 Stream 前呼叫一個類別似 `map()` 
的轉換方法來轉換 Stream 的事件。該方法將返回一個新的 Stream。

<?code-excerpt "misc/lib/articles/creating-streams/stream_controller.dart (use-map)"?>
```dart
// Double the integer in each event.
var doubleCounterStream = counterStream.map((int x) => x * 2);
doubleCounterStream.forEach(print);
```

Instead of `map()`, you could use any other transforming method,
such as the following:

你可以使用任意其它的變換方法替代 `map()`，
比如類似下面的這些：

<?code-excerpt "misc/lib/articles/creating-streams/stream_controller.dart (use-where)"?>
```dart
.where((int x) => x.isEven) // Retain only even integer events.
.expand((var x) => [x, x]) // Duplicate each event.
.take(5) // Stop after the first five events.
```

Often, a transforming method is all you need.
However, if you need even more control over the transformation,
you can specify a
[StreamTransformer]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-async/StreamTransformer-class.html)
with `Stream`’s `transform()` method.
The platform libraries provide stream transformers for many common tasks.
For example, the following code uses the `utf8.decoder` and `LineSplitter`
transformers provided by the dart:convert library.

通常而言，使用各種轉換方法足以滿足你簡單的使用需求。
但是，如果你需要對轉換進行更多的控制，
你可以使用 `Stream` 類別的 `transform()` 方法指定一個 
[StreamTransformer]({{site.dart_api}}/{{site.data.pkg-vers.SDK.channel}}/dart-async/StreamTransformer-class.html)。
Dart 平台庫為許多常見的任務需求提供了 Stream 轉換器。
例如下面的程式碼使用了由 dart:convert 庫提供的
`utf8.decoder` 和 `LineSplitter` 轉換器。

<?code-excerpt "misc/lib/articles/creating-streams/stream_controller.dart (use-transform)"?>
```dart
Stream<List<int>> content = File('someFile.txt').openRead();
List<String> lines = await content
    .transform(utf8.decoder)
    .transform(const LineSplitter())
    .toList();
```


## Creating a stream from scratch

## 從零開始建立 Stream

One way to create a new stream is with
an asynchronous generator (`async*`) function.
The stream is created when the function is called,
and the function's body starts running when the stream is listened to.
When the function returns, the stream closes.
Until the function returns, it can emit events on the stream by
using `yield` or `yield*` statements.

上一小節中我們使用一個現有的 Stream 經過轉換產生新的 Stream。
這一小節我們透過非同步產生器 (`async*`) 函式來完完全全地建立一個 Stream。
當非同步產生器函式被呼叫時會建立一個 Stream，
而函式體則會在該 Stream 被監聽時開始執行。
當函式返回時，Stream 關閉。在函式返回前，
你可以使用 `yield` 或 `yield*` 陳述式向該 Stream 提交事件。

Here's a primitive example that emits numbers at regular intervals:

下面是一個週期性傳送整數的函式例子：

<?code-excerpt "misc/lib/articles/creating-streams/stream_controller.dart (async-generator)" replace="/timedCounterGenerator/timedCounter/g"?>
```dart
Stream<int> timedCounter(Duration interval, [int? maxCount]) async* {
  int i = 0;
  while (true) {
    await Future.delayed(interval);
    yield i++;
    if (i == maxCount) break;
  }
}
```

{% comment %}
[PENDING: show code that uses it, so we have some context for
the mention of StreamSubscription?]
{% endcomment %}

This function returns a `Stream`.
When that stream is listened to, the body starts running.
It repeatedly delays for the requested interval and then yields the next number.
If the `maxCount` parameter is omitted, there is no stop condition on the loop,
so the stream outputs increasingly larger numbers forever -
or until the listener cancels its subscription.

該函式返回一個 `Stream`。而函式體會在該 Stream 被監聽時開始執行
且以一定的週期間隔在指定的數字範圍內不斷地產生一個遞增數字。
如果省略掉 `count` 引數，那麼迴圈將無休止地執行下去，
此時除非取消訂閱，否則 Stream 會不停地產生越來越多的數字。

When the listener cancels
(by invoking `cancel()` on the `StreamSubscription`
object returned by the `listen()` method),
then the next time the body reaches a `yield` statement,
the `yield` instead acts as a `return` statement.
Any enclosing `finally` block is executed,
and the function exits.
If the function attempts to yield a value before exiting,
that fails and acts as a return.

當監聽器取消時（呼叫由 `listen()` 方法返回的 `StreamSubscription` 
物件中的 `cancel()` 方法），如果下一次迴圈體執行到 `yield` 陳述式，
此時該陳述式的作用類似於 `return` 陳述式。而且任意 `finally`
陳述式塊在此時執行均會導致函式退出。如果函式嘗試在退出前 yield 一個值，
那麼該嘗試將會以失敗告終併產生類似於 return 陳述式的效果。

When the function finally exits, the future returned by
the `cancel()` method completes.
If the function exits with an error, the future completes with that error;
otherwise, it completes with `null`.

當函式最終退出時，由 `cancel()` 方法返回的 Future 完成。
如果函式是因為出錯導致退出，
則 Future 完成時會攜帶對應的錯誤，否則其會攜帶一個 `null` 。

Another, more useful example is a function that converts
a sequence of futures to a stream:

另外，一個更有用的範例是將一個 Future 序列轉換為 Stream 的函式：

<?code-excerpt "misc/lib/articles/creating-streams/stream_controller.dart (stream-from-futures)"?>
```dart
Stream<T> streamFromFutures<T>(Iterable<Future<T>> futures) async* {
  for (final future in futures) {
    var result = await future;
    yield result;
  }
}
```

This function asks the `futures` iterable for a new future,
waits for that future, emits the resulting value, and then loops.
If a future completes with an error, then the stream completes with that error.

該函式迴圈向 `Future` 序列請求一個 Future 並等待該 Future 完成獲取其結果後
提交給 Stream。如果某個 Future 因出錯完成，則該錯誤也會提交給 Stream。

It's rare to have an `async*` function building a stream from nothing.
It needs to get its data from somewhere,
and most often that somewhere is another stream.
In some cases, like the sequence of futures above,
the data comes from other asynchronous event sources.
In many cases, however, an `async*` function is too simplistic to
easily handle multiple data sources.
That's where the `StreamController` class comes in.

在實際應用中，透過 `async*` 函式從零建構 Stream 的情況比較少見。
`async*` 函式通常會根據某些資料源來建立 Stream，
而這些資料源常常又是另一個 Stream。
比如像上述範例中的 Future 序列，其資料往往來自於其它的非同步事件源。
然而，在許多情況下，非同步函式過於簡單難以輕鬆地處理多個數據源的場景。
而這就是 `StreamController` 類別的用武之地。


## Using a StreamController

## 使用 StreamController

If the events of your stream comes from different parts of your program,
and not just from a stream or futures that can traversed by an `async` function,
then use a
[StreamController]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-async/StreamController-class.html)
to create and populate the stream.

如果你 Stream 的事件不僅來自於非同步函式可以遍歷的 Stream 和 Future，
還來自於你程式的不同部分，這種情況使用上述兩種方式產生 Stream 就顯得比較困難。
面對這種情況，我們可以使用一個
[StreamController]({{site.dart_api}}/{{site.data.pkg-vers.SDK.channel}}/dart-async/StreamController-class.html)
來建立和填充 Stream。

A `StreamController` gives you a new stream
and a way to add events to the stream at any point, and from anywhere.
The stream has all the logic necessary to handle listeners and pausing.
You return the stream and keep the controller to yourself.

`StreamController` 可以為你產生一個 Stream，
並提供在任何時候、任何地方將事件新增到該 Stream 的方法。
該 Stream 具有處理監聽器和暫停所需的所有邏輯。
控制器物件你可以自行處理而只需返回呼叫者所需的 Stream 即可。

The following example
(from [stream_controller_bad.dart][])
shows a basic, though flawed, usage of `StreamController`
to implement the `timedCounter()` function from the previous examples.
This code creates a stream to return,
and then feeds data into it based on timer events,
which are neither futures nor stream events.

下面的程式碼將為你展示一個簡單的範例
（出自 [stream_controller_bad.dart][]），
該範例使用 `StreamController` 來實現上一個範例中的 `timedCounter()` 函式。
儘管該範例有一定的缺陷，但其為你展示了 `StreamController` 的基本用法。
該程式碼將資料直接新增至 `StreamController` 而不是從 Future 或 Stream 中獲取，
並在最後返回 `StreamController` 中的 Stream。

[stream_controller_bad.dart]: https://github.com/dart-lang/site-www/blob/main/examples/misc/lib/articles/creating-streams/stream_controller_bad.dart

{:.bad}
<?code-excerpt "misc/lib/articles/creating-streams/stream_controller_bad.dart (flawed stream)"?>
{% prettify dart tag=pre+code %}
// NOTE: This implementation is FLAWED!
// It starts before it has subscribers, and it doesn't implement pause.
Stream<int> timedCounter(Duration interval, [int? maxCount]) {
  var controller = StreamController<int>();
  int counter = 0;
  void tick(Timer timer) {
    counter++;
    controller.add(counter); // Ask stream to send counter values as event.
    if (maxCount != null && counter >= maxCount) {
      timer.cancel();
      controller.close(); // Ask stream to shut down and tell listeners.
    }
  }

  Timer.periodic(interval, tick); // BAD: Starts before it has subscribers.
  return controller.stream;
}
{% endprettify %}

As before, you can use the stream returned by `timedCounter()` like this:

與前面一樣，你可以像下面這樣使用由 `timedCounter()` 函式返回的 Stream：

{% comment %}
**[PENDING: Did we show this before?]**
{% endcomment %}

<?code-excerpt "misc/lib/articles/creating-streams/stream_controller_bad.dart (using stream)"?>
```dart
var counterStream = timedCounter(const Duration(seconds: 1), 15);
counterStream.listen(print); // Print an integer every second, 15 times.
```

This implementation of `timedCounter()` has
a couple of problems:

`timedCounter()` 函式的實現有兩個問題：

* It starts producing events before it has subscribers.

  它在擁有訂閱者之前就開始產生事件。

* It keeps producing events even if the subscriber requests a pause.

  即使訂閱者請求暫停，它也會繼續產生事件。

As the next sections show,
you can fix both of these problems by specifying
callbacks such as `onListen` and `onPause`
when creating the `StreamController`.

如下一節所示，你可以在建立 `StreamController` 時透過指定回呼(Callback)，
比如 `onListen` 和 `onPause` 來修復這些問題。


### Waiting for a subscription

### 等待訂閱

As a rule, streams should wait for subscribers before starting their work.
An `async*` function does this automatically,
but when using a `StreamController`,
you are in full control and can add events even when you shouldn't.
When a stream has no subscriber,
its `StreamController` buffers events,
which can lead to a memory leak
if the stream never gets a subscriber.

一般來說，Stream 應該在它產生事件前等待訂閱者，否則事件的產生毫無意義。
對 `async*` 函式而言，它可以自行處理該問題。但是當使用 `StreamController` 時，
因為你可以有比使用 `async*` 函式更多的控制能力，
因此你完全可以無視相關規則自行新增並控制事件。
如果一個 Stream 沒有訂閱者，它的 `StreamController` 會不斷快取事件，
這可能會導致記憶體洩露。

Try changing the code that uses the stream to the following:

將上面範例中使用 Stream 的程式碼更改為如下：

<?code-excerpt "misc/lib/articles/creating-streams/stream_controller_bad.dart (pre-subscribe problem)"?>
```dart
void listenAfterDelay() async {
  var counterStream = timedCounter(const Duration(seconds: 1), 15);
  await Future.delayed(const Duration(seconds: 5));

  // After 5 seconds, add a listener.
  await for (final n in counterStream) {
    print(n); // Print an integer every second, 15 times.
  }
}
```

When this code runs,
nothing is printed for the first 5 seconds,
although the stream is doing work.
Then the listener is added,
and the first 5 or so events are printed all at once,
since they were buffered by the `StreamController`.

當我們執行上述程式碼時，儘管 Stream 一開始就工作，
但最開始的 5 秒內不會有任何東西列印輸出。
5 秒後我們向 Stream 新增監聽器，
此時前面的 5 個事件會被同時輸出，
因為它們被 `StreamController` 快取了。

To be notified of subscriptions, specify an
`onListen` argument when you create the `StreamController`.
The `onListen` callback is called
when the stream gets its first subscriber.
If you specify an `onCancel` callback,
it's called when the controller loses its last subscriber.
In the preceding example,
`Timer.periodic()`
should move to an `onListen` handler,
as shown in the next section.

當你建構 `StreamController` 時，
可以為其指定一個 `onListen` 引數回呼(Callback)用以接收訂閱通知。
當 Stream 獲取到它的第一個訂閱者時會觸發呼叫 `onListen` 回呼(Callback)。
同樣地，你也可以指定一個 `onCancel` 回呼(Callback)，
該回調則會在控制器丟失它最後一個訂閱者時觸發呼叫。
在上述例子中，
`Timer.periodic()` 的呼叫應該移至 `onListen` 中進行，如下一節所示。


### Honoring the pause state

### 遵循並實現暫停

Avoid producing events when the listener has requested a pause.
An `async*` function automatically pauses at a `yield` statement
while the stream subscription is paused.
A `StreamController`, on the other hand, buffers events during the pause.
If the code providing the events doesn't respect the pause,
the size of the buffer can grow indefinitely.
Also, if the listener stops listening soon after pausing,
then the work spent creating the buffer is wasted.

當監聽器請求暫停時應當避免繼續產生事件。
當 Stream 訂閱暫停時，async* 函式可以自動地在一個 `yield` 陳述式執行時暫停。
而 `StreamController` 則會在暫停時快取事件。
如果程式碼在處理事件產生時不考慮暫停功能，則快取的大小可以無限制地增長。
而且如果在暫停後監聽器很快又請求停止，
那麼在暫停到停止這段時間內所做的快取工作都是浪費的。

To see what happens without pause support,
try changing the code that uses the stream to the following:

為了可以檢視在不支援暫停的時候會發生什麼，
我們將上面使用 Stream 的程式碼更改為如下：

<?code-excerpt "misc/lib/articles/creating-streams/stream_controller_bad.dart (pause problem)"?>
```dart
void listenWithPause() {
  var counterStream = timedCounter(const Duration(seconds: 1), 15);
  late StreamSubscription<int> subscription;

  subscription = counterStream.listen((int counter) {
    print(counter); // Print an integer every second.
    if (counter == 5) {
      // After 5 ticks, pause for five seconds, then resume.
      subscription.pause(Future.delayed(const Duration(seconds: 5)));
    }
  });
}
```

When the five seconds of pause are up,
the events fired during that time are all received at once.
That happens because the stream's source doesn't honor pauses
and keeps adding events to the stream.
So the stream buffers the events,
and it then empties its buffer when the stream becomes unpaused.

當五秒鐘的暫停時間結束時，在此期間產生的事件將同時被輸出。
出現這種狀況的原因是因為產生 Stream 的源沒有遵循暫停規則，
因此其會持續不斷地向向 Stream 中新增事件。
進而導致 Stream 快取事件，
然後，當 Stream 從暫停中恢復時，它會清空並輸出其快取。

The following version of `timedCounter()`
(from [stream_controller.dart][])
implements pause by using the
`onListen`, `onPause`, `onResume`, and `onCancel` callbacks
on the `StreamController`.

下面程式碼所實現的 `timedCounter()` 版本
（出自 [stream_controller.dart][]）
透過使用 `StreamController` 中的
`onListen`、`onPause`、`onResume` 和 `onCancel` 回呼(Callback)實現暫停功能。

[stream_controller.dart]: https://github.com/dart-lang/site-www/blob/main/examples/misc/lib/articles/creating-streams/stream_controller.dart

<?code-excerpt "misc/lib/articles/creating-streams/stream_controller.dart (better stream)"?>
```dart
Stream<int> timedCounter(Duration interval, [int? maxCount]) {
  late StreamController<int> controller;
  Timer? timer;
  int counter = 0;

  void tick(_) {
    counter++;
    controller.add(counter); // Ask stream to send counter values as event.
    if (counter == maxCount) {
      timer?.cancel();
      controller.close(); // Ask stream to shut down and tell listeners.
    }
  }

  void startTimer() {
    timer = Timer.periodic(interval, tick);
  }

  void stopTimer() {
    timer?.cancel();
    timer = null;
  }

  controller = StreamController<int>(
      onListen: startTimer,
      onPause: stopTimer,
      onResume: startTimer,
      onCancel: stopTimer);

  return controller.stream;
}
```

Run this code with the `listenWithPause()` function above.
You'll see that it stops counting while paused,
and it resumes nicely afterwards.

在 `listenWithPause()` 函式中使用上面的這個 `timedCounter` 函式，
執行後你就可以看到當訂閱暫停時列印輸出的計數也會暫停，
爾後又可以正確地恢復。

You must use all of the listeners—`onListen`,
`onCancel`, `onPause`, and `onResume`—to be
notified of changes in pause state.
The reason is that if the
subscription and pause states both change at the same time,
only the `onListen` or `onCancel` callback is called.

你必須使用全部的回呼(Callback) `onListen`、`onCancel`、`onPause` 和 `onResume`
來通知暫停狀態的變化，否則如果訂閱狀態與暫停狀態在同一時間都改變了，
只會有 `onListen` 或 `onCancel` 回呼(Callback)會被呼叫。


## Final hints

## 最後的提示

When creating a stream without using an async* function,
keep these tips in mind:

當你不透過 async* 函式建立 Stream 時，請務必牢記以下幾點：

* Be careful when using a synchronous controller—for example,
  one created using `StreamController(sync: true)`.
  When you send an event on an unpaused synchronous controller
  (for example, using the `add()`, `addError()`, or `close()` methods defined by
  [EventSink]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-async/EventSink-class.html)),
  the event is sent immediately to all listeners on the stream.
  `Stream` listeners must never be called until
  the code that added the listener has fully returned,
  and using a synchronous controller at the wrong time can
  break this promise and cause good code to fail.
  Avoid using synchronous controllers.

  使用同步控制器時要小心。
  例如，使用 `StreamController(sync: true)` 構造方法建立控制器。
  當你傳送一個事件到一個未暫停的同步控制器
  （例如：使用 [EventSink]({{site.dart_api}}/{{site.data.pkg-vers.SDK.channel}}/dart-async/EventSink-class.html) 
  中定義的 `add()`、`addError()` 或 `close()` 方法），
  事件立即傳送給所有 Stream 的監聽器。
  在新增監聽器的程式碼返回之前，決不能呼叫 `Stream` 監聽器，
  而在錯誤的事件使用同步控制器會破壞該規則並導致其它正常程式碼執行失敗。
  因此，你應該避免使用同步控制器。

* If you use `StreamController`,
  the `onListen` callback is called before
  the `listen` call returns the `StreamSubscription`.
  Don't let the `onListen` callback depend
  on the subscription already existing.
  For example, in the following code,
  an `onListen` event fires
  (and `handler` is called)
  before the `subscription` variable
  has a valid value.

  如果你使用 `StreamController`，
  `onListen` 回呼(Callback)會在 `listen` 方法呼叫返回 `StreamSubscription` 前返回。
  不要讓 `onListen` 回呼(Callback)依賴於已經存在的訂閱。
  例如，在下面的程式碼中，`onListen` 回呼(Callback)有可能會在 `subscription`
  變數被初始化為一個有效值之前被觸發（同時 `處理器` 被呼叫）。

  <?code-excerpt "misc/lib/articles/creating-streams/stream_controller.dart (stream-listen-hint)"?>
  ```dart
  subscription = stream.listen(handler);
  ```

* The `onListen`, `onPause`, `onResume`, and `onCancel`
  callbacks defined by `StreamController` are
  called by the stream when the stream's listener state changes,
  but never during the firing of an event
  or during the call of another state change handler.
  In those cases, the state change callback is delayed until
  the previous callback is complete.

  當 Stream 的監聽器狀態改變時，
  由 `StreamController` 定義的
  `onListen`、`onPause`、`onResume` 和 `onCancel` 回呼(Callback)會被呼叫，
  該呼叫絕不會發生在事件產生時或在某個狀態變化處理回呼(Callback)的呼叫期間。
  在這些情況出現時，狀態變化的回呼(Callback)會被延遲，直到上一個回呼(Callback)執行完成。

* Don't try to implement the `Stream` interface yourself.
  It's easy to get the interaction between events, callbacks,
  and adding and removing listeners subtly wrong.
  Always use an existing stream, possibly from a `StreamController`,
  to implement the `listen` call of a new stream.

  不要嘗試自己去實現 `Stream` 介面。
  否則很容易在事件、回呼(Callback)以及新增和移除監聽器這些操作互動時出現一些難以察覺的錯誤。
  你應該總是使用一個現有的 Stream（比如由 `StreamController` 產生的）
  去實現新 Stream 中 `listen` 方法的呼叫。

* Although it's possible to create classes that extend `Stream` with
  more functionality by extending the `Stream` class and
  implementing the `listen` method and the extra functionality on top,
  that is generally not recommended because
  it introduces a new type that users have to consider.
  Instead of a class that _is_ a `Stream` (and more), 
  you can often make a class that _has_ a `Stream` (and more).

  儘管你可以透過擴充 `Stream` 類並實現 `listen` 方法來實現更多額外的功能，
  但一般不建議這麼做，因為這樣會引入一個呼叫者必須考慮的新型別。
  相反，你可以建立一個（或多個）具有 `Stream` 的類而不是一個（或多個）Stream。

{% comment %}
The tests for this article are at /src/tests/site/articles/creating-streams.
{% endcomment %}
