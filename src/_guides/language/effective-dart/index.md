---
title: Effective Dart
title: 高效 Dart 語言指南
description: Best practices for building consistent, maintainable, efficient Dart libraries.
description: 編寫具有高效、一致性、可維護的 Dart 程式碼。
declaration:
permalink: /guides/language/effective-dart
nextpage:
  url: /guides/language/effective-dart/style
  title: Style
  title: 程式碼風格
---

Over the past several years, we've written a ton of Dart code and learned a lot
about what works well and what doesn't. We're sharing this with you so you can
write consistent, robust, fast code too. There are two overarching themes:

在過去的幾年裡，我們編寫了大量的 Dart 程式碼，並從中收穫了很多經驗和教訓。我們將與你分享這些經驗，這些經驗將有助於你編寫出一致、健壯、高效的程式碼。這裡包含兩主題：

 1. **Be consistent.** When it comes to things like formatting, and casing,
    arguments about which is better are subjective and impossible to resolve.
    What we do know is that being *consistent* is objectively helpful.

    **保持一致** 當談論到格式、命名以及引數的相關規則時，哪種規則更好，得出的結論通常是主觀且無法達成一致的。但我們知道的是，客觀上保持 **一致** 是非常有益的。

    If two pieces of code look different it should be because they *are*
    different in some meaningful way. When a bit of code stands out and catches
    your eye, it should do so for a useful reason.

    如果兩段程式碼看起來不同，那它們就應該有不同的含義。當一段突出的程式碼吸引到你的注意時，那它就應該有吸引你的理由。

 2. **Be brief.** Dart was designed to be familiar, so it inherits many of the
    same statements and expressions as C, Java, JavaScript and other languages.
    But we created Dart because there is a lot of room to improve on what those
    languages offer. We added a bunch of features, from string interpolation to
    initializing formals, to help you express your intent more simply and
    easily.

    **保持簡潔** Dart 會讓開發者感到很親切，因為它繼承了許多與 C、Java、JavaScript 及其他語言相同的陳述式和表示式語法。我們之所以開發 Dart 語言，是因為這些語言依然有很大的改進的空間。我們提供了很多新的特性，比如字串插值、初始化正規化等，以幫助你更簡單、更輕鬆地表達意圖。

    If there are multiple ways to say something, you should generally pick the
    most concise one. This is not to say you should [code golf][] yourself into
    cramming a whole program into a single line. The goal is code that is
    *economical*, not *dense*.

    如果有多種方式來描述一件事情，那麼你通常應該選擇其中最簡潔的方式。這並不意味著你要像 [code golf][]（程式碼高爾夫挑戰賽）一樣將所有程式碼塞到一行中。而是應該讓程式碼變得 **簡約** 而非 **密集**。

[code golf]: https://en.wikipedia.org/wiki/Code_golf

## The guides

## 指南

We split the guidelines into a few separate pages for easy digestion:

為了便於理解，這裡我們將指南分成了幾個部分：

  * **[Style Guide][]** &ndash; This defines the rules for laying out and
    organizing code, or at least the parts that [dart format] doesn't handle for
    you. The style guide also specifies how identifiers are formatted:
    `camelCase`, `using_underscores`, etc.

    **[風格指南][Style Guide]** &ndash; 該部分定義了佈局和組織程式碼的規則，或者說是 [dart format][] 不能為你格式化的那些程式碼的佈局和組織規則。風格指南還為你指定了識別符號的格式，比如：駝峰式大小寫、下劃線的使用等等。

  * **[Documentation Guide][]** &ndash; This tells you everything you need to
    know about what goes inside comments. Both doc comments and regular,
    run-of-the-mill code comments.

    **[註釋指南][Documentation Guide]** &ndash; 該部分會告訴你註釋中應該包含哪些內容。包括文件註釋以及常規的普通程式碼註釋。

  * **[Usage Guide][]** &ndash; This teaches you how to make the best use of
    language features to implement behavior. If it's in a statement or
    expression, it's covered here.

    **[使用指南][Usage Guide]** &ndash; 該部分將教你如何充分利用語言特性來實現相關功能。比如你可以在該部分找到如何利用陳述式或表示式來實現某個功能。

  * **[Design Guide][]** &ndash; This is the softest guide, but the one
    with the widest scope. It covers what we've learned about designing
    consistent, usable APIs for libraries. If it's in a type signature or
    declaration, this goes over it.

    **[設計指南][Design Guide]** &ndash; 該部分是最易理解但是覆蓋範圍最廣的。其涵蓋了我們所總結的為庫設計一致、可用 API 的經驗。比如這些 API 的型別簽名或宣告的說明則會在這裡找到。

