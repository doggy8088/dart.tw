---
title: "Asynchronous programming: Streams"
title: 非同步程式設計：使用 stream
description: Learn how to consume single-subscriber and broadcast streams.
description: 瞭解如何使用 single-subscriber 和 broadcast streams。
js: [{url: 'https://dartpad.dev/inject_embed.dart.js', defer: true}]
---

<div class="mini-toc" markdown="1">
  <h4>What's the point?</h4>

  <h4>本章的重點</h4>

  * Streams provide an asynchronous sequence of data.

    Stream 提供一個非同步的資料序列。

  * Data sequences include user-generated events and data read from files.

    資料序列包括使用者產生的事件和從檔案讀取的資料。

  * You can process a stream using either **await for** or `listen()` from the Stream API.

    你可以使用 Stream API 中的 `listen()` 方法和 **await for** 關鍵字來處理一個 Stream。

  * Streams provide a way to respond to errors.

    當出現錯誤時，Stream 提供一種處理錯誤的方式。

  * There are two kinds of streams: single subscription or broadcast.

    Stream 有兩種型別：Single-Subscription 和 Broadcast。
</div>

Asynchronous programming in Dart is characterized by the
[Future][] and [Stream][] classes.

[Future][] 和 [Stream][] 類是 Dart 非同步程式設計的核心。

A Future represents a computation that doesn't complete immediately.
Where a normal function returns the result, an asynchronous function
returns a Future, which will eventually contain the result.
The future will tell you when the result is ready.

Future 表示一個不會立即完成的計算過程。與普通函式直接返回結果不同的是非同步函式返回一個將會包含結果的 Future。該 Future 會在結果準備好時通知呼叫者。

A stream is a sequence of asynchronous events.
It is like an asynchronous Iterable—where, instead of getting
the next event when you ask for it, the stream tells you that
there is an event when it is ready.

Stream 是一系列非同步事件的序列。其類似於一個非同步的 Iterable，不同的是當你向 Iterable 獲取下一個事件時它會立即給你，但是 Stream 則不會立即給你而是在它準備好時告訴你。

## Receiving stream events

## 接收 Stream 事件

Streams can be created in many ways, which is a topic for another
article, but they can all be used in the same way: the _asynchronous
for loop_ (commonly just called **await for**)
iterates over the events of a stream like the **for loop** iterates
over an [Iterable][]. For example:

Stream 可以透過許多方式建立，這個話題我們會在另一篇文章詳述，
而這些所有的建立方式都可以相同的方式在程式碼中使用：
像使用 **for迴圈** 迭代一個 Iterable 一樣，
我們可以使用 **非同步 for迴圈** （通常我們直接稱之為 **await for**）
來迭代 Stream 中的事件。例如：

<?code-excerpt "misc/lib/tutorial/sum_stream.dart (sumStream)" replace="/async|await for/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
Future<int> sumStream(Stream<int> stream) [!async!] {
  var sum = 0;
  [!await for!] (final value in stream) {
    sum += value;
  }
  return sum;
}
{% endprettify %}

This code simply receives each event of a stream of integer events,
adds them up, and returns (a future of) the sum.
When the loop body ends,
the function is paused until the next event arrives or the stream is done.

該程式碼只是簡單地接收整型事件流中的每一個事件並將它們相加，
然後返回（被 Future 包裹）相加後的整型值。
當迴圈體結束時，函式會暫停直到下一個事件到達或 Stream 完成。

The function is marked with the `async` keyword, which is required
when using the **await for** loop.

內部使用 **await for** 迴圈的函式需要使用 `async` 關鍵字標記。

The following example tests the previous code by
generating a simple stream of integers using an `async*` function:

下面的範例中使用了 `async*` 函式產生一個簡單的
整型 Stream 來測試上一個程式碼片段：

{{site.alert.note}}

  This page uses embedded DartPads to display runnable examples.
  
  本頁面內嵌了一些 DartPads 做例子展示，
  
  {% include dartpads-embedded-troubleshooting.md %}
  
{{site.alert.end}}

<?code-excerpt "misc/lib/tutorial/sum_stream.dart"?>
```dart:run-dartpad
Future<int> sumStream(Stream<int> stream) async {
  var sum = 0;
  await for (final value in stream) {
    sum += value;
  }
  return sum;
}

Stream<int> countStream(int to) async* {
  for (int i = 1; i <= to; i++) {
    yield i;
  }
}

void main() async {
  var stream = countStream(10);
  var sum = await sumStream(stream);
  print(sum); // 55
}
```

