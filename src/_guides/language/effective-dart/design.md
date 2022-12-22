---
title: "Effective Dart: Design"
title: 高效 Dart 語言指南：API 設計
description: Design consistent, usable libraries.
description: 庫的 API 設計。
prevpage:
  url: /guides/language/effective-dart/usage
  title: Usage
  title: 用法範例
---
<?code-excerpt replace="/([A-Z]\w*)\d\b/$1/g"?>
<?code-excerpt plaster="none"?>
<?code-excerpt path-base="misc/lib/effective_dart"?>

Here are some guidelines for writing consistent, usable APIs for libraries.

下面給出的準則用於指導為庫編寫一致的、可用的 API。

## Names

## 命名

Naming is an important part of writing readable, maintainable code.
The following best practices can help you achieve that goal.

命名是編寫可讀，可維護程式碼的重要部分。
以下最佳實踐可幫助你實現這個目標。

### DO use terms consistently.

### **要** 使用一致的術語。

Use the same name for the same thing, throughout your code. If a precedent
already exists outside your API that users are likely to know, follow that
precedent.

在你的程式碼中，同樣的東西要使用同樣的名字。
如果之前已經存在的 API 之外命名，並且使用者已經熟知，
那麼請繼續使用這個命名。

{:.good}
{% prettify dart tag=pre+code %}
pageCount         // A field.
updatePageCount() // Consistent with pageCount.
toSomething()     // Consistent with Iterable's toList().
asSomething()     // Consistent with List's asMap().
Point             // A familiar concept.
{% endprettify %}

{:.bad}
{% prettify dart tag=pre+code %}
renumberPages()      // Confusingly different from pageCount.
convertToSomething() // Inconsistent with toX() precedent.
wrappedAsSomething() // Inconsistent with asX() precedent.
Cartesian            // Unfamiliar to most users.
{% endprettify %}

The goal is to take advantage of what the user already knows. This includes
their knowledge of the problem domain itself, the conventions of the core
libraries, and other parts of your own API. By building on top of those, you
reduce the amount of new knowledge they have to acquire before they can be
productive.

總的目的是充分利用使用者已經知道的內容。
這裡包括他們所瞭解的問題領域，所熟悉的核心庫，以及你自己 API 那部分。
基於以上這些內容，他們在使用之前，不需要學習大量的新知識。


### AVOID abbreviations.

### **避免** 縮寫。

Unless the abbreviation is more common than the unabbreviated term, don't
abbreviate. If you do abbreviate, [capitalize it correctly][caps].

只使用廣為人知的縮寫，對於特有領域的縮寫，請避免使用。
如果要使用，請 [正確的指定首字母大小寫][caps]。

[caps]: /guides/language/effective-dart/style#identifiers

{:.good}
{% prettify dart tag=pre+code %}
pageCount
buildRectangles
IOStream
HttpRequest
{% endprettify %}

{:.bad}
{% prettify dart tag=pre+code %}
numPages    // "Num" is an abbreviation of "number (of)".
buildRects
InputOutputStream
HypertextTransferProtocolRequest
{% endprettify %}


### PREFER putting the most descriptive noun last.

### **推薦** 把最具描述性的名詞放到最後。

The last word should be the most descriptive of what the thing is. You can
prefix it with other words, such as adjectives, to further describe the thing.

最後一個詞應該是最具描述性的東西。
你可以在其前面新增其他單詞，例如形容詞，以進一步描述該事物。

{:.good}
{% prettify dart tag=pre+code %}
pageCount             // A count (of pages).
ConversionSink        // A sink for doing conversions.
ChunkedConversionSink // A ConversionSink that's chunked.
CssFontFaceRule       // A rule for font faces in CSS.
{% endprettify %}

{:.bad}
{% prettify dart tag=pre+code %}
numPages                  // Not a collection of pages.
CanvasRenderingContext2D  // Not a "2D".
RuleFontFaceCss           // Not a CSS.
{% endprettify %}


### CONSIDER making the code read like a sentence.

### **考慮** 儘量讓程式碼看起來像普通的句子。

When in doubt about naming, write some code that uses your API, and try to read
it like a sentence.

當你不知道如何命名 API 的時候，
使用你的 API 編寫些程式碼，試著讓程式碼看起來像普通的句子。

{:.good}
<?code-excerpt "design_good.dart (code-like-prose)"?>
{% prettify dart tag=pre+code %}
// "If errors is empty..."
if (errors.isEmpty) ...

// "Hey, subscription, cancel!"
subscription.cancel();

// "Get the monsters where the monster has claws."
monsters.where((monster) => monster.hasClaws);
{% endprettify %}

{:.bad}
<?code-excerpt "design_bad.dart (code-like-prose)" replace="/ as bool//g"?>
{% prettify dart tag=pre+code %}
// Telling errors to empty itself, or asking if it is?
if (errors.empty) ...

// Toggle what? To what?
subscription.toggle();

// Filter the monsters with claws *out* or include *only* those?
monsters.filter((monster) => monster.hasClaws);
{% endprettify %}

It's helpful to try out your API and see how it "reads" when used in code, but
you can go too far. It's not helpful to add articles and other parts of speech
to force your names to *literally* read like a grammatically correct sentence.

嘗試著使用你自己的 API，並且閱讀寫出來的程式碼，可以幫助你為 API 命名，但是不要過於冗餘。
新增文章和其他詞性以強制名字讀起來就像語法正確的句子一樣，是沒用的。

{:.bad}
<?code-excerpt "design_bad.dart (code-like-prose-overdone)"?>
{% prettify dart tag=pre+code %}
if (theCollectionOfErrors.isEmpty) ...

monsters.producesANewSequenceWhereEach((monster) => monster.hasClaws);
{% endprettify %}


### PREFER a noun phrase for a non-boolean property or variable.

### **推薦** 使用名詞短語來命名不是布林型別的變數和屬性。

The reader's focus is on *what* the property is. If the user cares more about
*how* a property is determined, then it should probably be a method with a
verb phrase name.

讀者關注屬性是*什麼*。
如果使用者更關心*如何*確定一個屬性，則很可能應該是一個使用動詞短語命名函式。

{:.good}
{% prettify dart tag=pre+code %}
list.length
context.lineWidth
quest.rampagingSwampBeast
{% endprettify %}

{:.bad}
{% prettify dart tag=pre+code %}
list.deleteItems
{% endprettify %}


### PREFER a non-imperative verb phrase for a boolean property or variable.

### **推薦** 使用非命令式動詞短語命名布林型別的變數和屬性。

Boolean names are often used as conditions in control flow, so you want a name
that reads well there. Compare:

布林名稱通常用在控制陳述式中當做條件，
因此你要應該讓這個名字在控制陳述式中讀起來語感很好。比較下面的兩個：

{% prettify dart tag=pre+code %}
if (window.closeable) ...  // Adjective.
if (window.canClose) ...   // Verb.
{% endprettify %}

Good names tend to start with one of a few kinds of verbs:

好的名字往往以某一種動詞作為開頭：

*   a form of "to be": `isEnabled`, `wasShown`, `willFire`. These are, by far,
    the most common.

    “to be” 形式： `isEnabled`， `wasShown`， `willFire`。 
    就目前來看，這些時做常見的。

*   an [auxiliary verb][]: `hasElements`, `canClose`,
    `shouldConsume`, `mustSave`.

    一個 [輔助動詞][auxiliary verb]: `hasElements`， `canClose`，
    `shouldConsume`， `mustSave`。

*   an active verb: `ignoresInput`, `wroteFile`. These are rare because they are
    usually ambiguous. `loggedResult` is a bad name because it could mean
    "whether or not a result was logged" or "the result that was logged".
    Likewise, `closingConnection` could be "whether the connection is closing"
    or "the connection that is closing". Active verbs are allowed when the name
    can *only* be read as a predicate.

    一個主動動詞： `ignoresInput`， `wroteFile`。
    因為經常引起歧義，所以這種形式比較少見。
    `loggedResult` 是一個不好的命名，因為它的意思可能是：
    "whether or not a result was logged" 或者 "the result that was logged"。
    `closingConnection` 的意思可能是：
    "whether the connection is closing" 或者 "the connection that is closing"。
    *只有* 當名字可以預期的時候才使用主動動詞。

[auxiliary verb]: https://en.wikipedia.org/wiki/Auxiliary_verb

What separates all these verb phrases from method names is that they are not
*imperative*. A boolean name should never sound like a command to tell the
object to do something, because accessing a property doesn't change the object.
(If the property *does* modify the object in a meaningful way, it should be a
method.)

可以使用命令式動詞來區分佈爾變數名字和函式名字。
一個布林變數的名字不應該看起來像一個命令，告訴這個物件做什麼事情。
原因在於存取一個變數的屬性並沒有修改物件的狀態。
（如果這個屬性*確實*修改了物件的狀態，則它應該是一個函式。）

{:.good}
{% prettify dart tag=pre+code %}
isEmpty
hasElements
canClose
closesWindow
canShowPopup
hasShownPopup
{% endprettify %}

{:.bad}
{% prettify dart tag=pre+code %}
empty         // Adjective or verb?
withElements  // Sounds like it might hold elements.
closeable     // Sounds like an interface.
              // "canClose" reads better as a sentence.
closingWindow // Returns a bool or a window?
showPopup     // Sounds like it shows the popup.
{% endprettify %}


### CONSIDER omitting the verb for a named boolean *parameter*.

### **考慮** 省略命名布林*引數*的動詞。

This refines the previous rule. For named parameters that are boolean, the name
is often just as clear without the verb, and the code reads better at the call
site.

提煉於上一條規則。對於命名布林引數，
沒有動詞的名稱通常看起來更加舒服。

{:.good}
<?code-excerpt "design_good.dart (omit-verb-for-bool-param)"?>
{% prettify dart tag=pre+code %}
Isolate.spawn(entryPoint, message, paused: false);
var copy = List.from(elements, growable: true);
var regExp = RegExp(pattern, caseSensitive: false);
{% endprettify %}


### PREFER the "positive" name for a boolean property or variable.

### **考慮** 為布林屬性或變數取“肯定”含義的名字。

Most boolean names have conceptually "positive" and "negative" forms where the
former feels like the fundamental concept and the latter is its
negation—"open" and "closed", "enabled" and "disabled", etc. Often the
latter name literally has a prefix that negates the former: "visible" and
"*in*-visible", "connected" and "*dis*-connected", "zero" and "*non*-zero".

大多數布林值名稱具有概念形式上的“肯定”和“否定”，
前者感覺更像是基本描述，後者是對基本描述的否定，例如：
"open" 和 "closed"， "enabled" 和 "disabled"，等等。
通常後者的名稱字面上有個字首，用來否定前者：
"visible" 和 "*in*-visible"，
"connected" 和 "*dis*-connected"，
"zero" 和 "*non*-zero"。

When choosing which of the two cases that `true` represents—and thus
which case the property is named for—prefer the positive or more
fundamental one. Boolean members are often nested inside logical expressions,
including negation operators. If your property itself reads like a negation,
it's harder for the reader to mentally perform the double negation and
understand what the code means.

當選擇 `true` 代表兩種情況中的其中一種情況
在布林的兩種情況中，當選擇 `true` 代表其中一種情況，
或使用這種情況作為屬性名稱時，更傾向使用“肯定”或基本描述的方式。
布林成員通常巢狀(Nesting)在邏輯表示式中，包括否定運算子。
如果屬性本身讀起來想是個“否定”的，
這將讓讀者耗費更多精力去閱讀雙重否定及理解程式碼的含義。

{:.good}
<?code-excerpt "design_good.dart (positive)"?>
{% prettify dart tag=pre+code %}
if (socket.isConnected && database.hasData) {
  socket.write(database.read());
}
{% endprettify %}

{:.bad}
<?code-excerpt "design_bad.dart (positive)"?>
{% prettify dart tag=pre+code %}
if (!socket.isDisconnected && !database.isEmpty) {
  socket.write(database.read());
}
{% endprettify %}

For some properties, there is no obvious positive form. Is a document that has
been flushed to disk "saved" or "*un*-changed"? Is a document that *hasn't* been
flushed "*un*-saved" or "changed"? In ambiguous cases, lean towards the choice
that is less likely to be negated by users or has the shorter name.

對於一些屬性，沒有明顯的“肯定”形式。
文件已經重新整理 “saved” 到磁碟，或者 "*un*-changed"？
文件還未屬性 “*un*-saved” 到磁碟，或者 "changed"？
在模稜兩可的情況下，傾向於選擇不太可能被使用者否定或較短的名字。

**Exception:** With some properties, the negative form is what users
overwhelmingly need to use. Choosing the positive case would force them to
negate the property with `!` everywhere. Instead, it may be better to use the
negative case for that property.

**例外:**  “否定”使用者絕大多數用到的形式。
選擇「肯定」方式，將會迫使在他們到處使用 `!` 對屬性進行取反操作。
這樣相反，屬性應該使用「否定」形式進行命名。

### PREFER an imperative verb phrase for a function or method whose main purpose is a side effect.

### **推薦** 使用命令式動詞短語來命名帶有副作用的函式或者方法。

Callable members can return a result to the caller and perform other work or
side effects. In an imperative language like Dart, members are often called
mainly for their side effect: they may change an object's internal state,
produce some output, or talk to the outside world.