For links to all the guidelines, see the
[summary](#summary-of-all-rules).

有關所有指南的連結，
請查閱 [概覽](#summary-of-all-rules)。

[dart format]: /tools/dart-format
[style guide]: /guides/language/effective-dart/style
[documentation guide]: /guides/language/effective-dart/documentation
[usage guide]: /guides/language/effective-dart/usage
[design guide]: /guides/language/effective-dart/design

## How to read the guides

## 如何閱讀這些指南

Each guide is broken into a few sections. Sections contain a list of guidelines.
Each guideline starts with one of these words:

每個指南都分為了幾個部分。每個部分包含一些詳細的準則。每條準則都以下面其中的一個詞作為開頭：

* **DO** guidelines describe practices that should always be followed. There
  will almost never be a valid reason to stray from them.

  **要**  準則所描述的內容應該始終被遵守。不應該以任何的理由來偏離違背這些準則。

* **DON'T** guidelines are the converse: things that are almost never a good
  idea. Hopefully, we don't have as many of these as other languages do because
  we have less historical baggage.

  **不要**  準則所描述的內容是相反的：描述的準則不是一個好主意。
  幸運的是，我們不會像其他語言有那麼多這樣的準則，因為我們沒有太多的歷史包袱。

* **PREFER** guidelines are practices that you *should* follow. However, there
  may be circumstances where it makes sense to do otherwise. Just make sure you
  understand the full implications of ignoring the guideline when you do.

  **推薦** 準則所描述的內容 **應該** 被遵守。但是在有些情況下，
  可能有更好的或者更合理的做法。請確保在你完全理解準則的情況下，再忽視這些準則。

* **AVOID** guidelines are the dual to "prefer": stuff you shouldn't do but
  where there may be good reasons to on rare occasions.

  **避免** 該單詞描述的準則與 “推薦” 描述的準則相反：
  顯然，這些事不應該做，但不排除在極少數場合下有充分的理由可以做。

* **CONSIDER** guidelines are practices that you might or might not want to
  follow, depending on circumstances, precedents, and your own preference.

  **考慮**  準則所描述的內容可以遵守也可以不遵守。
  取決於具體的情況、習慣做法以及自己的偏好。

Some guidelines describe an **exception** where the rule does *not* apply. When
listed, the exceptions may not be exhaustive—you might still need to use
your judgement on other cases.

某些準則描述了規則 **不** 適用的 **例外情況**。
當這些例外列出時，也有可能不是詳盡的&mdash;你可能還需要對其它的情況作出判斷。

This sounds like the police are going to beat down your door if you don't have
your laces tied correctly. Things aren't that bad. Most of the guidelines here
are common sense and we're all reasonable people. The goal, as always, is nice,
readable and maintainable code.

這聽起來好像有點小題大做。其實並沒有那麼糟糕。
大部分的準則都是常識，也符合我們的認知。
最終要達到的目標是寫出優雅，可讀，可維護的程式碼。

The Dart analyzer provides a linter
to help you write good, consistent code
that follows these and other guidelines.
If one or more [linter rules][lints] exist
that can help you follow a guideline
then the guideline links to those rules.
The links use the following format:

Dart 分析器提供了一個 Linter 工具，
可以幫助你編寫優秀的、一致性的以及符合各種指南的程式碼。
如果存在一個或者多個 [linter 規則][lints]
來幫助你遵循某個指南準則，那麼這些指南的連結也會有顯示。
比如下面的範例：

{% include linter-rule-mention.md rule="unnecessary_getters_setters" %}

To learn how to use the linter,
see [Enabling linter rules][]
and the list of [linter rules][lints].

學習如何使用 linter，請查閱文件
[啟用 linter 規則][Enabling linter rules]，
以及可用的各種 [linter 規則][lints]。

[Enabling linter rules]: /guides/language/analysis-options#enabling-linter-rules
[lints]: /tools/linter-rules

## Glossary

## 術語表

To keep the guidelines brief, we use a few shorthand terms to refer to different
Dart constructs.

為了使指南保持簡潔，我們使用一些簡寫術語來指代不同的 Dart 結構。

* A **library member** is a top-level field, getter, setter, or function.
  Basically, anything at the top level that isn't a type.

  **庫成員** 表示一個最上層欄位、Getter 或 Setter 方法、或函式。基本而言，任何最上層的東西都不會是一種型別。

* A **class member** is a constructor, field, getter, setter, function, or
  operator declared inside a class. Class members can be instance or static,
  abstract or concrete.

  **類成員** 表示類內部宣告的建構函式、欄位、Getter 或 Setter 方法、函式或運運算元。類成員可以是例項或靜態的，也可以是抽象或具體的。

* A **member** is either a library member or a class member.

  **成員** 可以表示是一個函式庫成員或者類成員。

* A **variable**, when used generally, refers to top-level variables,
  parameters, and local variables. It doesn't include static or instance fields.

  **變數** 通常指的是最上層變數、引數和區域變數。它不包括靜態或例項欄位。

* A **type** is any named type declaration: a class, typedef, or enum.

  **型別** 表示所有命名型別的宣告：一個類別、typedef 或列舉。

* A **property** is a top-level variable, getter (inside a class or at the top
  level, instance or static), setter (same), or field (instance or static).
  Roughly any "field-like" named construct.

  **屬性** 表示最上層變數、Getter 和 Setter 方法（位於類中或最上層，可以是例項或靜態的）或欄位（例項或靜態的）。基本上是任何類似欄位的命名結構都可以稱為屬性。

## Summary of all rules

## 所有準則摘要

{% include_relative toc.md %}