{{site.alert.note}}
  
  Click **Run** to see the result in the **Console**.

  **注意：** 點選 **Run** 可以在 **控制檯輸出** 檢視結果。
  
{{site.alert.end}}

## Error events

## 錯誤事件

Streams are done when there are no more events in them,
and the code receiving the events is notified of this just as
it is notified that a new event arrives.
When reading events using an **await for** loop,
the loops stops when the stream is done.

當 Stream 再也沒有需要處理的事件時會變為完成狀態，
與此同時，呼叫者可以像接收到新事件回呼(Callback)那樣接收 Stream 完成的事件回呼(Callback)。
當使用 **await for** 迴圈讀取事件時，迴圈會在 Stream 完成時停止。

In some cases, an error happens before the stream is done;
perhaps the network failed while fetching a file from a remote server,
or perhaps the code creating the events has a bug,
but someone needs to know about it.

有時在 Stream 完成前會出現錯誤；
比如從遠端伺服器獲取檔案時出現網路請求失敗，
或者建立事件時出現 bug，儘管錯誤總是會有可能存在，
但它出現時應該告知使用者。

Streams can also deliver error events like it delivers data events.
Most streams will stop after the first error,
but it is possible to have streams that deliver more than one error,
and streams that deliver more data after an error event.
In this document we only discuss streams that deliver at most one error.

Stream 可以像提供資料事件那樣提供錯誤事件。
大多數 Stream 會在第一次錯誤出現後停止，
但其也可以提供多次錯誤並可以在在出現錯誤後繼續提供資料事件。
在本篇文件中我們只討論 Stream 最多出現並提供一次錯誤事件的情況。

When reading a stream using **await for**, the error is thrown by the
loop statement. This ends the loop, as well. You can catch the
error using **try-catch**.  The following example throws an
error when the loop iterator equals 4:

當使用 **await for** 讀取 Stream 時，
如果出現錯誤，則由迴圈陳述式丟擲，同時迴圈結束。
你可以使用 **try-catch** 陳述式捕獲錯誤。
下面的範例會在迴圈迭代到引數值等於 4 時丟擲一個錯誤：

<?code-excerpt "misc/lib/tutorial/sum_stream_with_catch.dart"?>
```dart:run-dartpad
Future<int> sumStream(Stream<int> stream) async {
  var sum = 0;
  try {
    await for (final value in stream) {
      sum += value;
    }
  } catch (e) {
    return -1;
  }
  return sum;
}

Stream<int> countStream(int to) async* {
  for (int i = 1; i <= to; i++) {
    if (i == 4) {
      throw Exception('Intentional exception');
    } else {
      yield i;
    }
  }
}

void main() async {
  var stream = countStream(10);
  var sum = await sumStream(stream);
  print(sum); // -1
}
```

{{site.alert.note}}

  Click **Run** to see the result in the **Console**.

  **注意：** 點選 **Run** 可以在 **控制檯輸出** 檢視結果。

{{site.alert.end}}

## Working with streams

## Stream 的使用

The Stream class contains a number of helper methods that can do
common operations on a stream for you,
similar to the methods on an [Iterable.][Iterable]
For example, you can find the last positive integer in a stream using
`lastWhere()` from the Stream API.

Stream 類中包含了許多像 [Iterable][Iterable]
類中一樣的輔助方法幫助你實現一些常用的操作。
例如，你可以使用 Stream API 中的 `lastWhere()`
方法從 Stream 中找出最後一個正整數。

<?code-excerpt "misc/lib/tutorial/misc.dart (lastPositive)"?>
```dart
Future<int> lastPositive(Stream<int> stream) =>
    stream.lastWhere((x) => x >= 0);
```

## Two kinds of streams {#two-kinds-of-streams}

## Stream 的兩種型別 {#two-kinds-of-streams}

There are two kinds of streams.

Stream 有兩種型別。

### Single subscription streams {#single-subscription-streams}

### Single-Subscription 型別的 Stream {#single-subscription-streams}

The most common kind of stream contains a sequence of events that
are parts of a larger whole.
Events need to be delivered in the correct order and without
missing any of them.
This is the kind of stream you get when you read a file or receive
a web request.

最常見的型別是一個 Stream 只包含了某個眾多事件序列的一個。
而這些事件需要按順序提供並且不能丟失。
當你讀取一個檔案或接收一個網頁請求時就需要使用這種型別的 Stream。