函式通常返回一個結果給呼叫者，並且執行一些任務或者帶有副作用。
在像 Dart 這種命令式語言中，呼叫函式通常為了實現其副作用：
可能改變了物件的內部狀態、
產生一些輸出內容、或者和外部世界溝通等。

Those kinds of members should be named using an imperative verb phrase that
clarifies the work the member performs.

這種型別的成員應該使用命令式動詞短語來命名，強調
該成員所執行的任務。

{:.good}
<?code-excerpt "design_good.dart (verb-for-func-with-side-effect)"?>
{% prettify dart tag=pre+code %}
list.add('element');
queue.removeFirst();
window.refresh();
{% endprettify %}

This way, an invocation reads like a command to do that work.

這樣，呼叫的方法讀起來會讓人覺得是一個執行命令。


### PREFER a noun phrase or non-imperative verb phrase for a function or method if returning a value is its primary purpose.

### **考慮** 使用名詞短語或者非命令式動詞短語命名返回資料為主要功能的方法或者函式。

Other callable members have few side effects but return a useful result to the
caller. If the member needs no parameters to do that, it should generally be a
getter. But sometimes a logical "property" needs some parameters. For example,
`elementAt()` returns a piece of data from a collection, but it needs a
parameter to know *which* piece of data to return.

雖然這些函式可能也有副作用，但是其主要目的是返回一個數據給呼叫者。
如果該函式無需引數通常應該是一個 getter 。
有時候獲取一個屬性則需要一些引數，比如，
`elementAt()` 從集合中返回一個數據，但是需要一個指定返回那個資料的引數。

This means the member is *syntactically* a method, but *conceptually* it is a
property, and should be named as such using a phrase that describes *what* the
member returns.

在*語法*上看這是一個函式，其實*嚴格來說*其返回的是集合中的一個屬性，
應該使用一個能夠表示該函式返回的是*什麼*的詞語來命名。

{:.good}
<?code-excerpt "design_good.dart (noun-for-func-returning-value)"?>
{% prettify dart tag=pre+code %}
var element = list.elementAt(3);
var first = list.firstWhere(test);
var char = string.codeUnitAt(4);
{% endprettify %}

This guideline is deliberately softer than the previous one. Sometimes a method
has no side effects but is still simpler to name with a verb phrase like
`list.take()` or `string.split()`.

這條規則比前一條要寬鬆一些。有時候一些
函式沒有副作用，但仍然使用一個動詞短語來命名，例如：
`list.take()` 或者 `string.split()`。


### CONSIDER an imperative verb phrase for a function or method if you want to draw attention to the work it performs.

### **考慮** 使用命令式動詞短語命名一個函式或方法，若果你希望它的執行能被重視。

When a member produces a result without any side effects, it should usually be a
getter or a method with a noun phrase name describing the result it returns.
However, sometimes the work required to produce that result is important. It may
be prone to runtime failures, or use heavyweight resources like networking or
file I/O. In cases like this, where you want the caller to think about the work
the member is doing, give the member a verb phrase name that describes that
work.

當一個成員產生的結果沒有額外的影響，它通常應該使用一個 getter 或者一個名詞短語描述來命名，用於描述它返回的結果。
但是，有時候執行產生的結果很重要。
它可能容易導致執行時故障，或者使用重量級的資源（例如，網路或檔案 I/O）。
在這種情況下，你希望呼叫者考慮成員在進行的工作，
這時，為成員提供描述該工作的動詞短語。

{:.good}
<?code-excerpt "design_good.dart (verb-for-func-with-work)"?>
{% prettify dart tag=pre+code %}
var table = database.downloadData();
var packageVersions = packageGraph.solveConstraints();
{% endprettify %}

Note, though, that this guideline is softer than the previous two. The work an
operation performs is often an implementation detail that isn't relevant to the
caller, and performance and robustness boundaries change over time. Most of the
time, name your members based on *what* they do for the caller, not *how* they
do it.

但請注意，此準則比前兩個更寬鬆。操作執行工作的實現細節通常與呼叫這無關，
並且效能和健壯性是隨時間經常改變的。
大多數情況下，根據成員為呼叫者做了“什麼”來命名，而不是“如何”做。


### AVOID starting a method name with `get`.

### **避免** 在方法命名中使用 `get` 開頭。

In most cases, the method should be a getter with `get` removed from the name.
For example, instead of a method named `getBreakfastOrder()`, define a getter
named `breakfastOrder`.

在大多數情況下，getter 方法名稱中應該移除 `get` 。
例如，定義一個名為 `breakfastOrder` 的 getter 方法，
來替代名為 `getBreakfastOrder()` 的方法。

Even if the member does need to be a method because it takes arguments or
otherwise isn't a good fit for a getter, you should still avoid `get`. Like the
previous guidelines state, either:

即使成員因為需要傳入引數或者 getter 不適用，
而需要透過方法來實現，也應該避免使用 `get` 開頭。
與之前的準則一樣：

* Simply drop `get` and [use a noun phrase name][noun] like `breakfastOrder()`
  if the caller mostly cares about the value the method returns.

  如果呼叫者主要關心的是方法的返回值，只需刪除 `get` 並使用 [名詞短語][noun] 命名，
  如 `breakfastOrder()` 。

* [Use a verb phrase name][verb] if the caller cares about the work being done,
  but pick a verb that more precisely describes the work than `get`, like
  `create`, `download`, `fetch`, `calculate`, `request`, `aggregate`, etc.

  如果呼叫者關心的是正在完成的工作，請使用 [動名詞短語][verb] 命名，
  這種情況下應該選擇一個更能準確描述工作的動名詞，而不是使用 `get` 命名，
  如 `create`， `download`， `fetch`， `calculate`， `request`， `aggregate`，等等。

[noun]: #prefer-a-noun-phrase-or-non-imperative-verb-phrase-for-a-function-or-method-if-returning-a-value-is-its-primary-purpose

[verb]: #consider-an-imperative-verb-phrase-for-a-function-or-method-if-you-want-to-draw-attention-to-the-work-it-performs


### PREFER naming a method `to___()` if it copies the object's state to a new object.

### **推薦** 使用 `to___()` 來命名把物件的狀態轉換到一個新的物件的函式。

{% include linter-rule-mention.md rule="use_to_and_as_if_applicable" %}

A *conversion* method is one that returns a new object containing a copy of
almost all of the state of the receiver but usually in some different form or
representation. The core libraries have a convention that these methods are
named starting with `to` followed by the kind of result.

一個轉換函式返回一個新的物件，裡面包含一些原物件的狀態，
但通常新物件的形式或表現方式與原物件不同。
核心函式庫有一個約定，這些型別結果的方法名應該以 `to` 作為開頭。

If you define a conversion method, it's helpful to follow that convention.

如果要定義一個轉換函式，遵循該約定是非常有益的。

{:.good}
<?code-excerpt "design_good.dart (to___)"?>
{% prettify dart tag=pre+code %}
list.toSet();
stackTrace.toString();
dateTime.toLocal();
{% endprettify %}


### PREFER naming a method `as___()` if it returns a different representation backed by the original object.

### **推薦** 使用 `as___()` 來命名把原來物件轉換為另外一種表現形式的函式。

{% include linter-rule-mention.md rule="use_to_and_as_if_applicable" %}

Conversion methods are "snapshots". The resulting object has its own copy of the
original object's state. There are other conversion-like methods that return
*views*—they provide a new object, but that object refers back to the
original. Later changes to the original object are reflected in the view.

轉換函式提供的是“快照功能”。返回的物件有自己的資料副本，
修改原來物件的資料不會改變返回的物件中的資料。
另外一種函式返回的是同一份資料的另外一種表現形式，返回的是一個新的物件，
但是其內部參考的資料和原來物件參考的資料一樣。
修改原來物件中的資料，新返回的物件中的資料也一起被修改。

The core library convention for you to follow is `as___()`.

這種函式在核心庫中被命名為 `as___()`。

{:.good}
<?code-excerpt "design_good.dart (as___)"?>
{% prettify dart tag=pre+code %}
var map = table.asMap();
var list = bytes.asFloat32List();
var future = subscription.asFuture();
{% endprettify %}


### AVOID describing the parameters in the function's or method's name.

### **避免** 在方法或者函式名稱中描述引數。

The user will see the argument at the call site, so it usually doesn't help
readability to also refer to it in the name itself.

在呼叫程式碼的時候可以看到引數，所以無需再次顯示引數了。

{:.good}
<?code-excerpt "design_good.dart (avoid-desc-param-in-func)"?>
{% prettify dart tag=pre+code %}
list.add(element);
map.remove(key);
{% endprettify %}

{:.bad}
{% prettify dart tag=pre+code %}
list.addElement(element)
map.removeKey(key)
{% endprettify %}

However, it can be useful to mention a parameter to disambiguate it from other
similarly-named methods that take different types:

但是，對於具有多個類似的函式的時候，使用引數名字可以消除歧義，
這個時候應該帶有引數名字：

{:.good}
<?code-excerpt "design_good.dart (desc-param-in-func-ok)"?>
{% prettify dart tag=pre+code %}
map.containsKey(key);
map.containsValue(value);
{% endprettify %}


### DO follow existing mnemonic conventions when naming type parameters.

### **要** 在命名引數時，遵循現有的助記符約定。

Single letter names aren't exactly illuminating, but almost all generic types
use them. Fortunately, they mostly use them in a consistent, mnemonic way.
The conventions are:

單字母命名沒有直接的啟發性，但是幾乎所有通用型別都使用時情況就不一樣了。
幸運的是，它們大多數以一致的助記方式在使用，這些約定如下：

*   `E` for the **element** type in a collection:

    `E` 用於集合中的 **元素** 型別:

    {:.good}
    <?code-excerpt "design_good.dart (type-parameter-e)" replace="/\n\n/\n/g"?>
    {% prettify dart tag=pre+code %}
    class IterableBase<E> {}
    class List<E> {}
    class HashSet<E> {}
    class RedBlackTree<E> {}
    {% endprettify %}

*   `K` and `V` for the **key** and **value** types in an associative
    collection:

    `K` 和 `V` 分別用於關聯集合中的 **key** 和 **value** 型別：

    {:.good}
    <?code-excerpt "design_good.dart (type-parameter-k-v)" replace="/\n\n/\n/g"?>
    {% prettify dart tag=pre+code %}
    class Map<K, V> {}
    class Multimap<K, V> {}
    class MapEntry<K, V> {}
    {% endprettify %}

*   `R` for a type used as the **return** type of a function or a class's
    methods. This isn't common, but appears in typedefs sometimes and in classes
    that implement the visitor pattern:

    `R` 用於函式或類方法的 **返回值** 型別。 這種情況並不常見，
    但有時會出現在typedef中，或實現存取者模式的類中：

    {:.good}
    <?code-excerpt "design_good.dart (type-parameter-r)"?>
    {% prettify dart tag=pre+code %}
    abstract class ExpressionVisitor<R> {
      R visitBinary(BinaryExpression node);
      R visitLiteral(LiteralExpression node);
      R visitUnary(UnaryExpression node);
    }
    {% endprettify %}

*   Otherwise, use `T`, `S`, and `U` for generics that have a single type
    parameter and where the surrounding type makes its meaning obvious. There
    are multiple letters here to allow nesting without shadowing a surrounding
    name. For example:

    除此以外，對於具有單個型別引數的泛型，如果助記符能在周圍型別中明顯表達泛型含義，
    請使用`T`，`S` 和 `U` 。
    這裡允許多個字母巢狀(Nesting)且不會與周圍命名產生歧義。例如：

    {:.good}
    <?code-excerpt "design_good.dart (type-parameter-t)"?>
    {% prettify dart tag=pre+code %}
    class Future<T> {
      Future<S> then<S>(FutureOr<S> onValue(T value)) => ...
    }
    {% endprettify %}

    Here, the generic method `then<S>()` uses `S` to avoid shadowing the `T`
    on `Future<T>`.

    這裡，通常 `then<S>()` 方法使用 `S` 避免 `Future<T>` 中的 `T` 產生歧義。

If none of the above cases are a good fit, then either another single-letter
mnemonic name or a descriptive name is fine:

如果上述情況都不合適，則可以使用另一個單字母助記符名稱或描述性的名稱：

{:.good}
<?code-excerpt "design_good.dart (type-parameter-graph)"?>
{% prettify dart tag=pre+code %}
class Graph<N, E> {
  final List<N> nodes = [];
  final List<E> edges = [];
}

class Graph<Node, Edge> {
  final List<Node> nodes = [];
  final List<Edge> edges = [];
}
{% endprettify %}

In practice, the existing conventions cover most type parameters.

在實踐中，以上的約定涵蓋了大多數引數型別。

## Libraries

## 庫

A leading underscore character ( `_` ) indicates that a member is private to its
library. This is not mere convention, but is built into the language itself.

以 ( `_` ) 開頭的成員只能在其庫的內部被存取，是庫的私有成員。
這是 Dart 語言的內建特性，不僅僅是慣例。

### PREFER making declarations private.

### **推薦** 使用私有宣告。

A public declaration in a library—either top level or in a class—is
a signal that other libraries can and should access that member. It is also a
commitment on your library's part to support that and behave properly when it
happens.

