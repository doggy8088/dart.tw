---
title: Understanding null safety
title: 深入理解空安全
description: A deep dive into Dart language and library changes related to null safety.
description: 深入瞭解 Dart 語言及其依賴在空安全方面的改動。
---

_July 2020, Written by Bob Nystrom_

_文/ Bob Nystrom, Google Dart 團隊工程師_

Null safety is the largest change we've made to Dart since we replaced the
original unsound optional type system with [a sound static type system][strong]
in Dart 2.0. When Dart first launched, compile-time null safety was a rare
feature needing a long introduction. Today, Kotlin, Swift, Rust, and other
languages all have their own answers to what has become a very [familiar
problem.][billion] Here is an example:

自 Dart 2.0 替換了靜態可選型別系統為 [健全的靜態型別系統][strong] 後，
空安全是我們對 Dart 作出最大的改變。
在 Dart 初始之際，編譯時的空安全是一項少有且需要大量時間推進的功能。
時至今日，Kotlin、Swift、Rust 及眾多語言都擁有他們自己的解決方案，
空安全已經成為 [屢見不鮮的話題][billion]。讓我們來看下面這個例子：

[strong]: /guides/language/type-system
[billion]: https://www.infoq.com/presentations/Null-References-The-Billion-Dollar-Mistake-Tony-Hoare/

```dart
// Without null safety:
bool isEmpty(String string) => string.length == 0;

main() {
  isEmpty(null);
}
```

If you run this Dart program without null safety, it throws a
`NoSuchMethodError` exception on the call to `.length`. The `null` value is an
instance of the `Null` class, and `Null` has no "length" getter. Runtime
failures suck. This is especially true in a language like Dart that is designed
to run on an end-user's device. If a server application fails, you can often
restart it before anyone notices. But when a Flutter app crashes on a user's
phone, they are not happy. When your users aren't happy, you aren't happy.

如果你在執行這個 Dart 程式時並未使用空安全，
它將在呼叫 `.length` 時丟擲 `NoSuchMethodError` 例外。
`null` 值是 `Null` 類別的一個例項，而 `Null` 沒有 "length" getter。
執行時才出現的錯誤十分惱人，在本就是為終端打造的 Dart 語言上尤其如此。
如果一個伺服器端應用出現了例外，你可以快速對它進行重啟，而不被使用者察覺。
但當一個 Flutter 應用在使用者的手機上崩潰了，他們的體驗就會大打折扣。
使用者不開心，想必開發者也不會開心。

Developers like statically-typed languages like Dart because they enable the
type checker to find mistakes in code at compile time, usually right in the IDE.
The sooner you find a bug, the sooner you can fix it. When language designers
talk about "fixing null reference errors", they mean enriching the static type
checker so that the language can detect mistakes like the above attempt to call
`.length` on a value that might be `null`.

開發者偏愛像 Dart 這樣的靜態型別語言，
因為它通常可以讓使用 IDE 的開發者透過型別檢查發現錯誤。
Bug 越早被發現，就能越早處理。
當語言設計者在談論“修復空參考錯誤”時，他們指的是加強靜態型別檢查器，
使得諸如在可能為 `null` 的值上呼叫 `.length` 這樣的錯誤能被檢測到。

There is no one true solution to this problem. Rust and Kotlin both have their
own approach that makes sense in the context of those languages. This doc walks
through all the details of our answer for Dart. It includes changes to the
static type system and a suite of other modifications and new language features
to let you not only write null-safe code but hopefully to *enjoy* doing so.

針對這個問題，從來沒有一個標準答案。
Rust 和 Kotlin 在其語言內都各自擁有合理的解決方案。
這篇文件將帶你瞭解 Dart 的解決方案。
它包含了對靜態型別系統及諸多方面的修改，以及新的語言特性，
讓你在編寫程式碼時不僅能寫出空安全的程式碼，同時也能非常 **享受**。

This document is long. If you want something shorter that covers just what you
need to know to get up and running, start with the [overview][]. When you are
ready for a deeper understanding and have the time, come back here so you can
understand *how* the language handles `null`, *why* we designed it that way, and
how to write idiomatic, modern, null-safe Dart. (Spoiler alert: it ends up
surprisingly close to how you write Dart today.)

這篇文件很長。如果你只需要一份如何開始並執行的簡短的文件，請從 [概覽][overview] 開始。
當你認為你有充足的時間，且已經準備好深入理解它時，再回到這裡，
彼時你可以瞭解到語言是 **如何** 處理 `null`、**為什麼** 我們會這樣設計，
以及你如何寫出符合現代習慣的空安全 Dart 程式碼。
（劇透一下：實際上它和你當前寫 Dart 程式碼的方式相差無幾。）

[overview]: /null-safety

The various ways a language can tackle null reference errors each have their
pros and cons. These principles guided the choices we made:

處理空參考錯誤的方法各有利弊。我們基於以下的原則做出選擇：

*   **Code should be safe by default.** If you write new Dart code and don't use
    any explicitly unsafe features, it never throws a null reference error at
    runtime. All possible null reference errors are caught statically. If you
    want to defer some of that checking to runtime to get greater flexibility,
    you can, but you have to choose that by using some feature that is textually
    visible in the code.

    **程式碼在預設情況下是安全的。**
    如果你寫的新程式碼中沒有顯式使用不安全的特性，執行時將不會有空參考錯誤丟擲。
    所有潛在的空參考錯誤都將被靜態捕獲。
    如果你想為了靈活度而將某些檢查放到執行時進行，當然不成問題，
    但你必須在程式碼中顯式使用一些功能來達成你的目的。

    In other words, we aren't giving you a life jacket and leaving it up to you
    to remember to put it on every time you go out on the water. Instead, we
    give you a boat that doesn't sink. You stay dry unless you jump overboard.

    換句話說，我們並不是在你每次出海前給你一件救生衣，提醒你記得穿戴。
    相反，我們提供給你一艘不會沉的小船，只要你不跳下水裡，就無事發生。

*   **Null safe code should be easy to write.** Most existing Dart code is
    dynamically correct and does not throw null reference errors. You like your
    Dart program the way it looks now, and we want you to be able to keep
    writing code that way. Safety shouldn't require sacrificing usability,
    paying penance to the type checker, or having to significantly change the
    way you think.

    **空安全的程式碼應可以輕鬆編寫。**
    現有的大多數 Dart 程式碼都是動態正確的，並且不會丟擲空參考錯誤。
    想必你非常喜歡現在你編寫 Dart 程式碼的方式，
    我們也希望你可以繼續使用這樣的方式編寫程式碼。
    安全性不應該要求易用性作出妥協、不應花更多時間耗費在型別檢查器上，
    也不應使你顯著改變你的思維方式。

*   **The resulting null safe code should be fully sound.** "Soundness" in the
    context of static checking means different things to different people. For
    us, in the context of null safety, that means that if an expression has a
    static type that does not permit `null`, then no possible execution of that
    expression can ever evaluate to `null`. The language provides this guarantee
    mostly through static checks, but there can be some runtime checks involved
    too. (Though, note the first principle: any place where those runtime checks
    happen will be your choice.)

    **產出的空安全程式碼應該是非常健全的。**
    對於靜態檢查而言，“健全”有著多層含義。
    而對我們來說，在空安全的上下文裡，“健全”意味著如果一個表示式聲明瞭一個
    不允許值為 `null` 的靜態型別，那麼這個表示式的任何執行結果都不可能為 `null`。
    Dart 語言主要透過靜態檢查來保證這項特性，但在執行時也有一些檢查參與其中。
    （不過，根據第一條原則，在執行時何時何地進行檢查，完全由你自己掌握。）

    Soundness is important for user confidence. A boat that *mostly* stays
    afloat is not one you're enthused to brave the open seas on. But it's also
    important for our intrepid compiler hackers. When the language makes hard
    guarantees about semantic properties of a program, it means that the
    compiler can perform optimizations that assume those properties are true.
    When it comes to `null`, it means we can generate smaller code that
    eliminates unneeded `null` checks, and faster code that doesn't need to
    verify a receiver is non-`null` before calling methods on it.

    程式碼的健全性極大程度地決定了開發者對於自己的程式碼是否有自信。
    一艘 **大部分時間** 都在飄忽不定的小船，
    是不足以讓你鼓起勇氣，駛往公海進行冒險的。
    這對於我們無畏的“駭客”編譯器而言，同樣十分重要。
    當語言對程式中語義化的屬性做出硬性保證時，
    說明編譯器能真正意義上為這些屬性作出最佳化。
    當它涉及到 `null` 時，意味著可以消除不必要的 `null` 檢查，提供更精悍的程式碼，
    並且在對其呼叫方法前，不需要再校驗是否其為空呼叫。

    One caveat: We only guarantee soundness in Dart programs that are fully null
    safe. Dart supports programs that contain a mixture of newer null safe code
    and older legacy code. In these mixed-version programs, null reference errors
    may still occur. In a mixed-version program, you get all of the *static* safety
    benefits in the portions that are null safe, but you don't get full runtime
    soundness until the entire application is null safe.

    需要注意一點：目前我們只能完全保證使用了空安全的程式碼的健全性。
    Dart 程式支援新的空安全程式碼和舊的傳統程式碼混合。
    在這些使用混合空安全的程式版本中，空參考的錯誤仍有可能出現。
    這類程式裡讓你可以在使用了空安全的部分，享受到所有 **靜態部分的** 空安全福利。
    但在整個程式都使用了空安全之前，程式碼在執行時仍然不能保證是空安全的。

Note that *eliminating* `null` is not a goal. There's nothing wrong with `null`.
On the contrary, it's really useful to be able to represent the *absence* of a
value. Building support for a special "absent" value directly into the language
makes working with absence flexible and usable. It underpins optional
parameters, the handy `?.` null-aware operator, and default initialization. It
is not `null` that is bad, it is having `null` go *where you don't expect it*
that causes problems.

值得注意的是，我們的目標並不是 **消除** `null`。`null` 沒有任何錯。
相反，可以表示一個 **空缺** 的值是十分有用的。
在語言中提供對空缺的值的支援，讓處理空缺更為靈活和高效。
它為可選引數、`?.` 空呼叫語法糖和預設值初始化提供了基礎。
`null` 並不糟糕，糟糕的是 **它在你意想不到的地方出現**，最終引發問題。

Thus with null safety, our goal is to give you *control* and *insight* into
where `null` can flow through your program and certainty that it can't flow
somewhere that would cause a crash.

因此，對於空安全而言，我們的目標是讓你對程式碼中的 `null` 可見且可控，
並且確保它不會傳遞至某些位置從而引發崩潰。

## Nullability in the type system

## 型別系統中的可空性

Null safety begins in the static type system because everything else rests upon
that. Your Dart program has a whole universe of types in it: primitive types
like `int` and `String`, collection types like `List`, and all of the classes
and types you and the packages you use define. Before null safety, the static
type system allowed the value `null` to flow into expressions of any of those
types.

因為一切均建立於靜態型別系統上，所以空安全也始於此處。
你的 Dart 程式中包含了整個型別世界：基本型別（如 `int` 和 `String`）、
集合型別（如 `List`）以及你和你所使用的依賴所定義的類和型別。
在空安全推出之前，靜態型別系統允許所有型別的表示式中的每一處都可以有 `null`。

In type theory lingo, the `Null` type was treated as a subtype of all types:

從型別理論的角度來說，`Null` 型別被看作是所有型別的子類別；

<img src="understanding-null-safety/hierarchy-before.png" width="335">

The set of operations—getters, setters, methods, and
operators—allowed on some expressions are defined by its type. If the type
is `List`, you can call `.add()` or `[]` on it. If it's `int`, you can call `+`.
But the `null` value doesn't define any of those methods. Allowing `null` to
flow into an expression of some other type means any of those operations can
fail. This is really the crux of null reference errors—every failure comes
from trying to look up a method or property on `null` that it doesn't have.

型別會定義一些操作物件，包括 getters、setters、方法和運運算元，在表示式中使用。
如果是 `List` 型別，你可以對其呼叫 `.add()` 或 `[]`。
如果是 `int` 型別，你可以對其呼叫 `+`。 
但是 `null` 值並沒有它們定義的任何一個方法。
所以當 `null` 傳遞至其他型別的表示式時，任何操作都有可能失敗。
這就是空參考的癥結所在&mdash;&mdash;所有錯誤都來源於嘗試在 `null` 上查詢一個不存在的方法或屬性。