Such a stream can only be listened to once.
Listening again later could mean missing out on initial events,
and then the rest of the stream makes no sense.
When you start listening,
the data will be fetched and provided in chunks.

這種 Stream 只能設定一次監聽。重複設定則會丟失原來的事件，
而導致你所監聽到的剩餘其它事件毫無意義。
當你開始監聽時，資料將以塊的形式提供和獲取。

### Broadcast streams {#broadcast-streams}

### Broadcast 型別的 Stream {#broadcast-streams}

The other kind of stream is intended for individual messages that
can be handled one at a time. This kind of stream can be used for
mouse events in a browser, for example.

另一種流是針對單個訊息的，這種流可以一次處理一個訊息。
例如可以將其用於瀏覽器的滑鼠事件。

You can start listening to such a stream at any time,
and you get the events that are fired while you listen.
More than one listener can listen at the same time,
and you can listen again later after canceling a previous
subscription.

你可以在任何時候監聽這種 Stream，
且在此之後你可以獲取到任何觸發的事件。
這種流可以在同一時間設定多個不同的監聽器同時監聽，
同時你也可以在取消上一個訂閱後再次對其發起監聽。

## Methods that process a stream {#process-stream-methods}

## 處理 Stream 的方法 {#process-stream-methods}

The following methods on [Stream\<T>][Stream] process the stream and return a
result:

下面這些 [Stream\<T>][Stream] 類中的方法可以對 Stream 進行處理並返回結果：

<?code-excerpt "misc/lib/tutorial/stream_interface.dart (main-stream-members)" remove="/^\s*Stream/"?>
```dart
Future<T> get first;
Future<bool> get isEmpty;
Future<T> get last;
Future<int> get length;
Future<T> get single;
Future<bool> any(bool Function(T element) test);
Future<bool> contains(Object? needle);
Future<E> drain<E>([E? futureValue]);
Future<T> elementAt(int index);
Future<bool> every(bool Function(T element) test);
Future<T> firstWhere(bool Function(T element) test, {T Function()? orElse});
Future<S> fold<S>(S initialValue, S Function(S previous, T element) combine);
Future forEach(void Function(T element) action);
Future<String> join([String separator = '']);
Future<T> lastWhere(bool Function(T element) test, {T Function()? orElse});
Future pipe(StreamConsumer<T> streamConsumer);
Future<T> reduce(T Function(T previous, T element) combine);
Future<T> singleWhere(bool Function(T element) test, {T Function()? orElse});
Future<List<T>> toList();
Future<Set<T>> toSet();
```

All of these functions, except `drain()` and `pipe()`,
correspond to a similar function on [Iterable.][Iterable]
Each one can be written easily by using an `async` function
with an **await for** loop (or just using one of the other methods).
For example, some implementations could be:

上述所有的方法，除了 `drain()` and `pipe()` 方法外，
都在 [Iterable][Iterable] 類中有對應的相似方法。
如果你在非同步函式中使用了 **await for** 迴圈（或者只是在另一個方法中使用），
那麼使用上述的這些方法將會更加容易。例如，一些程式碼實現大概是這樣的：

<?code-excerpt "misc/lib/tutorial/misc.dart (mock-stream-method-implementations)"?>
```dart
Future<bool> contains(Object? needle) async {
  await for (final event in this) {
    if (event == needle) return true;
  }
  return false;
}

Future forEach(void Function(T element) action) async {
  await for (final event in this) {
    action(event);
  }
}

Future<List<T>> toList() async {
  final result = <T>[];
  await forEach(result.add);
  return result;
}

Future<String> join([String separator = '']) async =>
    (await toList()).join(separator);
```

(The actual implementations are slightly more complex,
but mainly for historical reasons.)

（上述程式碼只是個簡單的範例，實際的實現邏輯可能要稍微複雜一點。）

## Methods that modify a stream {#modify-stream-methods}

## 修改 Stream 的方法 {#modify-stream-methods}

The following methods on Stream return a new stream based
on the original stream.
Each one waits until someone listens on the new stream before
listening on the original.

下面的方法可以對原始的 Stream 進行處理並返回新的 Stream。
當呼叫了這些方法後，設定在原始 Stream 上的監聽器
會先監聽被轉換後的新 Stream，
待新的 Stream 處理完成後才會轉而回去監聽原始的 Stream。