庫中的公開宣告&mdash;最上層定義或者在類中定義&mdash;是一種訊號，
表示其他庫可以並應該存取這些成員。
同時公開宣告也是一種你的函式庫需要實現的契約，
當使用這些成員的時候，應該實現其宣稱的功能。

If that's not what you intend, add the little `_` and be happy. Narrow public
interfaces are easier for you to maintain and easier for users to learn. As a
nice bonus, the analyzer will tell you about unused private declarations so you
can delete dead code. It can't do that if the member is public because it
doesn't know if any code outside of its view is using it.

如果某個成員你不希望公開，則在成員名字之前新增一個 `_` 即可。
減少公開的介面讓你的函式庫更容易維護，也讓使用者更加容易掌握你的函式庫如何使用。
另外，分析工具還可以分析出沒有用到的私有成員定義，然後告訴你可以刪除這些無用的程式碼。
私有成員第三方程式碼無法呼叫而你自己在庫中也沒有使用，所以是無用的程式碼。


### CONSIDER declaring multiple classes in the same library.

### **考慮** 宣告多個類在一個函式庫中。

Some languages, such as Java, tie the organization of files to the organization of
classes—each file may only define a single top level class. Dart does not
have that limitation. Libraries are distinct entities separate from classes.
It's perfectly fine for a single library to contain multiple classes, top level
variables, and functions if they all logically belong together.

一些其他語言，比如 Java。將檔案結構和類結構進行捆綁&mdash：每個檔案僅能定義一個最上層類別。
Dart 沒有這樣的限制。庫與類是相互獨立的。如果多個類，最上層變數，以及函式，他們再邏輯上
歸為同一類，那麼將他們包含到單一的函式庫中，這樣做是非常棒的。

Placing multiple classes together in one library can enable some useful
patterns. Since privacy in Dart works at the library level, not the class level,
this is a way to define "friend" classes like you might in C++. Every class
declared in the same library can access each other's private members, but code
outside of that library cannot.

將多個類組織到一個函式庫中，就可以使用一些有用的模式。因為在 Dart 中私有特性是在庫級別上有效，
而不是在類級別，基於這個模式你可以定義類似於 C++ 中的 "friend" 類別。所有定義在同一個庫中
的類可以互相存取彼此的私有成員，但庫以外的程式碼無法發存取。

Of course, this guideline doesn't mean you *should* put all of your classes into
a huge monolithic library, just that you are allowed to place more than one
class in a single library.

當然，指南並不建議你 **應該** 把所有的類都放在單個巨大的函式庫中，
你可以同時在一個函式庫中放置多個類別。

## Classes and mixins

## 類

Dart is a "pure" object-oriented language in that all objects are instances of
classes. But Dart does not require all code to be defined inside a
class—you can define top-level variables, constants, and functions like
you can in a procedural or functional language.

Dart是一種 “純粹的” 面嚮物件語言，因為所有物件都是類別的例項。但是 Dart 並沒有要求所有程式碼都
定義到類中&mdash; 類似在面向過程或函式的語言，你可以在 Dart 中定義最上層變數，常量，以及函式。

### AVOID defining a one-member abstract class when a simple function will do.

### **避免** 避免為了使用一個簡單的函式而去定義一個單一成員的抽象類別

{% include linter-rule-mention.md rule="one_member_abstracts" %}

Unlike Java, Dart has first-class functions, closures, and a nice light syntax
for using them. If all you need is something like a callback, just use a
function. If you're defining a class and it only has a single abstract member
with a meaningless name like `call` or `invoke`, there is a good chance you
just want a function.

和 Java 不同，Dart 擁有一等公民的函式，閉套件，以及它們簡潔的使用語法。如果你僅僅是需要一個
類似於回呼(Callback)的功能，那麼使用函式即可。 例如如果你正在定義一個類別，並且它僅擁有一個毫無意義名稱的
抽象成員，如 `call` 或 `invoke` ，那麼這時你很可能只是需要一個函式。

{:.good}
<?code-excerpt "design_good.dart (one-member-abstract-class)"?>
{% prettify dart tag=pre+code %}
typedef Predicate<E> = bool Function(E element);
{% endprettify %}

{:.bad}
<?code-excerpt "design_bad.dart (one-member-abstract-class)"?>
{% prettify dart tag=pre+code %}
abstract class Predicate<E> {
  bool test(E element);
}
{% endprettify %}


### AVOID defining a class that contains only static members.

### **避免** 定義僅包含靜態成員的類別。

{% include linter-rule-mention.md rule="avoid_classes_with_only_static_members" %}

In Java and C#, every definition *must* be inside a class, so it's common to see
"classes" that exist only as a place to stuff static members. Other classes are
used as namespaces—a way to give a shared prefix to a bunch of members to
relate them to each other or avoid a name collision.

在 Java 和 C# 中，所有的定義*必須*要在類中。所有常常會看到一些這樣的類，這些
類中僅僅放置了些靜態成員。其他類僅用於名稱空間&mdash;一種為一堆成員提供共享
字首將它們相互關聯或避免名稱衝突的方法。

Dart has top-level functions, variables, and constants, so you don't *need* a
class just to define something. If what you want is a namespace, a library is a
better fit. Libraries support import prefixes and show/hide combinators. Those
are powerful tools that let the consumer of your code handle name collisions in
the way that works best for *them*.

Dart 有最上層函式、變數和常量，因此你 **不需要** 僅僅為了定義一些內容而建立一個類別。
如果你想要的是一個名稱空間，那麼一個函式庫是更合適的。庫支援匯入時指定字首，以及僅匯入其一部分。
這些強大的功能讓呼叫的程式碼可以以最適合的方式處理 **它們的** 名稱衝突。

If a function or variable isn't logically tied to a class, put it at the top
level. If you're worried about name collisions, give it a more precise name or
move it to a separate library that can be imported with a prefix.

如果函式或變數在邏輯上與類無關，那麼應該將其置於最上層。如果擔心名稱衝突，
那麼請為其指定更精確的名稱，或將其移動到可以使用字首匯入的單獨庫中。

{:.good}
<?code-excerpt "design_good.dart (class-only-static)"?>
{% prettify dart tag=pre+code %}
DateTime mostRecent(List<DateTime> dates) {
  return dates.reduce((a, b) => a.isAfter(b) ? a : b);
}

const _favoriteMammal = 'weasel';
{% endprettify %}

{:.bad}
<?code-excerpt "design_bad.dart (class-only-static)"?>
{% prettify dart tag=pre+code %}
class DateUtils {
  static DateTime mostRecent(List<DateTime> dates) {
    return dates.reduce((a, b) => a.isAfter(b) ? a : b);
  }
}

class _Favorites {
  static const mammal = 'weasel';
}
{% endprettify %}

In idiomatic Dart, classes define *kinds of objects*. A type that is never
instantiated is a code smell.

通常在 Dart 中，類定義了*一類物件*。一個類別型，如果型別從來沒有被初始化，
那麼這是另一種的程式碼氣息。

However, this isn't a hard rule. With constants and enum-like types, it may be
natural to group them in a class.

當然，這並不是一條硬性規則。對於常量和類似列舉的型別，將它們組合在一個類別
中看起來也是很自然。

{:.good}
<?code-excerpt "design_bad.dart (class-only-static-exception)"?>
{% prettify dart tag=pre+code %}
class Color {
  static const red = '#f00';
  static const green = '#0f0';
  static const blue = '#00f';
  static const black = '#000';
  static const white = '#fff';
}
{% endprettify %}


### AVOID extending a class that isn't intended to be subclassed.

### **避免** 整合一個不期望被整合的類別。

If a constructor is changed from a generative constructor to a factory
constructor, any subclass constructor calling that constructor will break.
Also, if a class changes which of its own methods it invokes on `this`, that
may break subclasses that override those methods and expect them to be called
at certain points.

如果一個類別的建構函式從產生建構函式被更改為工廠建構函式，則呼叫該建構函式的任何子類別建構函式都
將失敗。 此外，如果一個類別改變了它在 `this` 上呼叫的自己的方法，那麼覆蓋這些方法並期望他們在
某些點被呼叫的子類別再呼叫時會失敗。

Both of these mean that a class needs to be deliberate about whether or not it
wants to allow subclassing. This can be communicated in a doc comment, or by
giving the class an obvious name like `IterableBase`. If the author of the class
doesn't do that, it's best to assume you should *not* extend the class.
Otherwise, later changes to it may break your code.

以上兩種情況都意味著一個類別需要考慮是否要允許被子類別化。這種情況可以透過文件註釋來溝通，或者為類
提供一個顯示命名，如 `IterableBase`。如果該類別的作者不這樣做，最好假設你*不*能夠繼承這個類別。
否則，後續對它的修改可能會破壞你的程式碼。


### DO document if your class supports being extended.

### **要** 把能夠繼承的說明新增到文件中，如果這個類可以繼承。

This is the corollary to the above rule. If you want to allow subclasses of your
class, state that. Suffix the class name with `Base`, or mention it in the
class's doc comment.

該規則是上條規則的結果。如果允許你的類被子類別化，請在文件中說明情況。使用 `Base` 作為類別名稱的字尾，
或者在類別的註釋文件中註明。


### AVOID implementing a class that isn't intended to be an interface.

### **避免** 去實現一個不期望成為介面的類（該類不想作為介面被實現）。

Implicit interfaces are a powerful tool in Dart to avoid having to repeat the
contract of a class when it can be trivially inferred from the signatures of an
implementation of that contract.

隱含介面是Dart中的一個強大工具，當一個類別中可以很容易的推斷出一些已經約定的有特徵的實現時，
隱含介面可以避免重複定義這個類別的約定。

But implementing a class's interface is a very tight coupling to that class. It
means virtually *any* change to the class whose interface you are implementing
will break your implementation. For example, adding a new member to a class is
usually a safe, non-breaking change. But if you are implementing that class's
interface, now your class has a static error because it lacks an implementation
of that new method.

但是透過類別的隱含介面實現的新類，新類會與這個類產生非常緊密的耦合。也就是說，對於介面類別的
*任何*修改，你實現的新類都會被破壞。例如，向類中新增新成員通常是安全，不會產生破壞性的改變。
但是如果你實現了這個類別的介面，那麼現在你的類會產生一個靜態錯誤，因為它缺少了新方法的實現。

Library maintainers need the ability to evolve existing classes without breaking
users. If you treat every class like it exposes an interface that users are free
to implement, then changing those classes becomes very difficult. That
difficulty in turn means the libraries you rely on are slower to grow and adapt
to new needs.

庫的維護人員需要能夠在不破壞使用者程式碼的情況下迭代現有的累。如果把每個類都看待成是暴露給使用者
的介面，使用者可以自由的實現，這時修改這些類將變得非常困難。反過來，這個困難將導致你的函式庫
迭代緩慢，從而無法適應新的需求。

To give the authors of the classes you use more leeway, avoid implementing
implicit interfaces except for classes that are clearly intended to be
implemented. Otherwise, you may introduce a coupling that the author doesn't
intend, and they may break your code without realizing it.

為了給你的類別的開發人員提供更多的餘地，避免實現隱含介面，除非那些類明確需要實現。否則，
你可能會引入開發者沒有預料到的耦合情況，這樣可能會在沒有意識到的情況下破壞你的程式碼。

### DO document if your class supports being used as an interface.

### **要** 對支援介面的類在文件註明

If your class can be used as an interface, mention that in the class's doc
comment.

如果你的類可以被用作介面，那麼將這個情況註明到類別的文件中。


### DO use `mixin` to define a mixin type.

### **要** 對支援 mixin 的類在文件註明

{% include linter-rule-mention.md rule="prefer_mixin" %}

Dart originally didn't have a separate syntax for declaring a class intended to
be mixed in to other classes. Instead, any class that met certain restrictions
(no non-default constructor, no superclass, etc.) could be used as a mixin. This
was confusing because the author of the class might not have intended it to be
mixed in.

Dart 2.1.0 added a `mixin` keyword for explicitly declaring a mixin. Types
created using that can *only* be used as mixins, and the language also ensures
that your mixin stays within the restrictions. When defining a new type that you
intend to be used as a mixin, use this syntax.

{:.good}
<?code-excerpt "design_good.dart (mixin)"?>
{% prettify dart tag=pre+code %}
mixin ClickableMixin implements Control {
  bool _isDown = false;

  void click();

  void mouseDown() {
    _isDown = true;
  }

  void mouseUp() {
    if (_isDown) click();
    _isDown = false;
  }
}
{% endprettify %}

You might still encounter older code using `class` to define mixins, but the new
syntax is preferred.

### AVOID mixing in a type that isn't intended to be a mixin. {#avoid-mixing-in-a-class-that-isnt-intended-to-be-a-mixin}

### **避免** 去 mixin 一個不期望被 mixin 的類 {#avoid-mixing-in-a-class-that-isnt-intended-to-be-a-mixin}

{% include linter-rule-mention.md rule="prefer_mixin" %}

For compatibility, Dart still allows you to mix in classes that aren't defined
using `mixin`. However, that's risky. If the author of the class doesn't intend
the class to be used as a mixin, they might change the class in a way that
breaks the mixin restrictions. For example, if they add a constructor, your
class will break.

If the class doesn't have a doc comment or an obvious name like `IterableMixin`,
assume you cannot mix in the class if it isn't declared using `mixin`.