### Non-nullable and nullable types

### 非空和可空型別

Null safety eliminates that problem at the root by changing the type hierarchy.
The `Null` type still exists, but it's no longer a subtype of all types.
Instead, the type hierarchy looks like this:

空安全透過修改了型別的層級結構，從根源上解決了這個問題。
`Null` 型別仍然存在，但它不再是所有型別的子類別。
現在的型別層級看起來是這樣的：

<img src="understanding-null-safety/hierarchy-after.png" width="344">

Since `Null` is no longer a subtype, no type except the special `Null` class
permits the value `null`. We've made all types *non-nullable by default*. If you
have a variable of type `String`, it will always contain *a string*. There,
we've fixed all null reference errors.

既然 `Null` 已不再被看作所有型別的子類別，
那麼除了特殊的 `Null` 型別允許傳遞 `null` 值，其他型別均不允許。
我們已經將所有的型別設定為 **預設不可空** 的型別。
如果你的變數是 `String` 型別，它必須包含 **一個字串**。
這樣一來，我們就修復了所有的空參考錯誤。

If we didn't think `null` was useful at all, we could stop here. But `null` is
useful, so we still need a way to handle it. Optional parameters are a good
illustrative case. Consider this null safe Dart code:

如果 `null` 對我們來說沒有什麼意義的話，那大可不必再研究下去了。
但實際上 `null` 十分有用，所以我們仍然需要合理地處理它。
可選引數就是非常好的例子。讓我們來看下這段空安全的程式碼：

```dart
// Using null safety:
makeCoffee(String coffee, [String? dairy]) {
  if (dairy != null) {
    print('$coffee with $dairy');
  } else {
    print('Black $coffee');
  }
}
```

Here, we want to allow the `dairy` parameter to accept any string, or the value
`null`, but nothing else. To express that, we give `dairy` a *nullable type* by
slapping `?` at the end of the underlying base type `String`. Under the hood,
this is essentially defining a [union][] of the underlying type and the `Null`
type. So `String?` would be a shorthand for `String|Null` if Dart had
full-featured union types.

此處我們希望 `dairy` 引數能傳入任意字串，或者一個 `null` 值。
為了表達我們的想法，我們在原有型別 `String` 的尾部加上 `?` 使得 `dairy` 成為可空的型別。
本質上，這和定義了一個原有型別加 `Null` 的 [組合型別][union] 沒有什麼區別。
所以如果 Dart 包含完整的組合型別定義，那麼 `String?` 就是 `String|Null` 的縮寫。

[union]: https://en.wikipedia.org/wiki/Union_type

### Using nullable types

### 使用可空型別

If you have an expression with a nullable type, what can you do with the result?
Since our principle is safe by default, the answer is not much. We can't let you
call methods of the underlying type on it because those might fail if the value
is `null`:

如果你的表示式可能返回空值，你會如何處理它呢？
由於安全是我們的原則之一，答案其實所剩無幾。
因為你在其值為 `null` 的時候呼叫方法將會失敗，所以我們不會允許你這樣做。

```dart
// Hypothetical unsound null safety:
bad(String? maybeString) {
  print(maybeString.length);
}

main() {
  bad(null);
}
```

This would crash if we let you run it. The only methods and properties we can
safely let you access are ones defined by both the underlying type and the
`Null` class. That's just `toString()`, `==`, and `hashCode`. So you can use
nullable types as map keys, store them in sets, compare them to other values,
and use them in string interpolation, but that's about it.

如果我們允許這樣的程式碼執行，那麼它將毫無疑問地崩潰。
我們只允許你存取同時在原有型別及 `Null` 類下同時定義的方法和屬性。
所以只有 `toString()`、`==` 和 `hashCode` 可以存取。
因此，你可以將可空型別用於 Map 的鍵值、儲存於集合中或者與其他值進行比較，僅此而已。

How do they interact with non-nullable types? It's always safe to pass a
*non*-nullable type to something expecting a nullable type. If a function
accepts `String?` then passing a `String` is allowed because it won't cause any
problems. We model this by making every nullable type a supertype of its
underlying type. You can also safely pass `null` to something expecting a nullable type, so
`Null` is also a subtype of every nullable type:

那麼原有型別是如何與可空型別互動的呢？
我們知道，將一個 **非** 空型別的值傳遞給可空型別是一定安全的。
如果一個函式接受 `String?`，那麼向其傳遞 `String` 是允許的，不會有任何問題。
在此次改動中，我們將所有的可空型別作為基礎型別的超類別。
你也可以將 `null` 傳遞給一個可空的型別，即 `Null` 也是任何可空型別的子類別：

<img src="understanding-null-safety/nullable-hierarchy.png" width="235">

But going the other direction and passing a nullable type to something expecting
the underlying non-nullable type is unsafe. Code that expects a `String` may
call `String` methods on the value. If you pass a `String?` to it, `null` could
flow in and that could fail:

但將一個可空型別傳遞給非空的基礎型別，是不安全的。
宣告為 `String` 的變數可能會在你傳遞的值上呼叫 `String` 的方法。
如果你傳遞了 `String?`，傳入的 `null` 將可能產生錯誤：

```dart
// Hypothetical unsound null safety:
requireStringNotNull(String definitelyString) {
  print(definitelyString.length);
}

main() {
  String? maybeString = null; // Or not!
  requireStringNotNull(maybeString);
}
```

This program is not safe and we shouldn't allow it. However, Dart has always had
this thing called *implicit downcasts*. If you, for example, pass a value of
type `Object` to a function expecting an `String`, the type checker allows it:

我們不會允許這樣不安全的程式出現。
然而，**隱含轉換** 在 Dart 中一直存在。
假設你將型別為 `Object` 的例項傳遞給了需要 `String` 的函式，
型別檢查器會允許你這樣做：

```dart
// Without null safety:
requireStringNotObject(String definitelyString) {
  print(definitelyString.length);
}

main() {
  Object maybeString = 'it is';
  requireStringNotObject(maybeString);
}
```

To maintain soundness, the compiler silently inserts an `as String` cast on the
argument to `requireStringNotObject()`. That cast could fail and throw an
exception at runtime, but at compile time, Dart says this is OK. Since
non-nullable types are modeled as subtypes of nullable types, implicit downcasts
would let you pass a `String?` to something expecting a `String`. Allowing that
would violate our goal of being safe by default. So with null safety we are
removing implicit downcasts entirely.

為了保持健全性，編譯器為 `requireStringNotObject()` 的引數
靜默添加了 `as String` 強制轉換。
在執行時進行轉換可能會丟擲例外，但在編譯時，Dart 允許這樣的操作。
在可空型別已經變為非空型別的子類別的前提下，
隱含轉換允許你給需要 `String` 的內容傳遞 `String?`。
這項來自隱含轉換的允諾與我們的安全性目標不符。
所以在空安全推出之際，我們完全移除了隱含轉換。

This makes the call to `requireStringNotNull()` produce a compile error, which
is what you want. But it also means *all* implicit downcasts become compile
errors, including the call to `requireStringNotObject()`. You'll have to add the
explicit downcast yourself:

這會讓 `requireStringNotNull()` 的呼叫產生你預料中的編譯錯誤。
同時也意味著，類似 `requireStringNotObject()` 這樣的
**所有** 隱含轉換呼叫都變成了編譯錯誤。
你需要自己新增顯式型別轉換：

```dart
// Using null safety:
requireStringNotObject(String definitelyString) {
  print(definitelyString.length);
}

main() {
  Object maybeString = 'it is';
  requireStringNotObject(maybeString as String);
}
```

We think this is an overall good change. Our impression is that most users never
liked implicit downcasts. In particular, you may have been burned by this
before:

總的來說，我們認為這是項非常好的改動。
在我們的印象中，大部分使用者非常厭惡隱含轉換。
你可能已經遭受過它的摧殘：

```dart
// Without null safety:
List<int> filterEvens(List<int> ints) {
  return ints.where((n) => n.isEven);
}
```

Spot the bug? The `.where()` method is lazy, so it returns an `Iterable`, not a
`List`. This program compiles but then throws an exception at runtime when it
tries to cast that `Iterable` to the `List` type that `filterEvens` declares it
returns. With the removal of implicit downcasts, this becomes a compile error.

看出問題了嗎？`.where()` 方法是延遲載入的，所以它返回了一個 `Iterable` 而非 `List`。
這段程式碼會正常編譯，但會在執行時丟擲一個例外，提示你在對 `Iterable` 進行
轉換為 `filterEvens` 宣告的返回型別 `List` 時遇到了錯誤。
在隱含轉換移除後，這就變成了一個編譯錯誤。

Where were we? Right, OK, so it's as if we've taken the universe of types in
your program and split them into two halves:

（我是誰我在哪？剛剛說到哪了？）
所以正如我們在型別世界中將所有型別拆分成兩半一樣：

<img src="understanding-null-safety/bifurcate.png" width="668">

There is a region of non-nullable types. Those types let you access all of the
interesting methods, but can never ever contain `null`. And then there is a
parallel family of all of the corresponding nullable types. Those permit `null`,
but you can't do much with them. We let values flow from the non-nullable side
to the nullable side because doing so is safe, but not the other direction.

此處有一個非空型別的區域劃分。該區域中的型別能存取到你想要的所有方法，但不能包含 `null`。
接著有一個對應並行的可空型別家族。它們允許出現 `null`，但你並沒有太多操作空間。
讓值從非空的一側走向可空的一側是安全的，但反之則不是。

That seems like nullable types are basically useless. They have no methods and
you can't get away from them. Don't worry, we have a whole suite of features to
help you move values from the nullable half over to the other side that we will
get to soon.

這麼看來，可空型別基本宣告毫無作用了。
它們不包含任何方法，但是你又無法擺脫它們。
別擔心，接下來我們有一整套的方法來幫助你把值從可空的一半轉移到另一半。

### Top and bottom

### 最上層及底層

This section is a little esoteric. You can mostly skip it, except for two
bullets at the very end, unless you're into type system stuff. Imagine all the
types in your program with edges between ones that are subtypes and supertypes
of each other. If you were to draw it, like the diagrams in this doc, it would
form a huge directed graph with supertypes like `Object` near the top and leaf
classes like your own types near the bottom.

這一節會略微深奧。
除非你對型別系統非常感興趣，否則你可以直接跳過這一節，並且在本文最後部分，還有兩項有趣的內容。
想象一下，在你的程式裡，所有的型別都互為子類別或超類別。
如果將它們的關係用畫圖表示出來，就像文中的那些圖一樣，那將會是一幅巨大的有向圖，
諸如 `Object` 的超類會在最上層，子類別在底層。

If that directed graph comes to a point at the top where there is a single type
that is the supertype (directly or indirectly), that type is called the *top
type*. Likewise, if there is a weird type at that bottom that is a subtype of
every type, you have a *bottom type*. (In this case, your directed graph is a
[lattice.][lattice])

如果這張有向圖的頂部有是一個單一的超類（直接或間接），那麼這個型別稱為 **最上層型別**。
類似的，如果有一個在底部有一個奇怪的型別，是所有型別的子類別，這個型別就被稱為 **底層型別**。
（在這個情況下，你的有向圖是一種 [偏序集合 (lattice)][lattice]）

[lattice]: https://en.wikipedia.org/wiki/Lattice_(order)

It's convenient if your type system has a top and bottom type, because it means
that type-level operations like least upper bound (which type inference uses to
figure out the type of a conditional expression based on the types of its two
branches) can always produce a type. Before null safety, `Object` was Dart's top
type and `Null` was its bottom type.

如果型別系統中有最上層和底層型別，將給我們帶來一定程度的便利，
因為它意味著像最小上界這樣型別層面的操作
（型別推理常根據一個條件表示式的兩個分支推匯出一個類別型）
一定能推匯出一個類別型。
在空安全引入以前，Dart 中的最上層型別是 `Object`，底層型別是 `Null`。

Since `Object` is non-nullable now, it is no longer a top type. `Null` is not a
subtype of it. Dart has no *named* top type. If you need a top type, you want
`Object?`. Likewise, `Null` is no longer the bottom type. If it was, everything
would still be nullable. Instead, we've added a new bottom type named `Never`:

由於現在 `Object` 不再可空，所以它不再是一個最上層型別了。`Null` 也不再是它的子類別。
Dart 中沒有 **令人熟知的** 最上層型別。如果你需要一個最上層型別，可以用 `Object?`。
同樣的，`Null` 也不再是底層型別，否則所有型別都仍將是可空。
取而代之是一個全新的底層型別 `Never`：