<?code-excerpt "misc/lib/tutorial/stream_interface.dart (main-stream-members)" remove="/async\w+|distinct|transform/" retain="/^\s*Stream/"?>
```dart
Stream<R> cast<R>();
Stream<S> expand<S>(Iterable<S> Function(T element) convert);
Stream<S> map<S>(S Function(T event) convert);
Stream<T> skip(int count);
Stream<T> skipWhile(bool Function(T element) test);
Stream<T> take(int count);
Stream<T> takeWhile(bool Function(T element) test);
Stream<T> where(bool Function(T event) test);
```

The preceding methods correspond to similar methods on [Iterable][]
which transform an iterable into another iterable.
All of these can be written easily using an `async` function
with an **await for** loop.

在 [Iterable][] 類中也有一些將一個 iterable 轉換為另一個 iterable 的方法，
上述的這些方法與 [Iterable][] 類中的這些方法相似。
如果你在非同步函式中使用了 **await for** 迴圈，
那麼使用上述的這些方法將會更加容易。

<?code-excerpt "misc/lib/tutorial/stream_interface.dart (main-stream-members)" remove="/transform/" retain="/async\w+|distinct/"?>
```dart
Stream<E> asyncExpand<E>(Stream<E>? Function(T event) convert);
Stream<E> asyncMap<E>(FutureOr<E> Function(T event) convert);
Stream<T> distinct([bool Function(T previous, T next)? equals]);
```

The `asyncExpand()` and `asyncMap()` functions are similar to
`expand()` and `map()`,
but allow their function argument to be an asynchronous function.
The `distinct()` function doesn't exist on `Iterable`, but it could have.

`asyncExpand()` 和 `asyncMap()` 方法與 `expand()` 和 `map()` 方法類似，
不同的是前兩者允許將一個非同步函式作為函式引數。
`Iterable` 中沒有與 `distinct()` 類似的方法，
但是在不久的將來可能會加上。

<?code-excerpt "misc/lib/tutorial/stream_interface.dart (special-stream-members)"?>
```dart
Stream<T> handleError(Function onError, {bool Function(dynamic error)? test});
Stream<T> timeout(Duration timeLimit,
    {void Function(EventSink<T> sink)? onTimeout});
Stream<S> transform<S>(StreamTransformer<T, S> streamTransformer);
```

The final three functions are more special.
They involve error handling which an **await for** loop
can't do—the first error reaching the loops will end
the loop and its subscription on the stream.
There is no recovering from that.
The following code shows how to use `handleError()` to remove errors
from a stream before using it in an **await for** loop.

最後這三個方法比較特殊。它們用於處理 **await for** 迴圈不能處理的錯誤：
當迴圈執行過程中出現錯誤時，該迴圈會結束同時取消 Stream 上的訂閱且不能恢復。
你可以使用 `handleError()` 方法在 **await for** 迴圈中
使用 Stream 前將相關錯誤移除。

<?code-excerpt "misc/lib/tutorial/misc.dart (mapLogErrors)"?>
```dart
Stream<S> mapLogErrors<S, T>(
  Stream<T> stream,
  S Function(T event) convert,
) async* {
  var streamWithoutErrors = stream.handleError((e) => log(e));
  await for (final event in streamWithoutErrors) {
    yield convert(event);
  }
}
```

### The transform() function {#transform-function}

### transform() 方法 {#transform-function}

The `transform()` function is not just for error handling;
it is a more generalized "map" for streams.
A normal map requires one value for each incoming event.
However, especially for I/O streams,
it might take several incoming events to produce an output event.
A [StreamTransformer][] can work with that.
For example, decoders like [Utf8Decoder][] are transformers.
A transformer requires only one function, [bind()][], which can be
easily implemented by an `async` function.

`transform()` 方法並不只是用於處理錯誤；它更是一個通用的 Stream “map 對映”。
通常而言，一個 “map 對映”會為每一個輸入事件設定一個值。
但是對於 I/O Stream 而言，它可能會使用多個輸入事件來產生一個輸出事件。
這時候使用 [StreamTransformer][] 就可以做到這一點。
例如像 [Utf8Decoder][] 這樣的解碼器就是一個變換器。
一個變換器只需要實現一個 [bind()][] 方法，
其可透過 `async` 函式輕鬆實現。

### Reading and decoding a file {#reading-decoding-file}

### 讀取和解碼檔案 {#reading-decoding-file}

The following code reads a file and runs two transforms over the stream.
It first converts the data from UTF8 and then runs it through
a [LineSplitter.][LineSplitter]
All lines are printed, except any that begin with a hashtag, `#`.