## Constructors

## 建構函式

Dart constructors are created by declaring a function with the same name as the
class and, optionally, an additional identifier. The latter are called *named
constructors*.

透過宣告與類具有相同名稱的函式以及附加可選的識別符號來建立 Dart 建構函式。 後者附加標示符的
建構函式被稱為*命名建構函式*。

### CONSIDER making your constructor `const` if the class supports it.

### **考慮** 在類支援的情況下，指定建構函式為  `const`。

If you have a class where all the fields are final, and the constructor does
nothing but initialize them, you can make that constructor `const`. That lets
users create instances of your class in places where constants are
required—inside other larger constants, switch cases, default parameter
values, etc.

如果一個類別，它所有的欄位都是 final ，並且構造函數出了初始化他們之外沒有任
何其他操作，那麼可以將其作為 `const` 建構函式。這樣就能夠允許使用者在需要
常量的位置建立類別的例項&mdash;一些大型的常量，switch case 陳述式，預設引數中，
以及其他的情況。

If you don't explicitly make it `const`, they aren't able to do that.

如果沒有顯示的指定為 `const` 建構函式，那麼就無法實現上述目的。

Note, however, that a `const` constructor is a commitment in your public API. If
you later change the constructor to non-`const`, it will break users that are
calling it in constant expressions. If you don't want to commit to that, don't
make it `const`. In practice, `const` constructors are most useful for simple,
immutable value-like types.

但需要注意的是，建構函式被指定為 `const` ，那它就是公共 API 的一中承諾。
如果後面將建構函式更改為非 `const` ，那麼在常量表達式中呼叫它的程式碼就會被破壞。
如果不想做出這樣的承諾，那麼就不要指定它為 `const` 建構函式。在實際運用中，
`const` 建構函式對於簡單的，不可變的資料記錄類是非常有用的。

## Members

## 成員

A member belongs to an object and can be either methods or instance variables.

成員屬於物件，成員可以是方法或例項變數。

### PREFER making fields and top-level variables `final`.

### **推薦** 指定欄位或最上層變數為 `final` 。

{% include linter-rule-mention.md rule="prefer_final_fields" %}

State that is not *mutable*—that does not change over time—is
easier for programmers to reason about. Classes and libraries that minimize the
amount of mutable state they work with tend to be easier to maintain.

狀態 **不可變**&mdash;隨著時間推移狀態不發生變化&mdash;有益於程式設計師推理。
類和庫中可變狀態量越少，類和庫越容易維護。

Of course, it is often useful to have mutable data. But, if you don't need it,
your default should be to make fields and top-level variables `final` when you
can.

當然，可變資料是非常有用的。但是，如果並不需要可變資料，
應該儘可能預設指定欄位和最上層變數為 `final` 。

Sometimes an instance field doesn't change after it has been initialized, but
can't be initialized until after the instance is constructed. For example, it
may need to reference `this` or some other field on the instance. In cases like
that, consider making the field `late final`. When you do, you may also be able
to [initialize the field at its declaration][init at decl].

有時例項的某些欄位在被初始化後不會再變化，但只能在例項被構造後才能被初始化。
例如，某些欄位可能需要參考 `this`。
在這種情況下，請考慮將其宣告為 `late final` 形式。
當這樣聲明後，您也許可以 [在宣告時完成初始化][init at decl]。

[init at decl]: /guides/language/effective-dart/usage#do-initialize-fields-at-their-declaration-when-possible

### DO use getters for operations that conceptually access properties.

### **要** 對概念上是存取的屬性使用 getter 方法。

Deciding when a member should be a getter versus a method is a subtle but
important part of good API design, hence this very long guideline.
Some other language's cultures shy away from getters. They only use them when
the operation is almost exactly like a field—it does a minuscule amount of
calculation on state that lives entirely on the object. Anything more complex or
heavyweight than that gets `()` after the name to signal "computation goin' on
here!" because a bare name after a `.` means "field".

判定一個成員應該是一個 getter 而不是一個方法是一件具有挑戰性的事情。它雖然微妙，但對於好的
API 設計是非常重要的，也導致本規則會很長。其他的一些語言文化中迴避了getter。他們只有在幾乎
類似於欄位存取的時候才會使用&mdash;它僅僅是根據物件的狀態進行微小的計算。任何比這更復雜或
重量級的東西得到帶有 `()` 的名字後面，給出一種"計算的操作在這！"訊號。因為 `.` 後面只跟名稱
意味著是"欄位"。

Dart is *not* like that. In Dart, *all* dotted names are member invocations that
may do computation. Fields are special—they're getters whose
implementation is provided by the language. In other words, getters are not
"particularly slow fields" in Dart; fields are "particularly fast getters".

Dart 與他們 *不* 同。在 Dart 中，所有點名稱都可以是進行計算的成員呼叫。欄位是特殊的&mdash;
欄位的 getter 的實現是有語言提供的。換句話說，在 Dart 中，getter 不是"存取特別慢的欄位"；
欄位是"存取特別快的 getter "。

Even so, choosing a getter over a method sends an important signal to the
caller. The signal, roughly, is that the operation is "field-like". The
operation, at least in principle, *could* be implemented using a field, as far
as the caller knows. That implies:

即便如此，選擇 getter 而不是方法對於呼叫者來說是一個重要訊號。訊號大致的意思成員的操作
"類似於欄位"。至少原則上可以這麼認為，只要呼叫者清楚，這個操作*可以*使用欄位來實現。這意味著：

*   **The operation does not take any arguments and returns a result.**

    **操作返回一個結果但不接受任何引數。**

*   **The caller cares mostly about the result.** If you want the caller to
    worry about *how* the operation produces its result more than they do the
    result being produced, then give the operation a verb name that describes
    the work and make it a method.

    **呼叫者主要關係結果。** 如果希望呼叫者關係操作產生結果的方式多於產生的結果，那麼為操作提供一個方法，使用描述工作的動詞作為方法的名稱。

    This does *not* mean the operation has to be particularly fast in order to
    be a getter. `IterableBase.length` is `O(n)`, and that's OK. It's fine for a
    getter to do significant calculation. But if it does a *surprising* amount
    of work, you may want to draw their attention to that by making it a method
    whose name is a verb describing what it does.

    這並*不*意味著操作必須特別快才能成為 getter 方法。
    `IterableBase.length` 複雜度是 `O(n)`，是可以的。
    使用 getter 方法進行重要計算是沒問題的。
    但是如果它做了 **超** 大量的工作，
    你可能需要透過一個描述其功能的動詞的方法來引起使用者的注意。

    {:.bad}
    {% prettify dart tag=pre+code %}
    connection.nextIncomingMessage; // Does network I/O.
    expression.normalForm; // Could be exponential to calculate.
    {% endprettify %}

*   **The operation does not have user-visible side effects.** Accessing a real
    field does not alter the object or any other state in the program. It
    doesn't produce output, write files, etc. A getter shouldn't do those things
    either.

    **操作不會產生使用者可見的副作用。** 在程式中存取一個實際的欄位不會改變物件或者其他的狀態。
    操作不會產生輸出，寫入檔案等。同樣 getter 方法也一樣。

    The "user-visible" part is important. It's fine for getters to modify hidden
    state or produce out of band side effects. Getters can lazily calculate and
    store their result, write to a cache, log stuff, etc. As long as the caller
    doesn't *care* about the side effect, it's probably fine.

    注意關鍵字"使用者可見"。只要呼叫者不*關心*這些副作用。getter 方法可以修改隱藏狀態或產生
    帶外副作用。 getter 方法可以惰性計算和儲存他們的結果，寫入快取， log 等。這樣是沒有問題的。

    {:.bad}
    {% prettify dart tag=pre+code %}
    stdout.newline; // Produces output.
    list.clear; // Modifies object.
    {% endprettify %}

*   **The operation is *idempotent*.** "Idempotent" is an odd word that, in this
    context, basically means that calling the operation multiple times produces
    the same result each time, unless some state is explicitly modified between
    those calls. (Obviously, `list.length` produces different results if you add
    an element to the list between calls.)

    注意關鍵字"使用者可見"。只要呼叫者不*關心*這些副作用。getter 方法可以修改隱藏狀態或產生
    帶外副作用。 getter 方法可以惰性計算和儲存他們的結果，寫入快取， log 等。這樣是沒有問題的。

    "Same result" here does not mean a getter must literally produce an
    identical object on successive calls. Requiring that would force many
    getters to have brittle caching, which negates the whole point of using a
    getter. It's common, and perfectly fine, for a getter to return a new future
    or list each time you call it. The important part is that the future
    completes to the same value, and the list contains the same elements.

    這裡"相同的結果"並不意味著 getter 方法必須一定要在每次呼叫成功後都返回相同的物件。如果
    按這樣的要求會迫使很過 getter 方法需要進行脆弱的快取 (brittle caching) ，這樣就否定了
    使用 getter 的全部意義。常見的非常好的範例是，每次呼叫一個 getter 方法返回一個新的 
    future 或 list。重點在於， future 完成後返回相同的值，list 包含了相同的元素。

    In other words, the result value should be the same *in the aspects that the
    caller cares about.*

    換句話說，*呼叫者關係的*是結果值應該相等。

    {:.bad}
    {% prettify dart tag=pre+code %}
    DateTime.now; // New result each time.
    {% endprettify %}

*   **The resulting object doesn't expose all of the original object's state.**
    A field exposes only a piece of an object. If your operation returns a
    result that exposes the original object's entire state, it's likely better
    off as a [`to___()`][to] or [`as___()`][as] method.

    **結果物件不用公開所有原始物件的狀態。** 一個欄位僅公開物件的一部分。如果操作返回的結果
    公開了原始物件的整個狀態，那麼把該操作作為 [`to___()`][to] 或 [`as___()`][as] 方法
    可能會更好。

[to]: #prefer-naming-a-method-to___-if-it-copies-the-objects-state-to-a-new-object
[as]: #prefer-naming-a-method-as___-if-it-returns-a-different-representation-backed-by-the-original-object

If all of the above describe your operation, it should be a getter. It seems
like few members would survive that gauntlet, but surprisingly many do. Many
operations just do some computation on some state and most of those can and
should be getters.

如果操作符合上述描述，那麼它應該是一個 getter 方法。看似滿足這一系列要求的成員並不多，但實際上
會超出你的想象。許多操作只是對某些狀態進行一些計算，其中大多數能夠，並且也應該作為 getter 方法。

{:.good}
{% prettify dart tag=pre+code %}
rectangle.area;
collection.isEmpty;
button.canShow;
dataSet.minimumValue;
{% endprettify %}

### DO use setters for operations that conceptually change properties.

### **要** 對概念上是修改的屬性使用 setter 方法。

{% include linter-rule-mention.md rule="use_setters_to_change_properties" %}

Deciding between a setter versus a method is similar to deciding between a
getter versus a method. In both cases, the operation should be "field-like".

判定一個成員應該是一個 setter 而不是一個方法與 getter 的判定一樣。兩者的操作都應該是
"類似於欄位"的操作。

For a setter, "field-like" means:

對於 setter 方法，"類似於欄位"意味著：

*   **The operation takes a single argument and does not produce a result
    value.**

    **操作只有一個引數，不會返回結果。**

*   **The operation changes some state in the object.**

    **操作會更改物件中的某些狀態。**

*   **The operation is idempotent.** Calling the same setter twice with the same
    value should do nothing the second time as far as the caller is concerned.
    Internally, maybe you've got some cache invalidation or logging going on.
    That's fine. But from the caller's perspective, it appears that the second
    call does nothing.

    **操作是*冪等*的。** 使用相同的值呼叫相同的 setter 兩次，就呼叫者而言，第二次不應該執
    行任何操作。在內部，也許你會得到一些無效的快取或者多次的日誌記錄。沒關係，從呼叫者的角度
    來看，第二次呼叫似乎沒做任何事情。

{:.good}
{% prettify dart tag=pre+code %}
rectangle.width = 3;
button.visible = false;
{% endprettify %}


### DON'T define a setter without a corresponding getter.

### **不要** 在沒有對應的 getter 的情況下定義 setter。

{% include linter-rule-mention.md rule="avoid_setters_without_getters" %}

Users think of getters and setters as visible properties of an object. A
"dropbox" property that can be written to but not seen is confusing and
confounds their intuition about how properties work. For example, a setter
without a getter means you can use `=` to modify it, but not `+=`.

使用者將 getter 和 setter 視為一個物件的可見屬性。一個 "dropbox" 屬性可以被寫入但無法讀
取，會令人感到困惑。並且也混淆了他們對屬性如何工作的直觀理解。 例如，沒有 getter 的 setter 
意味著你可以使用 `=` 來修改它，但卻不能使用 `+=` 。

This guideline does *not* mean you should add a getter just to permit the setter
you want to add. Objects shouldn't generally expose more state than they need
to. If you have some piece of an object's state that can be modified but not
exposed in the same way, use a method instead.

本規則意義並 **不是** 說，你需要先新增一個 getter 才被允許新增 setter ，
物件通常不應該暴露出多餘的狀態。
如果某個物件的某個狀態可以修改但不能以相同的方式存取，請改用方法實現。

### AVOID using runtime type tests to fake overloading.