<img src="understanding-null-safety/top-and-bottom.png" width="360">

In practice, this means:

依據實際開發中的經驗，這意味著：

*   If you want to indicate that you allow a value of any type, use `Object?`
    instead of `Object`. In fact, it becomes pretty unusual to use `Object`
    since that type means "could be any possible value except this one weirdly
    prohibited value `null`".

    如果你想表明讓一個值可以接受任意類別型，請用 `Object?` 而不是 `Object`。
    使用 `Object` 後會使得程式碼的行為變得非常詭異，
    因為它意味著能夠是“除了 `null` 以外的任何例項”。

*   On the rare occasion that you need a bottom type, use `Never` instead of
    `Null`. If you don't know if you need a bottom type, you probably don't.

    在極少數需要底層型別的情況下，請使用 `Never` 代替 `Null`。
    如果你不瞭解是否需要一個底層型別，那麼你基本上不會需要它。

## Ensuring correctness

## 確保正確性

We divided the universe of types into nullable and non-nullable halves. In order
to maintain soundness and our principle that you can never get a null reference
error at runtime unless you ask for it, we need to guarantee that `null` never
appears in any type on the non-nullable side.

我們將型別世界劃分為了非空和可空的兩半。
為了保持程式碼的健全和我們的原則：“除非你需要，否則你永遠不會在執行時遇到空參考錯誤”，
我們需要保證 `null` 不會出現在非空一側的任何型別裡。

Getting rid of implicit downcasts and removing `Null` as a bottom type covers
all of the main places that types flow through a program across assignments and
from arguments into parameters on function calls. The main remaining places
where `null` can sneak in are when a variable first comes into being and when
you leave a function. So there are some additional compile errors:

透過取代了隱含轉換，並且不再將 `Null` 作為底層型別，
我們覆蓋了程式中宣告、函式引數和函式呼叫等所有的主要位置。
現在只有當變數首次出現和你跳出函式的時候，`null` 可以悄悄潛入。
所以我們還會看到一些附加的編譯錯誤：

### Invalid returns

### 無效的返回值

If a function has a non-nullable return type, then every path through the
function must reach a `return` statement that returns a value. Before null
safety, Dart was pretty lax about missing returns. For example:

如果一個函式的返回型別非空，那麼函式內最終一定要呼叫 `return` 返回一個值。
在空安全引入以前，Dart 在限制未返回內容的函式時非常鬆懈。舉個例子：

```dart
// Without null safety:
String missingReturn() {
  // No return.
}
```

If you analyzed this, you got a gentle *hint* that *maybe* you forgot a return,
but if not, no big deal. That's because if execution reaches the end of a
function body then Dart implicitly returns `null`. Since every type is nullable,
*technically* this function is safe, even though it's probably not what you
want.

如果分析器檢查了這個函式，你會看到一個輕微的 **提示**，提醒你 **可能** 忘記返回值，
但不返回也無關緊要。
這是因為程式碼執行到最後時，Dart 會隱含返回一個 `null`。
因為所有的型別都是可空的，所以 **從程式碼層面而言**，這個函式是安全的，儘管它並不一定與你預期相符。

With sound non-nullable types, this program is flat out wrong and unsafe. Under
null safety, you get a compile error if a function with a non-nullable return
type doesn't reliably return a value. By "reliably", I mean that the language
analyzes all of the control flow paths through the function. As long as they all
return something, it is satisfied. The analysis is pretty smart, so even this
function is OK:

有了確定的非空型別，這段程式就是錯誤且不安全的。
在空安全下，如果一個返回值為非空型別的函式，沒有可靠地返回一個值，你就會看到編譯錯誤。
這裡所提到的“可靠”，指的是分析器會分析函式中所有的控制流。
只要它們都返回了內容，就滿足了條件。
分析器相當聰明，聰明到下面的程式碼也能應付：

```dart
// Using null safety:
String alwaysReturns(int n) {
  if (n == 0) {
    return 'zero';
  } else if (n < 0) {
    throw ArgumentError('Negative values not allowed.');
  } else {
    if (n > 1000) {
      return 'big';
    } else {
      return n.toString();
    }
  }
}
```

We'll dive more deeply into the new flow analysis in the next section.

下個章節我們會更加深入地瞭解新的流程分析。

### Uninitialized variables

### 未初始化的變數

When you declare a variable, if you don't give it an explicit initializer, Dart
default initializes the variable with `null`. That's convenient, but obviously
totally unsafe if the variable's type is non-nullable. So we have to tighten
things up for non-nullable variables:

當你在宣告變數時，如果沒有傳遞一個顯式的初始化內容，Dart 預設會將變數初始化為 `null`。
這的確非常方便，但在變數可空的情況下，明顯非常不安全。
所以，我們需要加強對非空變數的處理：

*   **Top level variable and static field declarations must have an
    initializer.** Since these can be accessed and assigned from anywhere in the
    program, it's impossible for the compiler to guarantee that the variable has
    been given a value before it gets used. The only safe option is to require
    the declaration itself to have an initializing expression that produces a
    value of the right type:

    **最上層變數和靜態欄位必須包含一個初始化方法。**
    由於它們能在程式裡的任何位置被存取到，編譯器無法保證它們在被使用前已被賦值。
    唯一保險的選項是要求其本身包含初始化表示式，以確保產生匹配的型別的值。

    ```dart
    // Using null safety:
    int topLevel = 0;

    class SomeClass {
      static int staticField = 0;
    }
    ```

*   **Instance fields must either have an initializer at the declaration, use an
    initializing formal, or be initialized in the constructor's initialization
    list.** That's a lot of jargon. Here are the examples:

    **例項的欄位也必須在宣告時包含初始化方法，
    可以為常見初始化形式，也可以在例項的構造方法中進行初始化。**
    這類初始化非常常見。舉個例子：

    ```dart
    // Using null safety:
    class SomeClass {
      int atDeclaration = 0;
      int initializingFormal;
      int initializationList;

      SomeClass(this.initializingFormal)
          : initializationList = 0;
    }
    ```

    In other words, as long as the field has a value before you reach the
    constructor body, you're good.

    換句話說，欄位在構造體執行前被賦值即可。

*   Local variables are the most flexible case. A non-nullable local variable
    *doesn't* need to have an initializer. This is perfectly fine:

    區域變數的靈活度最高。一個非空的變數 **不一定需要** 一個初始化方法。
    這裡有個很好的例子：

    ```dart
    // Using null safety:
    int tracingFibonacci(int n) {
      int result;
      if (n < 2) {
        result = n;
      } else {
        result = tracingFibonacci(n - 2) + tracingFibonacci(n - 1);
      }

      print(result);
      return result;
    }
    ```

    The rule is only that **a local variable must be *definitely assigned*
    before it is used.** We get to rely on the new flow analysis I alluded to
    for this as well. As long as every path to a variable's use initializes it
    first, the use is OK.

    此處遵循的規則是 **區域變數必須*確保在使用前被賦值*。**
    我們也可以依賴於之前所提到的全新的流程分析來實現。
    只要所有使用變數的路徑，在使用前都先初始化，就可以正常呼叫。

*   **Optional parameters must have a default value.** If you don't pass an
    argument for an optional positional or named parameter, then the language
    fills it in with the default value. If you don't specify a default value,
    the _default_ default value is `null`, and that doesn't fly if the parameter's
    type is non-nullable.

    **可選引數必須具有預設值。**
    如果一個可選位置引數或可選命名引數沒有傳遞內容，Dart 會自動使用預設值進行填充。
    在未指定預設值的情況下，**預設的** 預設值為 `null`，
    如此一來，非空型別的引數就要出事了。

    So, if you want a parameter to be optional, you need to either make it
    nullable or specify a valid non-`null` default value.

    所以，如果你需要一個可選引數，要麼它是可空的，要麼它的預設值不為 `null`。

These restrictions sound onerous, but they aren't too bad in practice. They are
very similar to the existing restrictions around `final` variables and you've
likely been working with those for years without even really noticing. Also,
remember that these only apply to *non-nullable* variables. You can always make
the type nullable and then get the default initialization to `null`.

這些限制聽起來非常繁瑣，但在實際操作中並不難。
它們與目前 `final` 有關的限制非常相似，你可能沒有特別關注過，但它們伴隨你已久。
另外，請記住，這些限制僅適用於 **非空** 變數。
在你使用可空的型別時，`null` 仍然可以作為初始化的預設值。

Even so, the rules do cause friction. Fortunately, we have a suite of new
language features to lubricate the most common patterns where these new
limitations slow you down. First, though, it's time to talk about flow analysis.

即便如此，這些規則也會讓你的適配之路有些小磕碰。
幸運的是，我們有一整套新的語言特性，來幫助你平穩渡過一些常見的顛簸。
不過，首先，我們是時候來聊一聊流程分析了。

## Flow analysis

## 流程分析

[Control flow analysis][] has been around in compilers for years. It's mostly
hidden from users and used during compiler optimization, but some newer
languages have started to use the same techniques for visible language features.
Dart already has a dash of flow analysis in the form of *type promotion*:

[控制流程分析][Control flow analysis] 已經在眾多編譯器中存在多年了。
通常它對於使用者而言是不可見的，只在編譯最佳化流程中使用，
但是，部分較新的語言，已經開始在可以看見的語言特性中使用同樣的技術了。
Dart 已經以 **型別提升** 的方式實現了一些流程分析：

```dart
// With (or without) null safety:
bool isEmptyList(Object object) {
  if (object is List) {
    return object.isEmpty; // <-- OK!
  } else {
    return false;
  }
}
```

[control flow analysis]: https://en.wikipedia.org/wiki/Control_flow_analysis

Note how on the marked line, we can call `isEmpty` on `object`. That method is
defined on `List`, not `Object`. This works because the type checker looks at
all of the `is` expressions and the control flow paths in the program. If the
body of some control flow construct only executes when a certain `is` expression
on a variable is true, then inside that body the variable's type is "promoted"
to the tested type.

請留意我們是如何在標記的程式碼行上對 `object` 呼叫 `isEmpty` 的。
該方法是在 `List` 中定義的，而不是 `Object`。
因為型別檢查器檢查了程式碼中所有的 `is` 表示式，以及控制流的路徑，所以這段程式碼是有效的。
如果部分控制流的程式碼主體只在變數的某個 `is` 表示式為真時才執行，
那麼這個程式碼塊中的變數，將會是經過推導得出的型別。

In the example here, the then branch of the `if` statement only runs when
`object` actually contains a list. Therefore, Dart promotes `object` to type
`List` instead of its declared type `Object`. This is a handy feature, but it's
pretty limited. Prior to null safety, the following functionally identical
program did not work:

在這個例子中，`if` 陳述式的 then 分支僅會在 `object` 是列表的時候執行。
因此，在這裡 Dart 將 `object` 的型別從它宣告的 `Object` 提升到了 `List`。
這項功能非常方便，但它有著諸多限制。
在空安全引入以前，下面的程式無法執行：

```dart
// Without null safety:
bool isEmptyList(Object object) {
  if (object is! List) return false;
  return object.isEmpty; // <-- Error!
}
```

Again, you can only reach the `.isEmpty` call when `object` contains a list, so
this program is dynamically correct. But the type promotion rules were not smart
enough to see that the `return` statement means the second statement can only be
reached when `object` is a list.

與之前一樣，你只能在 `object` 是列表的時候呼叫 `.isEmpty`，
所以實際上這段程式碼是正確的。
但是型別提升規則並不那麼智慧，
它無法預測到 `return` 讓下面程式碼只能在 `object` 為列表時才能存取到。

For null safety, we've taken this limited analysis and made it [much more
powerful in several ways.][flow analysis]

在空安全中，我們 [從不同的維度增強了][flow analysis] 這項能力，
讓它不再只能進行有限的分析。

[flow analysis]: https://github.com/dart-lang/language/blob/master/resources/type-system/flow-analysis.md

### Reachability analysis

### 可達性分析

First off, we fixed the [long-standing complaint][18921] that type promotion
isn't smart about early returns and other unreachable code paths. When analyzing
a function, it now takes into account `return`, `break`, `throw`, and any other
way execution might terminate early in a function. Under null safety, this function:

首先，長期以來型別提升在處理提前返回和無法到達的程式碼路徑時 [不夠智慧的問題][18921]，
已經被我們修復。
當我們在分析一個函式時，`return`、`break`、`throw` 以及任何可能提早結束函式的方式，
都將被考慮進來。
在空安全下，下面的這個函式：

[18921]: https://github.com/dart-lang/sdk/issues/18921

```dart
// Using null safety:
bool isEmptyList(Object object) {
  if (object is! List) return false;
  return object.isEmpty;
}
```

Is now perfectly valid. Since the `if` statement will exit the function when
`object` is *not* a `List`, Dart promotes `object` to be `List` on the second
statement. This is a really nice improvement that helps a lot of Dart code, even
stuff not related to nullability.

現在是完全有效的。
由於 `if` 陳述式會在 `object` **不是** `List` 時退出這個函式，
因此 Dart 將下一句的 `object` 型別提升至了 `List`。
對於眾多 Dart 程式碼來說，這是一項非常棒的改進，就算對於一些與空安全無關的程式碼來說也是。

### Never for unreachable code

### 為不可達的程式碼準備的 Never

You can also *program* this reachability analysis. The new bottom type `Never`
has no values. (What kind of value is simultaneously a `String`, `bool`, and
`int`?) So what does it mean for an expression to have type `Never`? It means
that expression can never successfully finish evaluating. It must throw an
exception, abort, or otherwise ensure that the surrounding code expecting the
result of the expression never runs.

你可以自己 **碼出** 這項可達性分析。
新的底層型別 `Never` 是沒有任何值的。（什麼值能同時是 `String`、`bool` 和 `int` 呢？）
那麼一個類別型為 `Never` 的表示式有什麼含義呢？
它意味著這個表示式永遠無法成功的推導和執行。
它必須要丟擲一個例外、中斷或者確保呼叫它的程式碼永遠不會執行。

In fact, according to the language, the static type of a `throw` expression is
`Never`. The type `Never` is declared in the core libraries and you can use it
as a type annotation. Maybe you have a helper function to make it easier to
throw a certain kind of exception:

事實上，根據語言的細則，`throw` 表示式的靜態型別就是 `Never`。
該型別已在核心庫中定義，你可以將它用於變數宣告。
也許你會寫一個輔助函式，用於簡單方便地丟擲一個固定的例外：

```dart
// Using null safety:
Never wrongType(String type, Object value) {
  throw ArgumentError('Expected $type, but was ${value.runtimeType}.');
}
```

You might use it like so:

你也可以這樣用：

```dart
// Using null safety:
class Point {
  final double x, y;

  bool operator ==(Object other) {
    if (other is! Point) wrongType('Point', other);
    return x == other.x && y == other.y;
  }

  // Constructor and hashCode...
}
```

This program analyzes without error. Notice that the last line of the `==`
method accesses `.x` and `.y` on `other`. It has been promoted to `Point` even
though the function doesn't have any `return` or `throw`. The control flow
analysis knows that the declared type of `wrongType()` is `Never` which means
the then branch of the `if` statement *must* abort somehow. Since the second
statement can only be reached when `other` is a `Point`, Dart promotes it.

這段程式碼不會分析出錯誤。
請注意 `==` 方法的最後一行，在 `other` 上呼叫 `.x` 和 `.y`。
儘管在第一行並沒有包含 `return` 或 `throw`，它的型別仍然提升為了 `Point`。
控制流程分析意識到 `wrongType()` 宣告的型別是 `Never`，
代表著 `if` 陳述式的 then 分支 **一定會** 由於某些原因被中斷。
由於下一句的程式碼僅能在 `other` 是 `Point` 時執行，所以 Dart 提升了它的型別。

In other words, using `Never` in your own APIs lets you extend Dart's
reachability analysis.

換句話說，在你的程式碼中使用 `Never` 讓你可以擴充 Dart 的可達性分析。

### Definite assignment analysis

### 絕對的賦值分析

I mentioned this one briefly with local variables. Dart needs to ensure a
non-nullable local variable is always initialized before it is read. We use
*definite assignment analysis* to be as flexible about that as possible. The
language analyzes each function body and tracks the assignments to local
variables and parameters through all control flow paths. As long as the variable
is assigned on every path that reaches some use of a variable, the variable is
considered initialized. This lets you declare a variable with no initializer and
then initialize it afterwards using complex control flow, even when the variable
has a non-nullable type.

前文已經在提到區域變數時簡單提到了這個分析。
Dart 需要確保一個非空的區域變數在它被讀取前一定完成了初始化。
我們使用了 **絕對的賦值分析**，從而保證儘可能靈活地處理變數的初始化。
Dart 語言會逐個分析函式的主體，並且追蹤所有控制流路徑的區域變數和引數的賦值。
只要變數在每個使用路徑中都已經被賦值，這個變數就被視為已初始化。
這項分析可以讓你不再一開始就對變數初始化，而是在後面複雜的控制流中進行賦值，
甚至非空型別變數也可以這樣做。

We also use definite assignment analysis to make *final* variables more
flexible. Before null safety, it can be difficult to use `final` for local
variables if you need to initialize them in any sort of interesting way:

同時我們也透過絕對賦值分析使得宣告為 **final** 的變數更靈活。
在空安全引入以前，當你需要宣告一個 `final` 變數時，
一些有意思的初始化方式是無法使用的：

```dart
// Using null safety:
int tracingFibonacci(int n) {
  final int result;
  if (n < 2) {
    result = n;
  } else {
    result = tracingFibonacci(n - 2) + tracingFibonacci(n - 1);
  }

  print(result);
  return result;
}
```

This would be an error since the `result` variable is `final` but has no
initializer. With the smarter flow analysis under null safety, this program is
fine. The analysis can tell that `result` is definitely initialized exactly once
on every control flow path, so the constraints for marking a variable `final`
are satisfied.

鑑於 `result` 被宣告為 `final`，又不包含初始化內容，這段程式碼將返回一個錯誤。
而對於更智慧的空安全流程分析來說，這段程式碼是正確的。
透過分析可以知道，`result` 在所有的控制流路徑上都已經被初始化，
所以對於標記的 `final` 變數而言，約束得以滿足。

### Type promotion on null checks

### 空檢查的型別提升

The smarter flow analysis helps lots of Dart code, even code not related to
nullability. But it's not a coincidence that we're making these changes now. We
have partitioned types into nullable and non-nullable sets. If you have a value
of a nullable type, you can't really *do* anything useful with it. In cases
where the value *is* `null`, that restriction is good. It's preventing you from
crashing.

更智慧的流程分析對於眾多 Dart 程式碼而言幫助極大，甚至對於一些與是否可空無關的程式碼也是如此。
但是我們在現在做出這些改動並非巧合。
我們已經將型別劃分成了可空和非空的集合，
如果一個變數是一個可空的型別，你無法對它 **做** 任何有用的事情。
所以在 **值為** `null` 的情況下，這項限制是很有效的，
它可以避免你的程式崩潰。

But if the value isn't `null`, it would be good to be able to move it over to
the non-nullable side so you can call methods on it. Flow analysis is one of the
primary ways to do this for local variables and parameters. We've extended type
promotion to also look at `== null` and `!= null` expressions.

而如果值不為 `null`，最好是直接將它移到非空的一側，如此一來你就可以呼叫它的方法了。
流程分析是對變數和區域變數進行處理的主要方法之一。
我們在分析 `== null` 和 `!= null` 表示式時也進行了型別提升的擴充。

If you check a local variable with nullable type to see if it is not `null`, 
Dart then promotes the variable to the underlying non-nullable type:

如果你判斷了一個可空的變數是否不為 `null`，進行到下一步後
Dart 就會將這個變數的型別提升至非空的對應型別：

```dart
// Using null safety:
String makeCommand(String executable, [List<String>? arguments]) {
  var result = executable;
  if (arguments != null) {
    result += ' ' + arguments.join(' ');
  }
  return result;
}
```

Here, `arguments` has a nullable type. Normally, that prohibits you from calling
`.join()` on it. But because we have guarded that call in an `if` statement that
checks to ensure the value is not `null`, Dart promotes it from `List<String>?`
to `List<String>` and lets you call methods on it or pass it to functions that
expect non-nullable lists.

此處，`arguments` 是可空的型別。
通常來說，對其呼叫 `.join()` 是禁止的。
但是，由於 `if` 陳述式中的判斷已經足以確認值不為 `null`，
Dart 將它的型別從 `List<String>?` 提升到了 `List<String>`，
從而讓你能夠呼叫它的方法，或將它傳遞給一個需要非空列表的函式。

This sounds like a fairly minor thing, but this flow-based promotion on null
checks is what makes most existing Dart code work under null safety. Most Dart
code *is* dynamically correct and does avoid throwing null reference errors by
checking for `null` before calling methods. The new flow analysis on null checks
turns that *dynamic* correctness into provable *static* correctness.

這聽起來是件小事，但這種基於流程的空檢查提升，是大部分 Dart 程式碼能執行在空安全下的保障。
大部分的 Dart 程式碼 **是** 動態正確的，並且在呼叫前透過判斷 `null` 來避免丟擲空呼叫錯誤。
新的空安全流程分析將 **動態** 正確變成了更有保障的 **靜態** 正確。

It also, of course, works with the smarter analysis we do for reachability. The
above function can be written just as well as:

當然，它也同時和更智慧的分析一起進行檢查工作。
上面的函式也可以像下面這樣編寫：

```dart
// Using null safety:
String makeCommand(String executable, [List<String>? arguments]) {
  var result = executable;
  if (arguments == null) return result;
  return result + ' ' + arguments.join(' ');
}
```

The language is also smarter about what kinds of expressions cause promotion. An
explicit `== null` or `!= null` of course works. But explicit casts using `as`,
or assignments, or the postfix `!` operator we'll get to soon also cause
promotion. The general goal is that if the code is dynamically correct and it's
reasonable to figure that out statically, the analysis should be clever enough
to do so.

Dart 語言也對什麼表示式需要提升變數判斷地更智慧了。
除了顯式的 `== null` 和 `!= null` 以外，顯式使用 `as` 或賦值，
以及我們馬上就要提到的後置運運算元 `!` 也會進行型別提升。
總體來說的目標是：如果程式碼是動態正確的，而靜態分析時又是合理的，
那麼分析結果也足夠聰明，會對其進行型別提升。