下面的程式碼範例讀取一個檔案並在其 Stream 上執行了兩次變換。
第一次轉換是將檔案資料轉換成 UTF-8 編碼格式，
然後將轉換後的資料變換成一個 [LineSplitter][LineSplitter] 執行。
檔案中除了 `#` 開頭的行外其它的行都會被打印出來。

<?code-excerpt "misc/bin/cat_no_hash.dart"?>
```dart
import 'dart:convert';
import 'dart:io';

void main(List<String> args) async {
  var file = File(args[0]);
  var lines = utf8.decoder
      .bind(file.openRead())
      .transform(const LineSplitter());
  await for (final line in lines) {
    if (!line.startsWith('#')) print(line);
  }
}
```

## The listen() method {#listen-method}

## listen() 方法 {#listen-method}

The final method on Stream is `listen()`. This is a "low-level"
method—all other stream functions are defined in terms of `listen()`.

最後一個重要的方法是 `listen()`。這是一個“底層”方法，
其它所有的 Stream 方法都根據 `listen()` 方法定義。

<?code-excerpt "misc/lib/tutorial/stream_interface.dart (listen)"?>
```dart
StreamSubscription<T> listen(void Function(T event)? onData,
    {Function? onError, void Function()? onDone, bool? cancelOnError});
```

To create a new `Stream` type, you can just extend the `Stream`
class and implement the `listen()` method—all other methods
on `Stream` call `listen()` in order to work.

你只需繼承 `Stream` 類並實現 `listen()` 方法來建立一個 `Stream` 型別的子類別。
`Stream` 類中所有其它的方法都依賴於對 `listen()` 方法的呼叫。

The `listen()` method allows you to start listening on a stream.
Until you do so,
the stream is an inert object describing what events you want to see.
When you listen,
a [StreamSubscription][] object is returned which represents the
active stream producing events.
This is similar to how an `Iterable` is just a collection of objects,
but the iterator is the one doing the actual iteration.

`listen()` 方法可以讓你對一個 Stream 進行監聽。
在你對一個 Stream 進行監聽前，它只不過是個惰性物件，
該物件描述了你想檢視的事件。當你對其進行監聽後，
其會返回一個 [StreamSubscription][] 物件，
該物件用以表示一個生產事件的活躍的 Stream。
這與 `Iterable` 物件的實現方式類似，
不同的是 `Iterable` 物件可返回迭代器並可以進行真實的迭代操作。

The stream subscription allows you to pause the subscription,
resume it after a pause,
and cancel it completely.
You can set callbacks to be called for each data event or
error event, and when the stream is closed.

Stream 允許你暫停、繼續甚至完全取消一個訂閱。
你也可以為其設定一個回呼(Callback)，該回調會在每一個數據事件、
錯誤事件以及 Stream 自身關閉時通知呼叫者。

## Other resources

## 其它資源資訊

Read the following documentation for more details on using streams
and asynchronous programming in Dart.

可以閱讀下面的文件獲取更多關於在 Dart 中使用 Stream 和非同步程式設計的資訊：

* [Creating Streams in Dart](/articles/libraries/creating-streams),
  an article about creating your own streams

  [在 Dart 中建立 Stream](/articles/libraries/creating-streams), 該文將向你介紹如何建立 Stream

* [Futures and Error Handling](/guides/libraries/futures-error-handling),
  an article that explains how to handle errors using the Future API

  [Futures以及錯誤處理](/guides/libraries/futures-error-handling), 該文將向你介紹如何在使用 Future API 時處理相關錯誤。

* [Asynchrony support](/guides/language/language-tour#asynchrony-support),
  a section in the [language tour](/guides/language/language-tour)

  [Dart開發語言概覽](/guides/language/language-tour) 中的 [非同步支援](/guides/language/language-tour#asynchrony-support) 部分。

* [Stream API reference]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-async/Stream-class.html)

  [Stream API 參考文件]({{site.dart_api}}/{{site.data.pkg-vers.SDK.channel}}/dart-async/Stream-class.html)

[bind()]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-async/StreamTransformer/bind.html
[LineSplitter]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-convert/LineSplitter-class.html
[Future]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-async/Future-class.html
[Iterable]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Iterable-class.html
[Stream]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-async/Stream-class.html
[StreamSubscription]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-async/StreamSubscription-class.html
[StreamTransformer]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-async/StreamTransformer-class.html
[Utf8Decoder]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-convert/Utf8Decoder-class.html