It's common for an API to support similar operations
on different types of parameters.
To emphasize the similarity, some languages support *overloading*,
which lets you define multiple methods
that have the same name but different parameter lists.
At compile time, the compiler looks at the actual argument types to determine
which method to call.

Dart doesn't have overloading.
You can define an API that looks like overloading
by defining a single method and then using `is` type tests
inside the body to look at the runtime types of the arguments and perform the
appropriate behavior.
However, faking overloading this way turns a *compile time* method selection
into a choice that happens at *runtime*.

If callers usually know which type they have
and which specific operation they want,
it's better to define separate methods with different names
to let callers select the right operation.
This gives better static type checking and faster performance
since it avoids any runtime type tests.

However, if users might have an object of an unknown type
and *want* the API to internally use `is` to pick the right operation,
then a single method where the parameter is a supertype
of all of the supported types might be reasonable.

### AVOID public `late final` fields without initializers.

Unlike other `final` fields, a `late final` field without an initializer *does*
define a setter. If that field is public, then the setter is public. This is
rarely what you want. Fields are usually marked `late` so that they can be
initialized *internally* at some point in the instance's lifetime, often inside
the constructor body.

Unless you *do* want users to call the setter, it's better to pick one of the
following solutions:

* Don't use `late`.
* Use a factory constructor to compute the `final` field values.
* Use `late`, but initialize the `late` field at its declaration.
* Use `late`, but make the `late` field private and define a public getter for it.


### AVOID returning nullable `Future`, `Stream`, and collection types.

When an API returns a container type, it has two ways to indicate the absence of
data: It can return an empty container or it can return `null`. Users generally
assume and prefer that you use an empty container to indicate "no data". That
way, they have a real object that they can call methods on like `isEmpty`.

To indicate that your API has no data to provide, prefer returning an empty
collection, a non-nullable future of a nullable type, or a stream that doesn't
emit any values.

**Exception:** If returning `null` *means something different* from yielding an
empty container, it might make sense to use a nullable type.

如果確實有成員可能返回 `null` 的型別，請在文件中註明，以及在什麼情況下回返回 `null`。 

### AVOID returning `this` from methods just to enable a fluent interface.

### **避免** 為了書寫流暢，而從方法中返回 `this` 。

{% include linter-rule-mention.md rule="avoid_returning_this" %}

Method cascades are a better solution for chaining method calls.

方法級聯是連結方法呼叫的更好的解決方式。

{:.good}
<?code-excerpt "design_good.dart (cascades)"?>
{% prettify dart tag=pre+code %}
var buffer = StringBuffer()
  ..write('one')
  ..write('two')
  ..write('three');
{% endprettify %}

{:.bad}
<?code-excerpt "design_bad.dart (cascades)"?>
{% prettify dart tag=pre+code %}
var buffer = StringBuffer()
    .write('one')
    .write('two')
    .write('three');
{% endprettify %}


## Types

## 型別

When you write down a type in your program, you constrain the kinds of values
that flow into different parts of your code. Types can appear in two kinds of
places: *type annotations* on declarations and type arguments to *generic
invocations*.

程式中的型別用於約束流入程式碼各位置的 **值** 的不同型別。
型別會出現在兩種位置：
宣告中的 **型別註解 (type annotations) ** 和
**泛型呼叫 (generic invocations) ** 的型別引數。

Type annotations are what you normally think of when you think of "static
types". You can type annotate a variable, parameter, field, or return type. In
the following example, `bool` and `String` are type annotations. They hang off
the static declarative structure of the code and aren't "executed" at runtime.

當你想到 **靜態型別** 時，通常會聯想到型別註解。
型別註解可以用於為變數，引數，欄位，或者返回值宣告型別。
在下面的範例中，`bool` 和 `String` 是型別註解。
他們位於程式碼靜態宣告結構的前面，並且他們不會在執行時"執行"。

<?code-excerpt "design_good.dart (annotate-declaration)"?>
{% prettify dart tag=pre+code %}
bool isEmpty(String parameter) {
  bool result = parameter.isEmpty;
  return result;
}
{% endprettify %}

A generic invocation is a collection literal, a call to a generic class's
constructor, or an invocation of a generic method. In the next example, `num`
and `int` are type arguments on generic invocations. Even though they are types,
they are first-class entities that get reified and passed to the invocation at
runtime.

泛型呼叫可以是一個字面量集合的定義，一個泛型類建構函式的呼叫，或者一個泛型方法的呼叫。在下面
的範例中，`num` 和 `int` 都是泛型呼叫的型別引數。雖然它們是型別，但是它們也是第一類實體，
在執行時會被提升並傳遞給呼叫。

<?code-excerpt "design_good.dart (annotate-invocation)"?>
{% prettify dart tag=pre+code %}
var lists = <num>[1, 2];
lists.addAll(List<num>.filled(3, 4));
lists.cast<int>();
{% endprettify %}

We stress the "generic invocation" part here, because type arguments can *also*
appear in type annotations:

這裡再強調一下"泛型呼叫"，因為型別引數 **也** 可以出現在型別註解中：

<?code-excerpt "design_good.dart (annotate-type-arg)"?>
{% prettify dart tag=pre+code %}
List<int> ints = [1, 2];
{% endprettify %}

Here, `int` is a type argument, but it appears inside a type annotation, not a
generic invocation. You usually don't need to worry about this distinction, but
in a couple of places, we have different guidance for when a type is used in a
generic invocation as opposed to a type annotation.

這裡，`int` 是一個類別型引數，但它出現在了型別註解中，而不是泛型呼叫。通常來說不需要擔心這種情況，
但在幾個地方，對於型別的運用是泛型呼叫而不是型別註解有不同的指導。

#### Type inference

Type annotations are optional in Dart.
If you omit one, Dart tries to infer a type
based on the nearby context. Sometimes it doesn't have enough information to
infer a complete type. When that happens, Dart sometimes reports an error, but
usually silently fills in any missing parts with `dynamic`. The implicit
`dynamic` leads to code that *looks* inferred and safe, but actually disables
type checking completely. The rules below avoid that by requiring types when
inference fails.

The fact that Dart has both type inference and a `dynamic` type leads to some
confusion about what it means to say code is "untyped". Does that mean the code
is dynamically typed, or that you didn't *write* the type? To avoid that
confusion, we avoid saying "untyped" and instead use the following terminology:

在大多數地方，Dart 允許省略型別註解並根據附近的上下文提供推斷型別，或預設指定為 `dynamic` 
型別。Dart 同時具有型別推斷和 `dynamic` 型別的情況，導致對程式碼中 "untyped" 的含義產生一些
混淆。意思就是不*寫*型別就是動態型別嗎？為避免這種混淆，應該避免說 "untyped" ，而是使用以下術語：

*   If the code is *type annotated*, the type was explicitly written in the
    code.

    如果程式碼是*型別註解*，則在程式碼中顯式寫入型別。

*   If the code is *inferred*, no type annotation was written, and Dart
    successfully figured out the type on its own. Inference can fail, in which
    case the guidelines don't consider that inferred.

    如果程式碼的型別是*推斷*的，則不必寫型別註解，Dart 會自己會找出它的型別。規則不考慮推斷可能
    會失敗的情況，在一些地方，推理失敗會產生一個靜態錯誤。在其他情況下，Dart 使用 `dynamic`
    作為備選型別。

*   If the code is *dynamic*, then its static type is the special `dynamic`
    type. Code can be explicitly annotated `dynamic` or it can be inferred.

    如果程式碼是*動態型別*，那麼它的靜態型別就是特殊的 `dynamic` 型別。程式碼可以明確地註解為 
    `dynamic` 型別，也可以由 Dart 進行推斷。

In other words, whether some code is annotated or inferred is orthogonal to
whether it is `dynamic` or some other type.

換句話說，對於程式碼的型別是 `dynamic` 型別還是其他型別，在型別註解或型別推斷中是正交的。

Inference is a powerful tool to spare you the effort of writing and reading
types that are obvious or uninteresting. It keeps the reader's attention focused
on the behavior of the code itself. Explicit types are also a key part of
robust, maintainable code. They define the static shape of an API and create
boundaries to document and enforce what kinds of values are allowed to reach
different parts of the program.

Of course, inference isn't magic. Sometimes inference succeeds and selects a
type, but it's not the type you want. The common case is inferring an overly
precise type from a variable's initializer when you intend to assign values of
other types to the variable later. In those cases, you have to write the type
explicitly.

The guidelines here strike the best balance we've found between brevity and
control, flexibility and safety. There are specific guidelines to cover all the
various cases, but the rough summary is:

*   Do annotate when inference doesn't have enough context, even when `dynamic`
    is the type you want.

*   Don't annotate locals and generic invocations unless you need to.

*   Prefer annotating top-level variables and fields unless the initializer
    makes the type obvious.

### DO type annotate variables without initializers.

{% include linter-rule-mention.md rule="prefer_typing_uninitialized_variables" %}

The type of a variable—top-level, local, static field, or instance
field—can often be inferred from its initializer. However, if there is no
initializer, inference fails.

{:.good}
<?code-excerpt "design_good.dart (uninitialized-local)"?>
{% prettify dart tag=pre+code %}
List<AstNode> parameters;
if (node is Constructor) {
  parameters = node.signature;
} else if (node is Method) {
  parameters = node.parameters;
}
{% endprettify %}

{:.bad}
<?code-excerpt "design_bad.dart (uninitialized-local)"?>
{% prettify dart tag=pre+code %}
var parameters;
if (node is Constructor) {
  parameters = node.signature;
} else if (node is Method) {
  parameters = node.parameters;
}
{% endprettify %}

其餘指南涵蓋了和型別有關的其他具體問題。


### DO type annotate fields and top-level variables if the type isn't obvious.

### **推薦** 為型別不明顯的公共欄位和公共最上層變數指定型別註解。

{% include linter-rule-mention.md rule="type_annotate_public_apis" %}

Type annotations are important documentation for how a library should be used.
They form boundaries between regions of a program to isolate the source of a
type error. Consider:

型別註解是關於如何使用庫的重要文件。它們在程式的區域之間形成邊界以隔離型別錯誤來源。思考下面程式碼：

{:.bad}
<?code-excerpt "design_bad.dart (type_annotate_public_apis)"?>
{% prettify dart tag=pre+code %}
install(id, destination) => ...
{% endprettify %}

Here, it's unclear what `id` is. A string? And what is `destination`? A string
or a `File` object? Is this method synchronous or asynchronous? This is clearer:

在這裡，無法判斷：這個 `id` 是什麼，一個字串？`destination` 又是什麼，一個字串還是一個 
`File` 物件？方法是同步的還是非同步的？下面的例項會清晰很多：

{:.good}
<?code-excerpt "design_good.dart (type_annotate_public_apis)"?>
{% prettify dart tag=pre+code %}
Future<bool> install(PackageId id, String destination) => ...
{% endprettify %}

In some cases, though, the type is so obvious that writing it is pointless:

但在一些情況下，型別非常明顯，根本沒有指明型別的必要：

{:.good}
<?code-excerpt "design_good.dart (inferred)"?>
{% prettify dart tag=pre+code %}
const screenWidth = 640; // Inferred as int.
{% endprettify %}

"Obvious" isn't precisely defined, but these are all good candidates:

這裡的"明顯"並沒有精確的定義，下面這些可以作為很好的參考：

* Literals.

  字面量。

* Constructor invocations.

  建構函式呼叫。

* References to other constants that are explicitly typed.

  參考的其他型別明確的常量。

* Simple expressions on numbers and strings.

  數字和字串的簡單表示式。

* Factory methods like `int.parse()`, `Future.wait()`, etc. that readers are
  expected to be familiar with.

  讀者熟悉的工廠方法，如 `int.parse()`， `Future.wait()` 等。

If you think the initializer expression—whatever it is—is
sufficiently clear, then you may omit the annotation. But if you think
annotating helps make the code clearer, then add one.

如果你認為初始化表示式&mdash;無論是什麼表示式&mdash;足夠清晰，
那麼可以省略它的註解。但是如果你認為註解有助於使程式碼更清晰，
那麼你應該加上這個註解。

When in doubt, add a type annotation. Even when a type is obvious, you may still
wish to explicitly annotate. If the inferred type relies on values or
declarations from other libraries, you may want to type annotate *your*
declaration so that a change to that other library doesn't silently change the
type of your own API without you realizing.

如有疑問，請新增型別註解。即使型別很明顯，但可能任然希望明確的註解。如果推斷型別依賴於其他庫中的值
或宣告，可能需要添加註解的宣告。這樣自己的API就不會因為其他庫的修改而被悄無聲息的改變了型別。

This rule applies to both public and private declarations. Just as type
annotations on APIs help *users* of your code, types on private members help
*maintainers*.

這條規則同時適用於公有和私有宣告。
就像 API 裡的型別註釋可以更好幫助程式碼的 **使用者**，
私有成員上的型別可以幫助 **維護者**。

### DON'T redundantly type annotate initialized local variables.

### **避免** 為初始化的區域變數新增冗餘地型別註解。

{% include linter-rule-mention.md rule="omit_local_variable_types" %}

Local variables, especially in modern code where functions tend to be small,
have very little scope. Omitting the type focuses the reader's attention on the
more important *name* of the variable and its initialized value.

區域變數，特別是現代的函式往往很少，範圍也很小。
省略區域變數型別會將讀者的注意力集中在變數的 **名稱** 及初始化值上。