Note that type promotion only works on local variables,
not on fields or top-level variables.
For more information about working with non-local variables,
see [Working with nullable fields](#working-with-nullable-fields).

請注意，型別提升僅對當前方法內的變數有效，對欄位和最上層變數無效。
想要了解更多針對非方法內的變數的處理，閱讀
[與可空欄位共舞](#working-with-nullable-fields)。

### Unnecessary code warnings

### 無用程式碼的警告

Having smarter reachability analysis and knowing where `null` can flow through
your program helps ensure that you *add* code to handle `null`. But we can also
use that same analysis to detect code that you *don't* need. Before null safety,
if you wrote something like:

在你的程式中，一個可以準確知曉 `null` 去向的可達性分析，
能確保你已經 **增加** 了對 `null` 的處理。
不過我們也可以用同樣的分析來檢測你是否有 **不用** 的程式碼。
在空安全以前，如果你編寫了如下的程式碼：

```dart
// Using null safety:
String checkList(List<Object> list) {
  if (list?.isEmpty ?? false) {
    return 'Got nothing';
  }
  return 'Got something';
}
```

Dart had no way of knowing if that null-aware `?.` operator is useful or not.
For all it knows, you could pass `null` to the function. But in null safe Dart,
if you have annotated that function with the now non-nullable `List` type, then
it knows `list` will never be `null`. That implies the `?.` will never do
anything useful and you can and should just use `.`.

Dart 無法得知避空運算子 `?.` 是否有用。它只知道你可以將 `null` 傳遞進方法內。
但是在有空安全的 Dart 裡，如果你將函式宣告為現有的非空 `List` 型別，
它就知道 `list` 永遠不會為空。
實際上就暗示了 `?.` 是不必要的，你完全可以直接使用 `.`。

To help you simplify your code, we've added warnings for unnecessary code like
this now that the static analysis is precise enough to detect it. Using a
null-aware operator or even a check like `== null` or `!= null` on a
non-nullable type gets reported as a warning.

為了幫助你簡化程式碼，我們為一些不必要的程式碼增加了一些警告，靜態分析可以精確地檢測到它。
在一個非空型別上使用避空運算子、用 `== null` 或 `!= null` 判斷，都會出現一個警告。

And, of course, this plays with non-nullable type promotion too. Once a
variable has been promoted to a non-nullable type, you get a warning if you
redundantly check it again for `null`:

同時，在非空型別提升的情況中也會看到類似的提示。
當一個變數已經被提升至非空型別，你會在不必要的 `null` 檢查時看到一個警告：

```dart
// Using null safety:
String checkList(List<Object>? list) {
  if (list == null) return 'No list';
  if (list?.isEmpty ?? false) {
    return 'Empty list';
  }
  return 'Got something';
}
```

You get a warning on the `?.` here because at the point that it executes, we
already know `list` cannot be `null`. The goal with these warnings is not just
to clean up pointless code. By removing *unneeded* checks for `null`, we ensure
that the remaining meaningful checks stand out. We want you to be able to look
at your code and *see* where `null` can flow.

此處由於程式碼執行後，`list` 不能為 `null`，所以你會在 `?.` 的呼叫處看到一個警告。
這些警告不僅僅是為了減少無意義的程式碼，
透過移除 **不必要** 的 `null` 判斷，我們得以確保其他有意義的判斷能夠脫穎而出。
我們期望你能 **看到** 你程式碼中的 `null` 會向何處傳遞。

## Working with nullable types

## 與可空型別共舞

We've now corralled `null` into the set of nullable types. With flow analysis,
we can safely let some non-`null` values hop over the fence to the non-nullable
side where we can use them. That's a big step, but if we stop here, the
resulting system is still painfully restrictive. Flow analysis only helps with
locals and parameters.

現在，我們已經將 `null` 歸到了可空型別的集合中。
有了流程分析，我們可以讓一些非 `null` 值安全地越過柵欄，到達非空的那一側，供我們使用。
這是相當大的一步，但如果我們就此止步不前，產出的系統仍然飽含痛苦的限制，
而流程分析也僅對區域變數和引數起作用。

To try to regain as much of the flexibility that Dart had before null
safety—and to go beyond it on some places—we have a handful of other
new features.

為了儘可能地保持 Dart 在擁有空安全之前的靈活度，並且在一定程度上超越它，
我們帶來了一些其他的實用新特性。

### Smarter null-aware methods

### 更智慧的空判斷方法

Dart's null aware operator `?.` is much older than null safety. The runtime
semantics state that if the receiver is `null` then the property access on the
right-hand side is skipped and the expression evaluates to `null`:

Dart 的避空運算子 `?.` 相對空安全而言儼然是一位老生。
根據執行時的語義化規定，如果接收者是 `null`，那麼右側的屬性存取就會被跳過，
表示式將被作為 `null` 看待。

```dart
// Without null safety:
String notAString = null;
print(notAString?.length);
```

Instead of throwing an exception, this prints "null". The null-aware operator is
a nice tool for making nullable types usable in Dart. While we can't let you
call methods on nullable types, we can and do let you use null-aware operators
on them. The post-null safety version of the program is:

這段程式碼將列印 “null”，而不是丟擲一個例外。
避空運算子是一個不錯的工具，讓可空型別在 Dart 中變得可用。
儘管我們不能讓你在可空型別上呼叫方法，但我們可以讓你使用避空運算子呼叫它們。
空安全版本的程式是這樣的：

```dart
// Using null safety:
String? notAString = null;
print(notAString?.length);
```

It works just like the previous one.

與之前一樣，它可以正常執行。

However, if you've ever used null-aware operators in Dart, you've probably
encountered an annoyance when using them in method chains. Let's say you want to
see if the length of a potentially absent string is an even number (not a
particularly realistic problem, I know, but work with me here):

然而，如果你曾經在 Dart 中使用過避空運算子，你可能經歷過鏈式方法呼叫的惱人操作。
假設你需要判斷一個可能為空的字串的長度是否為偶數
（這可能不是個貼合實際的問題，但請繼續往下看）：

```dart
// Using null safety:
String? notAString = null;
print(notAString?.length.isEven);
```

Even though this program uses `?.`, it still throws an exception at runtime. The
problem is that the receiver of the `.isEven` expression is the result of the
entire `notAString?.length` expression to its left. That expression evaluates to
`null`, so we get a null reference error trying to call `.isEven`. If you've
ever used `?.` in Dart, you probably learned the hard way that you have to apply
the null-aware operator to *every* property or method in a chain after you use
it once:

就算這個程式使用了 `?.`，它仍然會在執行時丟擲例外。
這裡的問題在於，`.isEven` 的接收器是左側整個 `notAString?.length` 表示式的結果。
這個表示式被認為是 `null`，所以我們在嘗試呼叫 `.isEven` 的時候出現了空參考的錯誤。
如果你在 Dart 中使用過 `?.`，你可能已經學會了一個非常繁瑣的方法，
那就是在使用了一次避空運算子後，其 **每一處** 屬性或方法的鏈式呼叫處都加上它。

```dart
String? notAString = null;
print(notAString?.length?.isEven);
```

This is annoying, but, worse, it obscures important information. Consider:

這非常煩人，但更致命的是，它會擾亂重要資訊的獲取。看看下面這個：

```dart
// Using null safety:
showGizmo(Thing? thing) {
  print(thing?.doohickey?.gizmo);
}
```

Here's a question for you: Can the `doohickey` getter on `Thing` return `null`?
It looks like it *could* because you're using `?.` on the result. But it may
just be that the second `?.` is only there to handle cases where `thing` is
`null`, not the result of `doohickey`. You can't tell.

這裡我們想問你一個問題：`Thing` 中獲取 `doohickey` 是否會返回 `null`？
看上去它 **會** 返回 `null`，因為你在呼叫後使用了 `?.`。
但也有可能第二個 `?.` 僅僅是為了處理 `thing` 為 `null` 的情況，
而不是 `doohickey` 的結果。你無法直接得出結論。

To address this, we borrowed a smart idea from C#'s design of the same feature.
When you use a null-aware operator in a method chain, if the receiver evaluates
to `null`, then *the entire rest of the method chain is short-circuited and
skipped*. This means if `doohickey` has a non-nullable return type, then you
can and should write:

為了解決這類問題，我們從 C# 相同功能的設計中借鑑了一個聰明的處理方法。
當你在鏈式方法呼叫中使用避空運算子時，如果接收器被判斷為 `null`，
那麼 **整個鏈式呼叫的剩餘部分都會被截斷並跳過**。
這意味著如果 `doohickey` 的返回值是一個非空的型別，你應該這樣寫：

```dart
// Using null safety:
showGizmo(Thing? thing) {
  print(thing?.doohickey.gizmo);
}
```

In fact, you'll get an unnecessary code warning on the second `?.` if you
don't. If you see code like:

實際上，如果你不去掉第二個 `?.`，你會看到一個警告，提示這段程式碼是不必要的。
所以如果你看到了這樣的程式碼：

```dart
// Using null safety:
showGizmo(Thing? thing) {
  print(thing?.doohickey?.gizmo);
}
```

Then you know for certain it means that `doohickey` itself has a nullable return
type. Each `?.` corresponds to a *unique* path that can cause `null` to flow
into the method chain. This makes null-aware operators in method chains both
more terse and more precise.

你立刻就會知道 `doohickey` 本身的返回型別就是可空的。
每一個 `?.` 對應一個 **獨一無二的** 程式碼路徑，能夠讓 `null` 隨著鏈式呼叫傳遞。
這就讓鏈式方法呼叫中的避空運算子更為簡潔和精確。

While we were at it, we added a couple of other null-aware operators:

同時，我們也在這裡加入了一些其他的避空運算子：

```dart
// Using null safety:

// Null-aware cascade:
receiver?..method();

// Null-aware index operator:
receiver?[index];
```

There isn't a null-aware function call operator, but you can write:

目前還沒有空判斷函式呼叫運運算元，但是你可以這樣寫：

```dart
// Allowed with or without null safety:
function?.call(arg1, arg2);
```

### Null assertion operator

### 空值斷言運運算元

The great thing about using flow analysis to move a nullable variable to the
non-nullable side of the world is that doing so is provably safe. You get to
call methods on the previously-nullable variable without giving up any of the
safety or performance of non-nullable types.

利用流程分析，將可空的變數轉移到非空的一側，是安全可靠的。
你可以在先前可空的變數上呼叫方法，同時還能享受到非空型別的安全和效能優勢。

But many valid uses of nullable types can't be *proven* to be safe in a way that
pleases static analysis. For example:

但是，很多有效的可空型別使用方法，不能向靜態分析 **證明** 它們的安全性。例如：

```dart
// Using null safety, incorrectly:
class HttpResponse {
  final int code;
  final String? error;

  HttpResponse.ok()
      : code = 200,
        error = null;
  HttpResponse.notFound()
      : code = 404,
        error = 'Not found';

  @override
  String toString() {
    if (code == 200) return 'OK';
    return 'ERROR $code ${error.toUpperCase()}';
  }
}
```

If you try to run this, you get a compile error on the call to `toUpperCase()`.
The `error` field is nullable because it won't have a value in a successful
response. We can see by inspecting the class that we never access the `error`
message when it is `null`. But that requires understanding the relationship
between the value of `code` and the nullability of `error`. The type checker
can't see that connection.

如果你嘗試執行這段程式碼，你會看到一個指向 `toUpperCase()` 呼叫的編譯錯誤。
`error` 屬性是可空的，在返回結果成功時，它不會有值。
我們透過仔細觀察類可以看出，當訊息為空時，我們永遠不會存取 `error`。
但為了知曉這個行為，必須要理解 `code` 的值與 `error` 的可空性之間的聯絡。
型別檢查器看不出這種聯絡。

In other words, we human maintainers of the code *know* that error won't be
`null` at the point that we use it and we need a way to assert that. Normally,
you assert types using an `as` cast, and you can do the same thing here:

換句話說，作為程式碼的人類維護者，我們知道在使用 error 時，
它的值不會是 `null`，並且我們需要對其進行斷言。
通常你可以透過使用 `as` 轉換來斷言型別，這裡你也可以這樣做：

```dart
// Using null safety:
String toString() {
  if (code == 200) return 'OK';
  return 'ERROR $code ${(error as String).toUpperCase()}';
}
```

Casting `error` to the non-nullable `String` type will throw a runtime exception
if the cast fails. Otherwise, it gives us a non-nullable string that we can then
call methods on.

如果在執行時，將 `error` 轉換為非空的 `String` 型別出現了無法轉換的錯誤，會丟擲一個例外。
若轉換成功，一個非空的字串就會回到我們的手上，讓我們可以進行方法呼叫。

"Casting away nullability" comes up often enough that we have a new shorthand
syntax. A postfix exclamation mark (`!`) takes the expression on the left and
casts it to its underlying non-nullable type. So the above function is
equivalent to:

“排除可空性的轉換”的場景頻繁出現，這促使了我們帶來了新的短小精悍的語法。
一個作為字尾的感嘆號標記 (`!`) 會讓左側的表示式轉換成其對應的非空型別。
所以上面的函式等效於：

```dart
// Using null safety:
String toString() {
  if (code == 200) return 'OK';
  return 'ERROR $code ${error!.toUpperCase()}';
}
```

This one-character "bang operator" is particularly handy when the underlying
type is verbose. It would be really annoying to have to write `as
Map<TransactionProviderFactory, List<Set<ResponseFilter>>>` just to cast away a
single `?` from some type.

當原有的型別非常繁瑣的時候，這個只有一個字元的“重點運運算元”就會非常上手。
如果僅僅是為了將一個類別型轉換為非空，就需要寫出類似於
`as Map<TransactionProviderFactory, List<Set<ResponseFilter>>>`
這樣的程式碼，會讓這個過程變得非常煩人。

Of course, like any cast, using `!` comes with a loss of static safety. The cast
must be checked at runtime to preserve soundness and it may fail and throw an
exception. But you have control over where these casts are inserted, and you can
always see them by looking through your code.

當然，與其他所有轉換一樣，使用 `!` 將會失去部分靜態的安全性。
這些轉換必須在執行時進行，從而確保程式碼健全，並且有可能失敗並丟擲例外。
但你可以完全控制這些轉換的使用位置，並且能從程式碼中直接看到它們。

### Late variables

### 延遲載入的變數

The most common place where the type checker cannot prove the safety of code is
around top-level variables and fields. Here is an example:

對於最上層變數和欄位而言，型別檢查器常常無法證明其是否安全。這裡有一個例子：

```dart
// Using null safety, incorrectly:
class Coffee {
  String _temperature;

  void heat() { _temperature = 'hot'; }
  void chill() { _temperature = 'iced'; }

  String serve() => _temperature + ' coffee';
}

main() {
  var coffee = Coffee();
  coffee.heat();
  coffee.serve();
}
```

Here, the `heat()` method is called before `serve()`. That means `_temperature`
will be initialized to a non-null value before it is used. But it's not feasible
for a static analysis to determine that. (It might be possible for a trivial
example like this one, but the general case of trying to track the state of each
instance of a class is intractable.)

在這裡，`heat()` 方法在 `serve()` 之前就被呼叫了。
這意味著 `_temperature` 會在它被使用前初始化為一個非空的值。
但對於靜態分析而言，這樣是不可行的。
（實際上在與例子類別似的情況下，程式碼可能是可行的，但是在一般情況下，我們難以追蹤每一個例項的狀態。）

Because the type checker can't analyze uses of fields and top-level variables,
it has a conservative rule that non-nullable fields have to be initialized
either at their declaration (or in the constructor initialization list for
instance fields). So Dart reports a compile error on this class.

由於型別檢查器無法分析欄位和最上層變數的用途，因此它遵循一個相對保守的規則，
即不可空的欄位必須在宣告時初始化（或是在建構函式的初始化欄位列表中）。
所以在這裡，Dart 會在這個類上提示一個編譯錯誤。

You can fix the error by making the field nullable and then using null assertion
operators on the uses:

為了解決這個問題，你可以將它宣告為可空，接著使用空斷言運運算元：

```dart
// Using null safety:
class Coffee {
  String? _temperature;

  void heat() { _temperature = 'hot'; }
  void chill() { _temperature = 'iced'; }

  String serve() => _temperature! + ' coffee';
}
```

This works fine. But it sends a confusing signal to the maintainer of the class.
By marking `_temperature` nullable, you imply that `null` is a useful,
meaningful value for that field. But that's not the intent. The `_temperature`
field should never be *observed* in its `null` state.

這樣一來，程式碼確實可以正常工作了。但是它讓這個類別的維護人員感到困惑。
將 `_temperature` 變為可空，暗示著 `null` 對於欄位來說是有用的值。
但實際上其與你的企圖背道而馳。
`_temperature` 欄位永遠不會在為 `null` 的情況下 **被觀測到**。

To handle the common pattern of state with delayed initialization, we've added a
new modifier, `late`. You can use it like this:

為了處理類似延遲初始化這樣常見的行為，我們新增了一個修飾符：`late`。你可以這樣使用：

```dart
// Using null safety:
class Coffee {
  late String _temperature;

  void heat() { _temperature = 'hot'; }
  void chill() { _temperature = 'iced'; }

  String serve() => _temperature + ' coffee';
}
```

Note that the `_temperature` field has a non-nullable type, but is not
initialized. Also, there's no explicit null assertion when it's used. There are
a few models you can apply to the semantics of `late`, but I think of it like
this: The `late` modifier means "enforce this variable's constraints at runtime
instead of at compile time". It's almost like the word "late" describes *when*
it enforces the variable's guarantees.

此處我們注意到，`_temperature` 欄位是一個非空的型別，但是並沒有進行初始化。
同時，在使用時也沒有明確的空斷言。
雖然 `late` 應用的語義有幾種解釋，但在這裡應該是：
`late` 修飾符是“在執行時而非編譯時對變數進行約束”。
這就讓 `late` 這個詞語約等於 **何時** 執行對變數的強制約束。

In this case, since the field is not definitely initialized, every time the
field is read, a runtime check is inserted to make sure it has been assigned a
value. If it hasn't, an exception is thrown. Giving the variable the type
`String` means "you should never see me with a value other than a string" and
the `late` modifier means "verify that at runtime".

當前場景裡，欄位並不一定已經被初始化，
每次它被讀取時，都會插入一個執行時的檢查，以確保它已經被賦值。
如果並未賦值，就會丟擲一個例外。
給一個變數加上 `String` 型別就是在說：“我的值絕對是字串”，
而加上 `late` 修飾符意味著：“每次執行都要檢查檢查是不是真的”。

In some ways, the `late` modifier is more "magical" than using `?` because any
use of the field could fail, and there isn't anything textually visible at the
use site. But you *do* have to write `late` at the declaration to get this
behavior, and our belief is that seeing the modifier there is explicit enough
for this to be maintainable.

在某些方面，`late` 修飾符比 `?` 更為神奇，因為對這個欄位的任何呼叫都有可能失敗，
且在失敗的事故現場不會有任何的文字說明。

In return, you get better static safety than using a nullable type. Because the
field's type is non-nullable now, it is a *compile* error to try to assign
`null` or a nullable `String` to the field. The `late` modifier lets you *defer*
initialization, but still prohibits you from treating it like a nullable
variable.

作為回報，它在靜態安全方面比可空型別更靠譜。
因為這個欄位現在是非空的了，在 **編譯時** 為它賦予 `null` 或可空的 `String` 就會出錯。
雖然 `late` 修飾符讓你延遲了初始化，但它仍然禁止你將變數作為可空的型別進行處理。

### Lazy initialization

### 延遲初始化

The `late` modifier has some other special powers too. It may seem paradoxical,
but you can use `late` on a field that has an initializer:

`late` 修飾符也有一些特殊的能力。
雖然聽起來起來有一些自相矛盾，但是你可以在一個包含初始化內容的欄位上使用 `late`：

```dart
// Using null safety:
class Weather {
  late int _temperature = _readThermometer();
}
```

When you do this, the initializer becomes *lazy*. Instead of running it as soon
as the instance is constructed, it is deferred and run lazily the first time the
field is accessed. In other words, it works exactly like an initializer on a
top-level variable or static field. This can be handy when the initialization
expression is costly and may not be needed.

當你這麼宣告時，會讓初始化 **延遲** 執行。
例項的構造將會延遲到欄位首次被存取時執行，而不是在例項構造時就初始化。
換句話說，它讓欄位的初始化方式變得與最上層變數和靜態欄位完全一致。
當初始化表示式比較消耗效能，並且有可能不需要時，這會變得非常有用。

Running the initializer lazily gives you an extra bonus when you use `late` on
an instance field. Usually instance field initializers cannot access `this`
because you don't have access to the new object until all field initializers
have completed. But with a `late` field, that's no longer true, so you *can*
access `this`, call methods, or access fields on the instance.

當你在例項欄位上使用 `late` 時，延遲初始化會給你帶來更多的便利。
通常例項欄位的初始化內容無法存取到 `this`，
因為在所有的初始化方法完成前，你無法存取到新的例項物件。
但是，使用了 `late` 讓這個條件不再為真，
所以你 **可以** 存取到 `this`、呼叫方法以及存取例項的欄位。

### Late final variables

### 延遲的終值

You can also combine `late` with `final`:

你也可以將 `late` 與 `final` 結合使用：

```dart
// Using null safety:
class Coffee {
  late final String _temperature;

  void heat() { _temperature = 'hot'; }
  void chill() { _temperature = 'iced'; }

  String serve() => _temperature + ' coffee';
}
```

Unlike normal `final` fields, you do not have to initialize the field in its
declaration or in the constructor initialization list. You can assign to it
later at runtime. But you can only assign to it *once*, and that fact is checked
at runtime. If you try to assign to it more than once—like calling both
`heat()` and `chill()` here—the second assignment throws an exception.
This is a great way to model state that gets initialized eventually and is
immutable afterwards.

與普通的 `final` 欄位不同，你不需要在宣告或構造時就將其初始化。
你可以稍後在執行中的某個地方載入它。
但是你只能對其進行 **一次** 賦值，並且它在執行時會進行校驗。
如果你嘗試對它進行多次賦值，比如 `heat()` 和 `chill()` 都呼叫，
那麼第二次的賦值會丟擲例外。
這是確定欄位狀態的好方法，它最終會被初始化，並且在初始化後是無法改變的。

In other words, the new `late` modifier in combination with Dart's other
variable modifiers covers most of the feature space of `lateinit` in Kotlin and
`lazy` in Swift. You can even use it on local variables if you want a little
local lazy evaluation.

換句話說，新的 `late` 修飾符與 Dart 的其他變數修飾符結合後，
已經實現了 Kotlin 中的 `lateinit` 和 Swift 中的 `lazy` 的大量特性。
如果你需要給區域變數加上一些延遲初始化，你也可以在區域變數上使用它。

### Required named parameters

### 必需的命名引數

To guarantee that you never see a `null` parameter with a non-nullable type, the
type checker requires all optional parameters to either have a nullable type or
a default value. What if you want to have a named parameter with a non-nullable
type and no default value? That would imply that you want to require the caller
to *always* pass it. In other words, you want a parameter that is *named*
but not optional.

為了保證你永遠不會看到一個非空型別的引數值為 `null`，
型別檢查器給所有的可選引數提出了要求，要麼是一個可空的型別，要麼包含一個預設值。
如果你需要一個可空的命名引數，同時又不包含預設值，該怎麼辦呢？
這就意味著你要求呼叫者 **每次** 都為其傳遞內容。
換句話說，你需要的是一個非可選的 **命名** 引數。

I visualize the various kinds of Dart parameters with this table:

這個表格直觀地展示了 Dart 的各種引數：

```
                必需的        可選的
            +------------+------------+
位置引數     | f(int x)   | f([int x]) |
            +------------+------------+
命名引數     | ???        | f({int x}) |
            +------------+------------+
```

For unclear reasons, Dart has long supported three corners of this table but
left the combination of named+mandatory empty. With null safety, we filled that
in. You declare a required named parameter by placing `required` before the
parameter:

Dart 為何長期以來只支援三種引數型別，而不支援 命名+必需 組合的引數，仍然是未解之謎。
隨著空安全的引入，我們將這個空缺的引數型別補充上了。
現在你只需要將 `required` 放在引數前，就可以宣告一個必需的命名引數：

```dart
// Using null safety:
function({int? a, required int? b, int? c, required int? d}) {}
```

Here, all the parameters must be passed by name. The parameters `a` and `c` are
optional and can be omitted. The parameters `b` and `d` are required and must
be passed. Note that required-ness is independent of nullability. You can have
required named parameters of nullable types, and optional named parameters of
non-nullable types (if they have a default value).

這裡的所有引數都必須透過命名來傳遞。
引數 `a` 和 `c` 是可選的，可以省略。
引數 `b` 和 `d` 是必需的，呼叫時必須傳遞。
在這裡請注意，是否必需和是否可空無關。
你可以寫出可空型別的必需命名引數，以及非空型別的可選命名引數（如果它們包含了預設值）。

This is another one of those features that I think makes Dart better regardless
of null safety. It simply makes the language feel more complete to me.

無論是否為空安全，這都是另一個讓 Dart 變得更好的特性之一。
它讓這門語言看起來更為完整。

### Abstract fields

### 抽象欄位

One of the neat features of Dart is that
it upholds a thing called the [uniform access principle][].
In human terms it means that
fields are indistinguishable from getters and setters.
It's an implementation detail whether a "property" in some Dart class
is computed or stored.
Because of this,
when defining an interface using an abstract class,
it's typical to use a field declaration:

Dart 有一項好用的功能，即其擁有 [統一存取原則][uniform access principle]。
意思是欄位和拆分的 getter 和 setter 沒有區別。
這是 Dart 中一個類別的「屬性」是否進行計算和儲存的實現細節。
因為這項功能的存在，當你在定義抽象類別的介面時，
會經常使用欄位宣告的形式：

[uniform access principle]: https://en.wikipedia.org/wiki/Uniform_access_principle

```dart
abstract class Cup {
  Beverage contents;
}
```

The intent is that users only implement that class and don't extend it.
The field syntax is simply a shorter way of writing a getter/setter pair:

使用者應只能實現這個類，而不能對其進行擴充。
這樣的欄位定義陳述式只是一對 getter 和 setter 的簡寫形式：

```dart
abstract class Cup {
  Beverage get contents;
  set contents(Beverage);
}
```

But Dart doesn't *know* that this class will never be used as a concrete type.
It sees that `contents` declaration as a real field.
And, unfortunately, that field is non-nullable and has no initializer,
so you get a compile error.

但是 Dart 並不 **瞭解** 這個類是否會被用於具體型別的定義。
它會認為 `contents` 是一個真實定義存在的欄位。
所以，該欄位是非空型別卻沒有進行初始化，你會在編譯時看到一個編譯錯誤，屬實不幸。

One fix is to use explicit abstract getter/setter declarations
like in the second example.
But that's a little verbose,
so with null safety
we also added support for explicit abstract field declarations:

解決這個問題的其中一種方法是像第二種範例那樣，顯式宣告抽象的 getter 和 setter。
但這樣寫起來過於冗長，所以隨著空安全的推出，我們一併增加了顯式宣告抽象欄位的支援：

```dart
abstract class Cup {
  abstract Beverage contents;
}
```

This behaves exactly like the second example.
It simply declares an abstract getter and setter with the given name and type.

這段程式碼與第二種範例行為一致。
它用非常簡潔的方式聲明瞭指定名稱和型別抽象 getter 和 setter。

### Working with nullable fields

### 與可空欄位共舞

These new features cover many common patterns and make working with `null`
pretty painless most of the time. But even so, our experience is that nullable
fields can still be difficult. In cases where you can make the field `late` and
non-nullable, you're golden. But in many cases you need to *check* to see if the
field has a value, and that requires making it nullable so you can observe the
`null`.

新引入的特性處理了非常多常見的行為模式，並且讓大部分處理 `null` 的工作不再那麼痛苦。
即便如此，根據我們的經驗之談，處理可空的欄位仍然是較為困難的。
在你能使用 `late` 和非空型別的情況下，已經相當穩妥。
但在很多場景裡，你仍然需要 **檢查** 欄位是否有值，
這些情況下，欄位會是可空的，你也能觀測到 `null` 的存在。

You might expect this to work:

以下這段程式碼，你可能會認為可以這麼寫：

```dart
// Using null safety, incorrectly:
class Coffee {
  String? _temperature;

  void heat() { _temperature = 'hot'; }
  void chill() { _temperature = 'iced'; }

  void checkTemp() {
    if (_temperature != null) {
      print('Ready to serve ' + _temperature + '!');
    }
  }

  String serve() => _temperature! + ' coffee';
}
```

Inside `checkTemp()`, we check to see if `_temperature` is `null`. If not, we
access it and end up calling `+` on it. Unfortunately, this is not allowed.
Flow-based type promotion does not apply to fields because the static analysis
cannot *prove* that the field's value doesn't change between the point that you
check for `null` and the point that you use it. (Consider that in pathological
cases, the field itself could be overridden by a getter in a subclass that
returns `null` the second time it is called.)

在 `checkTemp()` 中，我們檢查了 `_temperature` 是否為 `null`。
如果不為空，我們會存取並對它呼叫 `+`。
很遺憾，這樣做是不被允許的。
基於流程分析的型別提升並不適用於欄位，
因為靜態分析不能 **證明** 這個欄位的值在你判斷後和使用前沒有發生變化。
（某些極端場景中，欄位本身可能會被子類別的 getter 重寫，從而在第二次呼叫時返回 `null`。）

So, since we care about soundness, fields don't promote and the above method
does not compile. This is annoying. In simple cases like here, your best bet is
to slap a `!` on the use of the field. It seems redundant, but that's more or
less how Dart behaves today.

因為程式碼的健全性也是我們在乎的指標，所以欄位的型別不會被提升，且上面的方法也無法編譯。
這其實不太舒服。在這樣的簡單例子中，最好的辦法是在使用欄位時加上 `!`。
它看起來是多餘的，但是目前的 Dart 需要這樣的操作。

Another pattern that helps is to copy the field to a local variable first and
then use that instead:

還有一種可以解決這類情況的方法，就是先將欄位複製為一個區域變數，然後再使用它：

```dart
// Using null safety:
void checkTemp() {
  var temperature = _temperature;
  if (temperature != null) {
    print('Ready to serve ' + temperature + '!');
  }
}
```

Since the type promotion does apply to locals, this now works fine. If you need
to *change* the value, just remember to store back to the field and not just the
local.

對於區域變數而言，型別提升是有效的，所以它會正常執行。
如果你需要 **更改** 它的值，記得要儲存回原有的欄位，不要只更新了你的區域變數。

For more information on handling these and other type promotion issues,
see [Fixing type promotion failures](/tools/non-promotion-reasons).

想要了解更多關於如何處理型別提升問題的方法，檢視
[處理型別提升的失敗情況](/tools/non-promotion-reasons)。

### Nullability and generics

### 可空性和泛型

Like most modern statically-typed languages, Dart has generic classes and
generic methods. They interact with nullability in a few ways that seem
counter-intuitive but make sense once you think through the implications. First
is that "is this type nullable?" is no longer a simple yes or no question.
Consider:

與現今主流的靜態型別語言一樣，Dart 也有泛型類和泛型方法。
它們在與可空性的互動上，會有一些反直覺的地方，
可一旦你想清楚了其中隱含的設計意圖，就會理解它們的合理性。
首先，“這個型別是否是可空？”已經不再是一個簡單的是非問題。
讓我們來考慮以下的情況：

```dart
// Using null safety:
class Box<T> {
  final T object;
  Box(this.object);
}

main() {
  Box<String>('a string');
  Box<int?>(null);
}
```

In the definition of `Box`, is `T` a nullable type or a non-nullable type? As
you can see, it can be instantiated with either kind. The answer is that `T` is a
*potentially nullable type*. Inside the body of a generic class or method, a
potentially nullable type has all of the restrictions of both nullable types
*and* non-nullable types.

在 `Box` 的定義中，`T` 是可空還是非空的型別？
正如你所看到的那樣，它可以透過任意一種型別來進行例項化。
答案是：`T` 是一個 **潛在的可空型別**。
在泛型類或泛型方法的主體中，一個潛在的可空型別包含了可空型別 **以及** 非空型別的所有限制。

The former means you can't call any methods on it except the handful defined on
Object. The latter means that you must initialize any fields or variables of
that type before they're used. This can make type parameters pretty hard to work with.

前者意味著除了在 Object 上定義的少數方法以外，不能呼叫其他的任何方法。
後者意味著這個型別的任何欄位或變數都需要在使用前被初始化。
這就會讓型別引數非常難處理。

In practice, a few patterns show up. In collection-like classes where the type
parameter can be instantiated with any type at all, you just have to deal with
the restrictions. In most cases, like the example here, it means ensuring you do
have access to a value of the type argument's type whenever you need to work
with one. Fortunately, collection-like classes rarely call methods on their
elements.

實際上，有一些模式已經在這麼處理了。
比如一個類別似集合的類在例項化時，型別引數可以使用任何型別。
你只需要在使用例項時，用合適的方式處理型別相關的約束即可。
而在像此處的例子一樣的大部分場景中，這樣做意味著每當你需要使用型別引數的型別的值時，
都可以確保你能存取這個值。
幸運的是，類似集合的類很少直接在其元素上呼叫方法。

In places where you don't have access to a value, you can make the use of the
type parameter nullable:

在你不需要存取值的時候，你可以將型別引數變為可空：

```dart
// Using null safety:
class Box<T> {
  T? object;
  Box.empty();
  Box.full(this.object);
}
```

Note the `?` on the declaration of `object`. Now the field has an explicitly
nullable type, so it is fine to leave it uninitialized.

注意此處對於 `object` 宣告的 `?`。
現在這個欄位是一個顯式的可空型別，所以它可以是未被初始化的。

When you make a type parameter type nullable like `T?` here, you may need to
cast the nullability away. The correct way to do that is using an explicit `as
T` cast, *not* the `!` operator:

當你將型別引數像此處一樣變為可空型別時，你可能需要強制將它轉換為非空型別。
正確的做法是明確地使用 `as T` 進行轉換，**而不是** 使用 `!` 運運算元。

```dart
// Using null safety:
class Box<T> {
  T? object;
  Box.empty();
  Box.full(this.object);

  T unbox() => object as T;
}
```

The `!` operator *always* throws if the value is `null`. But if the type
parameter has been instantiated with a nullable type, then `null` is a perfectly
valid value for `T`:

如果值為 `null`，使用 `!` 運運算元 **一定** 會丟擲例外。
但是如果型別引數已被宣告為一個可空的型別，那麼 `null` 對於 `T` 就是一個完全有效的值：

```dart
// Using null safety:
main() {
  var box = Box<int?>.full(null);
  print(box.unbox());
}
```

This program should run without error. Using `as T` accomplishes that. Using
`!` would throw an exception.

這段程式碼可以正常執行，完全歸功於使用了 `as T`，而如果使用 `!` 就會丟擲例外。

Other generic types have some bound that restricts the kinds of type arguments
that can be applied:

其他的泛型也存在一些限制可用型別引數類別的型別約束：

```dart
// Using null safety:
class Interval<T extends num> {
  T min, max;

  Interval(this.min, this.max);

  bool get isEmpty => max <= min;
}
```

If the bound is non-nullable, then the type parameter is also non-nullable. This
means you have the restrictions of non-nullable types—you can't leave
fields and variables uninitialized. The example class here must have a
constructor that initializes the fields.

如果型別的約束是非空的，那麼型別引數也是非空的。
這就意味著你會受到非空型別的一些限制，即必須要初始化欄位和變數。
範例中的類必須要有建構函式來對欄位進行初始化。

In return for that restriction, you can call any methods on values of the type
parameter type that are declared on its bound. Having a non-nullable bound does,
however, prevent *users* of your generic class from instantiating it with a
nullable type argument. That's probably a reasonable limitation for most
classes.

這些限制同時也帶來了一些好處，你可以呼叫型別引數繼承自其型別約束的任何方法。
當然，非空的型別約束會阻止 **使用者** 用可空的型別引數對泛型進行例項化。
對於大部分類來說，這也是合理的限制。

You can also use a nullable *bound*:

你也可以使用可空的**型別約束**：

```dart
// Using null safety:
class Interval<T extends num?> {
  T min, max;

  Interval(this.min, this.max);

  bool get isEmpty {
    var localMin = min;
    var localMax = max;

    // No min or max means an open-ended interval.
    if (localMin == null || localMax == null) return false;
    return localMax <= localMin;
  }
}
```

This means that in the body of the class you get the flexibility of treating the
type parameter as nullable, but you also have the limitations of nullability. 
You can't call anything on a variable of that type
unless you deal with the nullability first. In the example here, 
we copy the fields in local variables and check those locals for `null` 
so that flow analysis promotes them to non-nullable types before we use `<=`.


這意味著在類別的主體中，你擁有了將型別引數作為可空型別來處理的靈活性，
但你也受到了可空性的限制&mdash;&mdash;除非你先處理了可空狀態，否則你無法呼叫變數的任何方法。
在此處的例子中，我們將欄位複製至區域變數，並且檢查了它們是否為 `null`，
所以在我們呼叫 `<=` 前，流程分析將它們提升成了非空型別。

Note that a nullable bound does not prevent users from instantiating the class
with non-nullable types. A nullable bound means that the type argument *can* be
nullable, not that it *must*. (In fact, the default bound on type parameters if
you don't write an `extends` clause is the nullable bound `Object?`.) There is
no way to *require* a nullable type argument. If you want uses of the type
parameter to reliably be nullable and be implicitly initialized to `null`, 
you can use `T?` inside the body of the class.

請注意，可空的型別約束並不會阻止使用者使用非空型別對類進行例項化。
一個可空的型別約束意味著型別引數 **可以** 為空，而不是 **必須** 為空。
（實際上，如果你沒有寫上 `extends` 陳述式，型別引數的預設型別約束是可空的 `Object?`。）
你沒有辦法宣告一個 **必需的** 可空型別引數。
如果你希望確保型別引數一定是可空且以 `null` 隱含初始化，
你可以在類別的主體中使用 `T?`。

## Core library changes

## 核心函式庫的改動

There are a couple of other tweaks here and there in the language, but they are
minor. Things like the default type of a `catch` with no `on` clause is now
`Object` instead of `dynamic`. Fallthrough analysis in switch statements uses
the new flow analysis.

我們在語言上還有一些其他微小的細節調整。
例如沒有使用 `on` 的 `catch` 現在返回的預設型別是 `Object` 而不是 `dynamic`。
同時，switch 陳述式中的條件貫穿分析也使用了新的流程分析。

The remaining changes that really matter to you are in the core libraries.
Before we embarked on the Grand Null Safety Adventure, we worried that it would
turn out there was no way to make our core libraries null safe without massively
breaking the world. It turned out not so dire. There *are* a few significant
changes, but for the most part, the migration went smoothly. Most core libraries
either did not accept `null` and naturally move to non-nullable types, or do and
gracefully accept it with a nullable type.

剩餘的重要改動，都存在於核心函式庫中。
在我們開始這次的空安全大冒險之前，我們曾經擔心過，
為了讓核心函式庫用上空安全，也許我們要對現有的語言系統做出大規模的破壞性改動。
而結果並沒有想象中的那麼可怕。
儘管確實 **有** 一些重大的變化，但在大部分情況下，遷移進行得十分順利。
大多數的核心庫要麼不接受 `null` ，從而自然地使用了非空的型別，
要麼接受了 `null`，並且優雅地處理了可空型別。

There are a few important corners, though:

不過，這裡還有一些比較重要的變動細節：

### The Map index operator is nullable

### Map 的索引運運算元是可空的

This isn't really a change, but more a thing to know. The index `[]` operator on
the Map class returns `null` if the key isn't present. This implies that the
return type of that operator must be nullable: `V?` instead of `V`.

這其實算不上一個改動，但你應該瞭解一下。
Map 類別的 `[]` 運運算元會在鍵值不存在時返回 `null`。
這就暗示了運運算元的返回型別必須是可空的 `V?` 而不是 `V`。

We could have changed that method to throw an exception when the key isn't
present and then given it an easier-to-use non-nullable return type. But code
that uses the index operator and checks for `null` to see if the key is absent
is very common, around half of all uses based on our analysis. Breaking all of
that code would have set the Dart ecosystem aflame.

我們本可以在鍵值不存在時丟擲例外，並且將返回型別改為更易使用的非空型別。
但是，透過索引運運算元判斷 `null` 來確認鍵值是否存在，是一個非常常見的操作，
經過我們的分析，大約有一半的操作是這樣的用途。
如果破壞了這些程式碼，會直接摧毀 Dart 的生態系統。

Instead, the runtime behavior is the same and thus the return type is obliged
to be nullable. This means you generally cannot immediately use the result of
a map lookup:

實際上，執行時的行為還是一樣的，因此返回型別必須是可空的。
這意味著你無法在 Map 查詢時立馬使用查詢的結果：

```dart
// Using null safety, incorrectly:
var map = {'key': 'value'};
print(map['key'].length); // Error.
```

This gives you a compile error on the attempt to call `.length` on a nullable
string. In cases where you *know* the key is present you can teach the type
checker by using `!`:

這段程式碼會在 `.length` 的呼叫處丟擲一個編譯例外，因為你嘗試呼叫可空的字串。
在你已經 **確定** 鍵值存在的情況下，你可以給型別檢查器上一個 `!`：

```dart
// Using null safety:
var map = {'key': 'value'};
print(map['key']!.length); // OK.
```

We considered adding another method to Map that would do this for you: look up
the key, throw if not found, or return a non-nullable value otherwise. But what
to call it? No name would be shorter than the single-character `!`, and no
method name would be clearer than seeing a `!` with its built-in semantics right
there at the call site. So the idiomatic way to access a known-present element
in a map is to use `[]!`. You get used to it.

我們曾經考慮過為 Map 增加另一個方法，幫助你辦到這件事：
查詢鍵值，如果沒找到則丟擲例外，否則返回一個非空值。
但是我們應該怎麼稱呼它？
任何名字都不如一個 `!` 來的簡短，也沒有任何一個方法的名字會比一個 `!` 的呼叫語義更清晰。
所以，在 Map 查詢一個已知存在的元素的方法是 `[]!`。
相信你會慢慢習慣的。

### No unnamed List constructor

### 去除 List 的非命名構造

The unnamed constructor on `List` creates a new list with the given size but
does not initialize any of the elements. This would poke a very large hole in
the soundness guarantees if you created a list of a non-nullable type and then
accessed an element.

`List` 的非命名建構函式會建立一個給定大小的列表，但是並沒有為任何元素進行初始化。
如果你建立了一個非空型別的列表，接著訪問了其中一個元素，這將會是巨大的漏洞。

To avoid that, we have removed the constructor entirely. It is an error to call
`List()` in null safe code, even with a nullable type. That sounds scary, but
in practice most code creates lists using list literals, `List.filled()`,
`List.generate()`, or as a result of transforming some other collection. For
the edge case where you want to create an empty list of some type, we added a
new `List.empty()` constructor.

為了避免這樣的情況發生，我們將這個建構函式完全移除了。
在空安全的程式碼中，就算是一個可空的型別，呼叫 `List()` 都會丟擲錯誤。
聽起來有點嚇人，但在實際開發中，大部分的程式碼都透過
字面量、`List.filled()`、`List.generate()` 或是透過其他集合轉換來建立列表。
為了一些極端情況，比如你需要建立某個型別的一個空的列表，我們新增了 `List.empty()` 構造。

The pattern of creating a completely uninitialized list has always felt out of
place in Dart, and now it is even more so. If you have code broken by this,
you can always fix it by using one of the many other ways to produce a list.

在 Dart 中，建立一個完全未初始化的列表，總是感覺不太對勁，以前是，現在更是。
如果你的程式碼因為這項改動而被影響了，你隨時可以透過其他方法來產生一個列表。

### Cannot set a larger length on non-nullable lists

### 不能對非空的列表設定更大的長度

This is little known, but the `length` getter on `List` also has a corresponding
*setter*. You can set the length to a shorter value to truncate the list. And
you can also set it to a *longer* length to pad the list with uninitialized
elements.

`List` 的 `length` getter 也有一個對應的 setter，這一點鮮為人知。
你可以對列表設定一個較短的長度，從而截斷它。
你也可以對列表設定一個 **更長的** 長度，從而使用未初始化的元素填充它。

If you were to do that with a list of a non-nullable type, you'd violate
soundness when you later accessed those unwritten elements. To prevent that, the
`length` setter will throw a runtime exception if (and only if) the list has a
non-nullable element type *and* you set it to a *longer* length. It is still
fine to truncate lists of all types, and you can grow lists of nullable types.

如果你對一個非空的列表做了這樣的操作，在存取未初始化的元素時，就與空安全的健全性發生了衝突。
為了防止意外發生，現在對一個非空型別的陣列呼叫 `length` setter，
**並且** 準備設定一個 **更長的** 長度時，會在執行時丟擲一個例外。
你仍然可以對任何型別的列表進行截斷，也可以對一個可空型別的列表進行填充。

There is an important consequence of this if you define your own list types that
extend `ListBase` or apply `ListMixin`. Both of those types provide an
implementation of `insert()` that previously made room for the inserted element
by setting the length. That would fail with null safety, so instead we changed
the implementation of `insert()` in `ListMixin` (which `ListBase` shares) to
call `add()` instead. Your custom list class should provide a definition of
`add()` if you want to be able to use that inherited `insert()` method.

如果你自訂了列表的型別，例如繼承了 `ListBase` 或者混入了 `ListMixin`，
那麼這項改動可能會造成較大的影響。
以上的兩種型別都提供了 `insert()` 的實現，透過設定長度，為插入的元素提供空間。
在空安全中這樣做可能會出現錯誤，所以我們將它們的 `insert()` 實現改為了 `add()`。
現在你自訂的列表應該繼承 `add()` 方法。

### Cannot access Iterator.current before or after iteration

### 在迭代前後不能存取 Iterator.current

The `Iterator` class is the mutable "cursor" class used to traverse the elements
of a type that implements `Iterable`. You are expected to call `moveNext()`
before accessing any elements to advance to the first element. When that method
returns `false`, you have reached the end and there are no more elements.

`Iterable` 是一個可變的“遊標”類，用於遍歷 `Iterable` 型別的元素。
在存取任何元素之前，你都需要呼叫 `moveNext()` 來跳到第一個元素。
當方法返回了 `false` 時，意味著你到達了終點，已經沒有更多元素了。

It used to be that `current` returned `null` if you called it either before
calling `moveNext()` the first time or after iteration finished. With null
safety, that would require the return type of `current` to be `E?` and not `E`.
That in turn means every element access would require a runtime `null` check.

在以前，在首次呼叫 `moveNext()` 前，或者在迭代結束後，呼叫 `current` 會返回 `null`。
有了空安全，就要求 `current` 的返回型別是 `E?` 而不是 `E`。
這樣的返回型別意味著在執行時，所有元素的存取前都需要檢查是否為 `null`。

Those checks would be useless given that almost no one ever accesses the current
element in that erroneous way. Instead, we have made the type of `current` be
`E`. Since there *may* be a value of that type available before or after
iterating, we've left the iterator's behavior undefined if you call it when you
aren't supposed to. Most implementations of `Iterator` throw a `StateError`.

鑑於目前幾乎沒有人會以這種錯誤的方式訪問當前元素，這些檢查其實毫無用處。
所以我們將 `current` 的返回型別確定為 `E`。
由於迭代前後 **有可能** 會有一個對應型別的值出現，
當你在不應該呼叫它的時候呼叫迭代器時，我們讓迭代器的行為保持為未定義。
對於 `Iterator` 的大部分實現都將丟擲 `StateError` 例外。

## Summary

## 總結

That is a very detailed tour through all of the language and library changes
around null safety. It's a lot of stuff, but this is a pretty big language
change. More importantly, we wanted to get to a point where Dart still feels
cohesive and usable. That requires changing not just the type system, but a
number of other usability features around it. We didn't want it to feel like
null safety was bolted on.

這是一場非常詳盡的空安全旅途，途中走遍了所有語言和庫的變更。
這其中的內容真的很多，但是這也是一項非常大的語言變更。
更重要的是，我們希望 Dart 仍然讓你感到好用且具備一致性。
所以不僅型別系統需要作出變動，一些可用性的特性也同時圍繞著一起改變。
我們不希望空安全僅僅是一個簡陋的語法附加特性。

The core points to take away are:

你需要掌握的核心要點有：

*   Types are non-nullable by default and made nullable by adding `?`.

    型別預設是非空的，可以新增 `?` 變為可空的。

*   Optional parameters must be nullable or have a default value. You can use
    `required` to make named parameters non-optional. Non-nullable top-level
    variables and static fields must have initializers. Non-nullable instance
    fields must be initialized before the constructor body begins.

    可選引數必須是可空的或者包含預設值的。
    你可以使用 `required` 來建構一個非可選命名引數。
    非空的全域變數和靜態欄位必須在宣告時被初始化。
    例項的非空欄位必須在構造體開始執行前被初始化。

*   Method chains after null-aware operators short circuit if the receiver is
    `null`. There are new null-aware cascade (`?..`) and index (`?[]`)
    operators. The postfix null assertion "bang" operator (`!`) casts its
    nullable operand to the underlying non-nullable type.

    如果接收者為 `null`，那麼在其避空運算子之後的鏈式方法呼叫都會被截斷。
    我們引入了新的空判斷級聯運運算元 (`?..`) 及索引運運算元 (`?[]`)。
    字尾空斷言“重點”運運算元 (`!`) 可以將可空的操作物件轉換為對應的非空型別。

*   Flow analysis lets you safely turn nullable local variables and parameters
    into usable non-nullable ones. The new flow analysis also has smarter rules
    for type promotion, missing returns, unreachable code, and variable
    initialization.

    新的流程分析，讓你更安全地將可空的區域變數和引數，轉變為可用的非空型別。
    它同時還對型別提升、遺漏的返回、不可達的程式碼以及變數的初始化，有著更為智慧的規則。

*   The `late` modifier lets you use non-nullable types and `final` in places
    you otherwise might not be able to, at the expense of runtime checking. It
    also gives you lazy-initialized fields.

    `late` 修飾符以在執行時每次都進行檢查的高昂代價，
    讓你在一些原本無法使用的地方，能夠使用非空型別和 `final`。
    它同時提供了對欄位延遲初始化的支援。

*   The `List` class is changed to prevent uninitialized elements.

    `List` 類現在不再允許包含未初始化的元素。

Finally, once you absorb all of that and get your code into the world of null
safety, you get a sound program that the compilers can optimize and where every
place a runtime error can occur is visible in your code. We hope you feel that's
worth the effort to get there.

最後，當你掌握了這篇文章的所有內容，並且將你的程式碼真正帶到空安全的世界中時，
你會得到一個健全的、編譯器可以進行最佳化的程式，
並且在你的程式碼中，你可以看到每一個執行時可能出錯的地方。
希望你的一切努力都是值得的。