{:.good}
<?code-excerpt "design_good.dart (omit-types-on-locals)"?>
{% prettify dart tag=pre+code %}
List<List<Ingredient>> possibleDesserts(Set<Ingredient> pantry) {
  var desserts = <List<Ingredient>>[];
  for (final recipe in cookbook) {
    if (pantry.containsAll(recipe)) {
      desserts.add(recipe);
    }
  }

  return desserts;
}
{% endprettify %}

{:.bad}
<?code-excerpt "design_bad.dart (omit-types-on-locals)"?>
{% prettify dart tag=pre+code %}
List<List<Ingredient>> possibleDesserts(Set<Ingredient> pantry) {
  List<List<Ingredient>> desserts = <List<Ingredient>>[];
  for (final List<Ingredient> recipe in cookbook) {
    if (pantry.containsAll(recipe)) {
      desserts.add(recipe);
    }
  }

  return desserts;
}
{% endprettify %}

Sometimes the inferred type is not the type you want the variable to have. For
example, you may intend to assign values of other types later. In that case,
annotate the variable with the type you want.

{:.good}
<?code-excerpt "design_good.dart (upcast-local)" replace="/Widget result/[!Widget!] result/g"?>
{% prettify dart tag=pre+code %}
Widget build(BuildContext context) {
  [!Widget!] result = Text('You won!');
  if (applyPadding) {
    result = Padding(padding: EdgeInsets.all(8.0), child: result);
  }
  return result;
}
{% endprettify %}


### DO annotate return types on function declarations.

Dart doesn't generally infer the return type of a function declaration from its body,
unlike some other languages. That means you should write a type annotation for
the return type yourself.

如果區域變數沒有初始值設定項，那麼就無法判斷它的型別了。
這種情況下，最好是為變數加上型別註解。
否則，你的到的會是一個 `dynamic` 型別，並失去靜態型別的好處。


{:.good}
<?code-excerpt "design_good.dart (annotate-return-types)"?>
{% prettify dart tag=pre+code %}
String makeGreeting(String who) {
  return 'Hello, $who!';
}
{% endprettify %}

{:.bad}
<?code-excerpt "design_bad.dart (annotate-return-types)"?>
{% prettify dart tag=pre+code %}
makeGreeting(String who) {
  return 'Hello, $who!';
}
{% endprettify %}

Note that this guideline only applies to *named* function declarations:
top-level functions, methods, and local functions. Anonymous function
expressions infer a return type from their body. In fact, the syntax doesn't
even allow a return type annotation.

### DO annotate parameter types on function declarations.

A function's parameter list determines its boundary to the outside world.
Annotating parameter types makes that boundary well defined.
Note that even though default parameter values look like variable initializers,
Dart doesn't infer an optional parameter's type from its default value.

{:.good}
<?code-excerpt "design_good.dart (annotate-parameters)"?>
{% prettify dart tag=pre+code %}
void sayRepeatedly(String message, {int count = 2}) {
  for (var i = 0; i < count; i++) {
    print(message);
  }
}
{% endprettify %}

{:.bad}
<?code-excerpt "design_bad.dart (annotate-parameters)" replace="/\(count as num\)/count/g"?>
{% prettify dart tag=pre+code %}
void sayRepeatedly(message, {count = 2}) {
  for (var i = 0; i < count; i++) {
    print(message);
  }
}
{% endprettify %}

**Exception:** Function expressions and initializing formals have different type annotation conventions, as described in the next two guidelines.


### DON'T annotate inferred parameter types on function expressions.

### **避免** 在函式表示式上註解推斷的引數型別。

{% include linter-rule-mention.md rule="avoid_types_on_closure_parameters" %}

Anonymous functions are almost always immediately passed to a method taking a
callback of some type.
When a function expression is created in a typed context,
Dart tries to infer the function's parameter types based on the expected type.

匿名函式幾乎都是作為一個回呼(Callback)引數型別立即傳遞給一個方法。
當在型別化上下文中建立函式表示式時，Dart 會嘗試根據預期型別來推斷
函式的引數型別。

For example, when you pass a function expression to `Iterable.map()`, your
function's parameter type is inferred based on the type of callback that `map()`
expects:

例如，當為 `Iterable.map()` 傳遞一個函式表示式時，函式的引數型別會根據 `map()` 回呼(Callback)中所
期望的型別進行推斷。

{:.good}
<?code-excerpt "design_good.dart (func-expr-no-param-type)"?>
{% prettify dart tag=pre+code %}
var names = people.map((person) => person.name);
{% endprettify %}

{:.bad}
<?code-excerpt "design_bad.dart (func-expr-no-param-type)"?>
{% prettify dart tag=pre+code %}
var names = people.map((Person person) => person.name);
{% endprettify %}

If the language is able to infer the type you want for a parameter in a function
expression, then don't annotate. In rare cases, the surrounding
context isn't precise enough to provide a type for one or more of the
function's parameters. In those cases, you may need to annotate.
(If the function isn't used immediately, it's usually better to
[make it a named declaration][named local].)

[named local]: usage#do-use-a-function-declaration-to-bind-a-function-to-a-name

### DON'T type annotate initializing formals.

{% include linter-rule-mention.md rule="type_init_formals" %}

If a constructor parameter is using `this.` to initialize a field, 
or `super.` to forward a super parameter, 
then the type of the parameter
is inferred to have the same type as 
the field or super-constructor parameter respectively.

{:.good}
<?code-excerpt "design_good.dart (dont-type-init-formals)"?>
{% prettify dart tag=pre+code %}
class Point {
  double x, y;
  Point(this.x, this.y);
}

class MyWidget extends StatelessWidget {
  MyWidget({super.key});
}
{% endprettify %}

{:.bad}
<?code-excerpt "design_bad.dart (dont-type-init-formals)"?>
{% prettify dart tag=pre+code %}
class Point {
  double x, y;
  Point(double this.x, double this.y);
}

class MyWidget extends StatelessWidget {
  MyWidget({Key? super.key});
}
{% endprettify %}


### DO write type arguments on generic invocations that aren't inferred.

Dart is pretty smart about inferring type arguments in generic invocations. It
looks at the expected type where the expression occurs and the types of
values being passed to the invocation. However, sometimes those aren't enough to
fully determine a type argument. In that case, write the entire type argument
list explicitly.

在其他情況下，如果沒有足夠的資訊來推斷型別時，應該為引數新增型別註解：

{:.good}
<?code-excerpt "design_good.dart (non-inferred-type-args)"?>
{% prettify dart tag=pre+code %}
var playerScores = <String, int>{};
final events = StreamController<Event>();
{% endprettify %}

{:.bad}
<?code-excerpt "design_bad.dart (non-inferred-type-args)"?>
{% prettify dart tag=pre+code %}
var playerScores = {};
final events = StreamController();
{% endprettify %}

Sometimes the invocation occurs as the initializer to a variable declaration. If
the variable is *not* local, then instead of writing the type argument list on the
invocation itself, you may put a type annotation on the declaration:

{:.good}
<?code-excerpt "design_good.dart (inferred-type-args)"?>
{% prettify dart tag=pre+code %}
class Downloader {
  final Completer<String> response = Completer();
}
{% endprettify %}

{:.bad}
<?code-excerpt "design_bad.dart (inferred-type-args)"?>
{% prettify dart tag=pre+code %}
class Downloader {
  final response = Completer();
}
{% endprettify %}

Annotating the variable also addresses this guideline because now the type
arguments *are* inferred.

在這裡，由於變數沒有型別註解，因此沒有足夠的上下文來確定建立的 `Set` 是什麼型別，因此應該顯式
的提供引數型別。

### DON'T write type arguments on generic invocations that are inferred.

This is the converse of the previous rule. If an invocation's type argument list
*is* correctly inferred with the types you want, then omit the types and let
Dart do the work for you.

{:.good}
<?code-excerpt "design_good.dart (redundant)"?>
{% prettify dart tag=pre+code %}
class Downloader {
  final Completer<String> response = Completer();
}
{% endprettify %}

{:.bad}
<?code-excerpt "design_bad.dart (redundant)"?>
{% prettify dart tag=pre+code %}
class Downloader {
  final Completer<String> response = Completer<String>();
}
{% endprettify %}

Here, the type annotation on the field provides a surrounding context to infer
the type argument of constructor call in the initializer.

{:.good}
<?code-excerpt "design_good.dart (explicit)"?>
{% prettify dart tag=pre+code %}
var items = Future.value([1, 2, 3]);
{% endprettify %}

{:.bad}
<?code-excerpt "design_bad.dart (explicit)"?>
{% prettify dart tag=pre+code %}
var items = Future<List<int>>.value(<int>[1, 2, 3]);
{% endprettify %}

Here, the types of the collection and instance can be inferred bottom-up from
their elements and arguments.


### AVOID writing incomplete generic types.

The goal of writing a type annotation or type argument is to pin down a complete
type. However, if you write the name of a generic type but omit its type
arguments, you haven't fully specified the type. In Java, these are called "raw
types". For example:

{:.bad}
<?code-excerpt "design_bad.dart (incomplete-generic)" replace="/List|Map/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
[!List!] numbers = [1, 2, 3];
var completer = Completer<[!Map!]>();
{% endprettify %}

Here, `numbers` has a type annotation, but the annotation doesn't provide a type
argument to the generic `List`. Likewise, the `Map` type argument to `Completer`
isn't fully specified. In cases like this, Dart will *not* try to "fill in" the
rest of the type for you using the surrounding context. Instead, it silently
fills in any missing type arguments with `dynamic` (or the bound if the
class has one). That's rarely what you want.

Instead, if you're writing a generic type either in a type annotation or as a type
argument inside some invocation, make sure to write a complete type:

{:.good}
<?code-excerpt "design_good.dart (incomplete-generic)"?>
{% prettify dart tag=pre+code %}
List<num> numbers = [1, 2, 3];
var completer = Completer<Map<String, int>>();
{% endprettify %}


### DO annotate with `dynamic` instead of letting inference fail.

### **推薦** 使用 `dynamic` 註解替換推斷失敗的情況。

When inference doesn't fill in a type, it usually defaults to `dynamic`. If
`dynamic` is the type you want, this is technically the most terse way to get
it. However, it's not the most *clear* way. A casual reader of your code who
sees that an annotation is missing has no way of knowing if you intended it to be
`dynamic`, expected inference to fill in some other type, or simply forgot to
write the annotation.

When `dynamic` is the type you want, write that explicitly to make your intent
clear and highlight that this code has less static safety.

{:.good}
<?code-excerpt "design_good.dart (prefer-dynamic)"?>
{% prettify dart tag=pre+code %}
dynamic mergeJson(dynamic original, dynamic changes) => ...
{% endprettify %}

{:.bad}
<?code-excerpt "design_bad.dart (prefer-dynamic)"?>
{% prettify dart tag=pre+code %}
mergeJson(original, changes) => ...
{% endprettify %}

Note that it's OK to omit the type when Dart *successfully* infers `dynamic`.

{:.good}
<?code-excerpt "design_good.dart (infer-dynamic)"?>
{% prettify dart tag=pre+code %}
Map<String, dynamic> readJson() => ...

void printUsers() {
  var json = readJson();
  var users = json['users'];
  print(users);
}
{% endprettify %}

Here, Dart infers `Map<String, dynamic>` for `json` and then from that infers
`dynamic` for `users`. It's fine to leave `users` without a type annotation. The
distinction is a little subtle. It's OK to allow inference to *propagate*
`dynamic` through your code from a `dynamic` type annotation somewhere else, but
you don't want it to inject a `dynamic` type annotation in a place where your
code did not specify one.

{{site.alert.info}}

  Before Dart 2, this guideline stated the exact opposite: 
  *don't* annotate with `dynamic` when it is implicit. 
  With the new stronger type system and type inference, 
  users now expect Dart to behave like an inferred statically-typed language. 
  With that mental model, 
  it is an unpleasant surprise to discover that
  a region of code has silently lost all of the
  safety and performance of static types.

  在 Dart 2 之前，本規則恰恰是相反的：**不要** 為隱性型別的成員指定 `dynamic` 註解。
  基於強型別系統和型別推斷，現在的開發者更希望 Dart 的行為類似於推斷的靜態型別語言。
  基於這種心理模型，我們發現程式碼區域慢慢地失去了靜態型別所具有的安全及效能。

{{site.alert.end}}

**Exception**: Type annotations on unused parameters (`_`) can be omitted.

### PREFER signatures in function type annotations.

### **推薦** 使 function 型別註解的特徵更明顯

The identifier `Function` by itself without any return type or parameter
signature refers to the special [Function][] type. This type is only
marginally more useful than using `dynamic`. If you're going to annotate, prefer
a full function type that includes the parameters and return type of the
function.

成員型別註解識別符號只有 `Function` ，註解識別符號不包括任何返回值型別或引數型別，請參考
專門的 [Function][] 型別說明。使用 `Function` 型別要稍微比使用 `dynamic` 更好些。
如果要使用 `Function` 來進行型別註解，註解型別應該包含函式的所有引數及返回值型別。

[Function]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Function-class.html

{:.good}
<?code-excerpt "design_good.dart (avoid-Function)" replace="/bool Function(\(.*?\))?/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
bool isValid(String value, [!bool Function(String)!] test) => ...
{% endprettify %}

{:.bad}
<?code-excerpt "design_bad.dart (avoid-Function)" replace="/Function/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
bool isValid(String value, [!Function!] test) => ...
{% endprettify %}

[fn syntax]: #prefer-inline-function-types-over-typedefs

**Exception:** Sometimes, you want a type that represents the union of multiple
different function types. For example, you may accept a function that takes one
parameter or a function that takes two. Since we don't have union types, there's
no way to precisely type that and you'd normally have to use `dynamic`.
`Function` is at least a little more helpful than that:

此條規則有個例外，如果期望一個類別型能夠表示多種函式型別的集合。例如，我們希望接受的可能是一個引數
的函式，也可能是兩個引數的函式。由於 Dart 沒有集合型別，所以沒有辦法為類似成員精確的指定型別，
這個時候通常只能使用 `dynamic`。但這裡使用 `Function` 要稍微比使用 `dynamic` 更有幫助些：

{:.good}
<?code-excerpt "design_good.dart (function-arity)" replace="/(void )?Function(\(.*?\))?/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
void handleError([!void Function()!] operation, [!Function!] errorHandler) {
  try {
    operation();
  } catch (err, stack) {
    if (errorHandler is [!Function(Object)!]) {
      errorHandler(err);
    } else if (errorHandler is [!Function(Object, StackTrace)!]) {
      errorHandler(err, stack);
    } else {
      throw ArgumentError('errorHandler has wrong signature.');
    }
  }
}
{% endprettify %}


### DON'T specify a return type for a setter.

### **不要** 為 setter 方法指定返回型別。

{% include linter-rule-mention.md rule="avoid_return_types_on_setters" %}

Setters always return `void` in Dart. Writing the word is pointless.

在 Dart 中，setter 永遠返回 `void` 。為 setter 指定型別沒有意義。

{:.bad}
<?code-excerpt "design_bad.dart (avoid_return_types_on_setters)"?>
{% prettify dart tag=pre+code %}
void set foo(Foo value) { ... }
{% endprettify %}

{:.good}
<?code-excerpt "design_good.dart (avoid_return_types_on_setters)"?>
{% prettify dart tag=pre+code %}
set foo(Foo value) { ... }
{% endprettify %}


### DON'T use the legacy typedef syntax.

### **不要** 使用棄用的 typedef 語法。

{% include linter-rule-mention.md rule="prefer_generic_function_type_aliases" %}

Dart has two notations for defining a named typedef for a function type. The
original syntax looks like:

Dart 有兩種為函式型別定義命名 typedef 註解語法。 原始語法如下：

{:.bad}
<?code-excerpt "design_bad.dart (old-typedef)"?>
{% prettify dart tag=pre+code %}
typedef int Comparison<T>(T a, T b);
{% endprettify %}

That syntax has a couple of problems:

該語法有幾個問題：

*   There is no way to assign a name to a *generic* function type. In the above
    example, the typedef itself is generic. If you reference `Comparison` in
    your code, without a type argument, you implicitly get the function type
    `int Function(dynamic, dynamic)`, *not* `int Function<T>(T, T)`. This
    doesn't come up in practice often, but it matters in certain corner cases.

    無法為一個*泛型*函式型別指定名稱。在上面的例子中，typedef 自己就是泛型。如果在程式碼中去
    參考 `Comparison` 卻不指定引數型別，那麼你會隱含的得到一個 `int Function(dynamic, dynamic)`
    型別的函式，*而不是* `int Function<T>(T, T)` 。在實際應用中雖然不常用，但是在極少數
    情況下是很重要的。

*   A single identifier in a parameter is interpreted as the parameter's *name*,
    not its *type*. Given:

    引數中的單個識別符號會被認為是引數名稱，而不是引數型別。參考下面程式碼：

    {:.bad}
    <?code-excerpt "design_bad.dart (typedef-param)"?>
    {% prettify dart tag=pre+code %}
    typedef bool TestNumber(num);
    {% endprettify %}

    Most users expect this to be a function type that takes a `num` and returns
    `bool`. It is actually a function type that takes *any* object (`dynamic`)
    and returns `bool`. The parameter's *name* (which isn't used for anything
    except documentation in the typedef) is "num". This has been a
    long-standing source of errors in Dart.

    大多數使用者希望這是一個接受 `num` 返回 `bool` 的函式型別。但它實際上是一個接受*任何*
    物件（`dynamic`）返回 `bool` 的型別。 "num" 是引數*名稱*（ 它除了被用在 typedef 的
    宣告程式碼中，再也沒有其他作用）。這個錯誤在 Dart 中存在了很長時間。

The new syntax looks like this:

新語法如下所示：

{:.good}
<?code-excerpt "design_good.dart (new-typedef)"?>
{% prettify dart tag=pre+code %}
typedef Comparison<T> = int Function(T, T);
{% endprettify %}

If you want to include a parameter's name, you can do that too:

如果想在方法中包含引數名稱，可以這樣做：

{:.good}
<?code-excerpt "design_good.dart (new-typedef-param-name)"?>
{% prettify dart tag=pre+code %}
typedef Comparison<T> = int Function(T a, T b);
{% endprettify %}

The new syntax can express anything the old syntax could express and more, and
lacks the error-prone misfeature where a single identifier is treated as the
parameter's name instead of its type. The same function type syntax after the
`=` in the typedef is also allowed anywhere a type annotation may appear, giving
us a single consistent way to write function types anywhere in a program.

新語法可以表達舊語法所表達的任何內容，並且避免了單個識別符號會被認為是引數型別的常見錯誤。同一個函式
型別語法（typedef 中 `=` 之後的部分）允許出現在任何型別註解可以能出現的地方。這樣在程式的任何位置，
我們都可以以一致的方式來書寫函式型別。

The old typedef syntax is still supported to avoid breaking existing code, but
it's deprecated.

為了避免對已有程式碼產生破壞， typedef 的舊語法依舊支援。但已被棄用。


### PREFER inline function types over typedefs.

### **推薦** 優先使用行內函數型別，而後是 typedef。

{% include linter-rule-mention.md rule="avoid_private_typedef_functions" %}

In Dart 1, if you wanted to use a function type for a field, variable, or
generic type argument, you had to first define a typedef for it. Dart 2 supports
a function type syntax that can be used anywhere a type annotation is allowed:

在 Dart 1 中，如果要在欄位，變數或泛型引數中使用函式型別，首選需要使用 typedef 定義這個型別。
Dart 2 中任何使用型別註解的地方都可以使用函式型別宣告語法：

{:.good}
<?code-excerpt "design_good.dart (function-type)"  replace="/(bool|void) Function\(Event\)/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
class FilteredObservable {
  final [!bool Function(Event)!] _predicate;
  final List<[!void Function(Event)!]> _observers;

  FilteredObservable(this._predicate, this._observers);

  [!void Function(Event)!]? notify(Event event) {
    if (!_predicate(event)) return null;

    [!void Function(Event)!]? last;
    for (final observer in _observers) {
      observer(event);
      last = observer;
    }

    return last;
  }
}
{% endprettify %}

It may still be worth defining a typedef if the function type is particularly
long or frequently used. But in most cases, users want to see what the function
type actually is right where it's used, and the function type syntax gives them
that clarity.

如果函式型別特別長或經常使用，那麼還是有必要使用 typedef 進行定義。但在大多數情況下，使用者
更希望知道函式使用時的真實型別，這樣函式型別語法使它們清晰。


### PREFER using function type syntax for parameters.

### **考慮** 在引數上使用函式型別語法。

{% include linter-rule-mention.md rule="use_function_type_syntax_for_parameters" %}

Dart has a special syntax when defining a parameter whose type is a function.
Sort of like in C, you surround the parameter's name with the function's return
type and parameter signature:

在定義引數為函式型別時，Dart 具有特殊的語法。與 C 類似，使用引數名稱作為函式引數的函式名：

<?code-excerpt "design_bad.dart (function-type-param)"?>
{% prettify dart tag=pre+code %}
Iterable<T> where(bool predicate(T element)) => ...
{% endprettify %}

Before Dart 2 added function type syntax, this was the only way to give a
parameter a function type without defining a typedef. Now that Dart has a
general notation for function types, you can use it for function-typed
parameters as well:

在 Dart 2 新增函式型別語法之前，如果希望不透過 typedef 使用函式引數型別，上例是唯一的方法。
如今 Dart 已經可以為函式提供泛型註解，那麼也可以將泛型註解用於函式型別引數中：

{:.good}
<?code-excerpt "design_good.dart (function-type-param)"?>
{% prettify dart tag=pre+code %}
Iterable<T> where(bool Function(T) predicate) => ...
{% endprettify %}

The new syntax is a little more verbose, but is consistent with other locations
where you must use the new syntax.

雖然新語法稍微冗長一點，但是你必須使用新語法才能與其他位置的型別註解的語法保持一致。

### AVOID using `dynamic` unless you want to disable static checking.

### **避免** 使用 `dynamic` 除非你希望禁用靜態檢查

Some operations work with any possible object. For example, a `log()` method
could take any object and call `toString()` on it. Two types in Dart permit all
values: `Object?` and `dynamic`. However, they convey different things. If you
simply want to state that you allow all objects, use `Object?`. If you want to
allow all objects *except* `null`, then use `Object`.

某些操作適用於任何物件。例如，`log()` 方法可以接受任何物件，並呼叫物件上的 `toString()` 方法。
在 Dart 中兩種型別可以表示所有型別：`Object` 和 `dynamic` 。但是，他們傳達的意義並不相同。
和 Java 或 C# 類似，要表示成員型別為所有物件，使用 `Object` 進行註解。

The type `dynamic` not only accepts all objects, but it also permits all
*operations*. Any member access on a value of type `dynamic` is allowed at
compile time, but may fail and throw an exception at runtime. If you want
exactly that risky but flexible dynamic dispatch, then `dynamic` is the right
type to use.

`dynamic` 這個型別不僅接受所有物件，也允許所有 *operations*。
在編譯時任何成員對 `dynamic` 型別值存取是允許的，但在執行時可能會引發例外。
如果你可以承擔風險來達到靈活性，`dynamic` 型別是你不錯的選擇。

Otherwise, prefer using `Object?` or `Object`. Rely on `is` checks and type
promotion to
ensure that the value's runtime type supports the member you want to access
before you access it.

除此之外，我們建議你使用 `Object?` 或者 `Object`，並使用 `is` 來檢查和進行型別升級，
以確保在執行時存取判斷這個值支援您要存取的成員。

{:.good}
<?code-excerpt "design_good.dart (Object-vs-dynamic)"?>
{% prettify dart tag=pre+code %}
/// Returns a Boolean representation for [arg], which must
/// be a String or bool.
bool convertToBool(Object arg) {
  if (arg is bool) return arg;
  if (arg is String) return arg.toLowerCase() == 'true';
  throw ArgumentError('Cannot convert $arg to a bool.');
}
{% endprettify %}

The main exception to this rule is when working with existing APIs that use
`dynamic`, especially inside a generic type. For example, JSON objects have type
`Map<String, dynamic>` and your code will need to accept that same type. Even
so, when using a value from one of these APIs, it's often a good idea to cast it
to a more precise type before accessing members.

這個規則的主要例外是，與已經使用 `dynamic` 的型別，特別是通用類進行操作的時候。
比如，JSON 物件有 `Map<String, dynamic>` 型別，而且程式碼需要接受相同的型別。
即便如此，在呼叫和使用這些 API 的時候，將型別轉換成一個更精確的型別之後
再去呼叫成員會更好。

{{site.alert.version-note}}
In code that hasn't been migrated to null safety yet, use `Object` to accept
values of all types, including `null`.
{{site.alert.end}}

### DO use `Future<void>` as the return type of asynchronous members that do not produce values.

### **要** 使用 `Future<void>` 作為無法回值非同步成員的返回型別。

When you have a synchronous function that doesn't return a value, you use `void`
as the return type. The asynchronous equivalent for a method that doesn't
produce a value, but that the caller might need to await, is `Future<void>`.

對於不返回值得同步函式，要使用 `void` 作為返回型別。對於需要等待的，但無返回值的非同步方法方法，
使用 `Future<void>` 作為返回值型別。

You may see code that uses `Future` or `Future<Null>` instead because older
versions of Dart didn't allow `void` as a type argument. Now that it does, you
should use it. Doing so more directly matches how you'd type a similar
synchronous function, and gives you better error-checking for callers and in the
body of the function.

你可能會見到使用 `Future` 或 `Future<Null>` 作為返回值型別，這是因為舊版本的 Dart 不允許
`void` 作為型別引數。既然現在允許了，那麼就應該使用新的方式。使用新的方式能夠更直接地匹配那些
已經指定了型別的同步函式，並在函式體中為呼叫者提供更好的錯誤檢查。

For asynchronous functions that do not return a useful value and where no
callers need to await the asynchronous work or handle an asynchronous failure,
use a return type of `void`.

對於一些非同步函式，這些非同步函式不會返回有用的值，而且不需要等待非同步執行結束或不需要處理錯誤結果。
那麼使用 `void` 作為這些非同步函式的返回型別。


### AVOID using `FutureOr<T>` as a return type.

### **避免** 使用 `FutureOr<T>` 作為返回型別。

If a method accepts a `FutureOr<int>`, it is [generous in what it
accepts][postel]. Users can call the method with either an `int` or a
`Future<int>`, so they don't need to wrap an `int` in `Future` that you are
going to unwrap anyway.

如果一個方法接受了一個 `FutureOr<int>` 引數，那麼 [引數接受的類型範圍就會變大][postel] 。使用者
可以使用 `int` 或者 `Future<int>` 來呼叫這個方法，所以呼叫這個方法時就不用把 `int` 包裝到一個
`Future` 中再傳到方法中。而在方法中這個引數一定會進行被解開封裝處理。

[postel]: https://en.wikipedia.org/wiki/Robustness_principle

If you *return* a `FutureOr<int>`, users need to check whether get back an `int`
or a `Future<int>` before they can do anything useful. (Or they'll just `await`
the value, effectively always treating it as a `Future`.) Just return a
`Future<int>`, it's cleaner. It's easier for users to understand that a function
is either always asynchronous or always synchronous, but a function that can be
either is hard to use correctly.

如果是*返回*一個 `FutureOr<int>` 型別的值，那麼方法呼叫者在做任何有意義的操作之前，需要檢查
返回值是一個 `int` 還是 `Future<int>` （或者呼叫者僅 `await` 得到一個值，卻把它當做了 
`Future` ）。返回值使用 `Future<int>` ，型別就清晰了。一個函式要麼一直非同步，要麼一直是同步，
這樣才能夠讓呼叫者更容易理解，否則這個函式很難被正確的使用。

{:.good}
<?code-excerpt "design_good.dart (future-or)"?>
{% prettify dart tag=pre+code %}
Future<int> triple(FutureOr<int> value) async => (await value) * 3;
{% endprettify %}

{:.bad}
<?code-excerpt "design_bad.dart (future-or)"?>
{% prettify dart tag=pre+code %}
FutureOr<int> triple(FutureOr<int> value) {
  if (value is int) return value * 3;
  return value.then((v) => v * 3);
}
{% endprettify %}

The more precise formulation of this guideline is to *only use `FutureOr<T>` in
[contravariant][] positions.* Parameters are contravariant and return types are
covariant. In nested function types, this gets flipped—if you have a
parameter whose type is itself a function, then the callback's return type is
now in contravariant position and the callback's parameters are covariant. This
means it's OK for a *callback's* type to return `FutureOr<T>`:

對這條規則更準確的描述是，**僅在 [逆變][contravariant] 位置使用 `FutureOr<T>`**。
引數是逆變 (contravariant) ，返回型別是協變 (covariant) 。
在巢狀(Nesting)函式型別中，描述是相反的&mdash;如果一個引數自身就是函式引數型別，
那麼此時回呼(Callback)函式的返回型別處於逆變位置，回呼(Callback)函式的引數是協變。
這意味著回呼(Callback)中的函式型別可以返回 `FutureOr<T>` ：

[contravariant]: https://en.wikipedia.org/wiki/Covariance_and_contravariance_(computer_science)

{:.good}
<?code-excerpt "design_good.dart (future-or-contra)" replace="/FutureOr.S./[!$&!]/g"?>
{% prettify dart tag=pre+code %}
Stream<S> asyncMap<T, S>(
    Iterable<T> iterable, [!FutureOr<S>!] Function(T) callback) async* {
  for (final element in iterable) {
    yield await callback(element);
  }
}
{% endprettify %}


## Parameters

## 引數

In Dart, optional parameters can be either positional or named, but not both.

在 Dart 中，可選引數可以是位置引數，也可以是命名引數，但不能兩者都是。

### AVOID positional boolean parameters.

### **避免** 布林型別的位置引數。

{% include linter-rule-mention.md rule="avoid_positional_boolean_parameters" %}

Unlike other types, booleans are usually used in literal form. Values like
numbers are usually wrapped in named constants, but we typically pass around
`true` and `false` directly. That can make call sites unreadable if it isn't
clear what the boolean represents:

與其他型別不同，布林值通常以字面量方式使用。數字值的通常可以包含在命名的常量裡，但對於布林值通常
喜歡直接傳 `true` 和 `false` 。如果不清楚布林值的含義，這樣會造成呼叫者的程式碼不可讀：

{:.bad}
{% prettify dart tag=pre+code %}
new Task(true);
new Task(false);
new ListBox(false, true, true);
new Button(false);
{% endprettify %}

Instead, prefer using named arguments, named constructors, or named constants
to clarify what the call is doing.

這裡，應該考慮使用命名引數，命名建構函式或命名常量來闡明呼叫所執行的操作。

{:.good}
<?code-excerpt "design_good.dart (avoid-positional-bool-param)"?>
{% prettify dart tag=pre+code %}
Task.oneShot();
Task.repeating();
ListBox(scroll: true, showScrollbars: true);
Button(ButtonState.enabled);
{% endprettify %}

Note that this doesn't apply to setters, where the name makes it clear what the
value represents:

請注意，這並不適用於 setter ，因為 setter 的名稱能夠清楚的闡明值得含義：

{:.good}
{% prettify dart tag=pre+code %}
listBox.canScroll = true;
button.isEnabled = false;
{% endprettify %}


### AVOID optional positional parameters if the user may want to omit earlier parameters.

### **避免** 在呼叫者需要省略前面引數的方法中，使用位置可選引數。

Optional positional parameters should have a logical progression such that
earlier parameters are passed more often than later ones. Users should almost
never need to explicitly pass a "hole" to omit an earlier positional argument to
pass later one. You're better off using named arguments for that.

可選的位置引數應該具有邏輯性，前面引數應該比後面的引數使用更頻繁。呼叫者不需要刻意的跳過或省略前面
的一個引數而為後面的引數賦值。如果需要省略前面引數，這種情況最好使用命名可選引數。

{:.good}
<?code-excerpt "design_good.dart (omit-optional-positional)"?>
{% prettify dart tag=pre+code %}
String.fromCharCodes(Iterable<int> charCodes, [int start = 0, int? end]);

DateTime(int year,
    [int month = 1,
    int day = 1,
    int hour = 0,
    int minute = 0,
    int second = 0,
    int millisecond = 0,
    int microsecond = 0]);

Duration(
    {int days = 0,
    int hours = 0,
    int minutes = 0,
    int seconds = 0,
    int milliseconds = 0,
    int microseconds = 0});
{% endprettify %}


### AVOID mandatory parameters that accept a special "no argument" value.

### **避免** 強制引數去接受一個特定表示"空引數"的值。

If the user is logically omitting a parameter, prefer letting them actually omit
it by making the parameter optional instead of forcing them to pass `null`, an
empty string, or some other special value that means "did not pass".

如果呼叫者在邏輯上省略了引數，那麼建議使用可選引數的方式讓這些引數能夠實際性的被省略，而不是
強制讓呼叫者去為他們傳入 `null`，或者空字串，或者是一些其他特殊的值來表示該引數"不需要傳值"。

Omitting the parameter is more terse and helps prevent bugs where a sentinel
value like `null` is accidentally passed when the user thought they were
providing a real value.

省略引數更加簡潔，也有助於防止在呼叫者偶然地將 `null` 作為實際值傳遞到方法中而引起 bug。

{:.good}
<?code-excerpt "design_good.dart (avoid-mandatory-param)"?>
{% prettify dart tag=pre+code %}
var rest = string.substring(start);
{% endprettify %}

{:.bad}
<?code-excerpt "design_bad.dart (avoid-mandatory-param)"?>
{% prettify dart tag=pre+code %}
var rest = string.substring(start, null);
{% endprettify %}


### DO use inclusive start and exclusive end parameters to accept a range.

### **要** 使用開始為閉區間，結束為開區間的半開半閉區間作為接受範圍。

If you are defining a method or function that lets a user select a range of
elements or items from some integer-indexed sequence, take a start index, which
refers to the first item and a (likely optional) end index which is one greater
than the index of the last item.

如果定義一個方法或函式來讓呼叫者能夠從某個整數索引序列中選擇一系列元素或項，開始索引指向的元素
為選取的第一個元素，結束索引（可以為可選引數）指向元素的上一個元素為獲取的最後一個元素。

This is consistent with core libraries that do the same thing.

這種方式與核心函式庫一致。

{:.good}
<?code-excerpt "../../test/effective_dart_test.dart (param-range)" replace="/expect\(//g; /, \/\*\*\// \/\//g; /\);//g"?>
{% prettify dart tag=pre+code %}
[0, 1, 2, 3].sublist(1, 3) // [1, 2]
'abcd'.substring(1, 3) // 'bc'
{% endprettify %}

It's particularly important to be consistent here because these parameters are
usually unnamed. If your API takes a length instead of an end point, the
difference won't be visible at all at the call site.

在這裡保持一致尤為重要，因為這些引數通常是未命名引數。如果你的 API 中第二個引數使用了長度值，
而不是結束索引，那麼在呼叫端是無法區分兩者之間的差異的。

## Equality

## 相等

Implementing custom equality behavior for a class can be tricky. Users have deep
intuition about how equality works that your objects need to match, and
collection types like hash tables have subtle contracts that they expect
elements to follow.

可能為類實現自訂相等的判定是比較棘手事情。使用者對於物件的判等情況有著很深的直覺，同時像雜湊表這樣
的集合型別擁有一些細微的規則，包含在這些集合中的元素需要遵循這些規則。

### DO override `hashCode` if you override `==`.

### **要** 對重寫 `==` 運運算元的類，重寫 `hashCode` 方法。

{% include linter-rule-mention.md rule="hash_and_equals" %}

The default hash code implementation provides an *identity* hash—two
objects generally only have the same hash code if they are the exact same
object. Likewise, the default behavior for `==` is identity.

預設的雜湊實現為物件提供了一個*身份*雜湊&mdash;如果兩個物件是完全相同的，那麼它們通常具有
相同的雜湊值。同樣，`==` 的預設行為是比較兩個物件的身份雜湊。

If you are overriding `==`, it implies you may have different objects that are
considered "equal" by your class. **Any two objects that are equal must have the
same hash code.** Otherwise, maps and other hash-based collections will fail to
recognize that the two objects are equivalent.

如果你重寫 `==` ，就意味著你可能有不同的物件要讓你的類認為是"相等的"。**任何兩個物件要相等就
必須必須具有相同的雜湊值。** 否則，這兩個物件就無法被 map 和其他基於雜湊的集合識別為等效物件。

### DO make your `==` operator obey the mathematical rules of equality.

### **要** 讓 `==` 運運算元的相等遵守數學規則。

An equivalence relation should be:

等價關係應該是：

* **Reflexive**: `a == a` should always return `true`.

  **自反性**: `a == a` 應該始終返回 `true`。

* **Symmetric**: `a == b` should return the same thing as `b == a`.

  **對稱性**: `a == b` 應該與 `b == a` 的返回值相同。

* **Transitive**: If `a == b` and `b == c` both return `true`, then `a == c`
  should too.

  **傳遞性**: If `a == b` 和 `b == c` 都返回 `true`，那麼 `a == c`
  也應該返回 `true` 。

Users and code that uses `==` expect all of these laws to be followed. If your
class can't obey these rules, then `==` isn't the right name for the operation
you're trying to express.

### AVOID defining custom equality for mutable classes.

### **避免** 為可變類自訂相等。

{% include linter-rule-mention.md rule="avoid_equals_and_hash_code_on_mutable_classes" %}

When you define `==`, you also have to define `hashCode`. Both of those should
take into account the object's fields. If those fields *change* then that
implies the object's hash code can change.

定義 `==` 時，必須要定義 `hashCode`。
兩者都需要考慮物件的欄位。
如果這些欄位發生了變化，則意味著物件的雜湊值可能會改變。

Most hash-based collections don't anticipate that—they assume an object's
hash code will be the same forever and may behave unpredictably if that isn't
true.

大多數基於雜湊的集合是無法預料元素雜湊值的改變&mdash;他們假設元素物件的雜湊值是永遠不變的，
如果元素雜湊值發生了改變，可能會出現不可預測的結果

### DON'T make the parameter to `==` nullable.

### **不要** 使用 `==` 運運算元與可空值比較。

{% include linter-rule-mention.md rule="avoid_null_checks_in_equality_operators" %}

The language specifies that `null` is equal only to itself, and that the `==`
method is called only if the right-hand side is not `null`.

Dart 指定此檢查是自動完成的，只有當右側不是 `null` 時才呼叫 `==` 方法。

{:.good}
<?code-excerpt "design_good.dart (eq-dont-check-for-null)" plaster?>
{% prettify dart tag=pre+code %}
class Person {
  final String name;
  // ···

  bool operator ==(Object other) => other is Person && name == other.name;
}
{% endprettify %}

{:.bad}
<?code-excerpt "design_bad.dart (eq-dont-check-for-null)" replace="/Object\?/[!$&!]/g" plaster?>
{% prettify dart tag=pre+code %}
class Person {
  final String name;
  // ···

  bool operator ==([!Object?!] other) =>
      other != null && other is Person && name == other.name;
}
{% endprettify %}

{{site.alert.version-note}}
In code that has not been migrated to null safety yet, the `Object` type
annotation permits `null`. Even so, Dart will never call your `==` method and
pass `null` to it, so you don't need to handle `null` inside the body of the
method.
{{site.alert.end}}
