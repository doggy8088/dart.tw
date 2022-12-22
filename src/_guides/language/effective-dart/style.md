---
title: "Effective Dart: Style"
title: 高效 Dart 語言指南：程式碼風格
description: Formatting and naming rules for consistent, readable code.
nextpage:
  url: /guides/language/effective-dart/documentation
  title: Documentation
  title: 文件
prevpage:
  url: /guides/language/effective-dart
  title: Overview
  title: 概覽
---
<?code-excerpt plaster="none"?>
<?code-excerpt path-base="misc/lib/effective_dart"?>

A surprisingly important part of good code is good style. Consistent naming,
ordering, and formatting helps code that *is* the same *look* the same. It takes
advantage of the powerful pattern-matching hardware most of us have in our
ocular systems.  If we use a consistent style across the entire Dart ecosystem,
it makes it easier for all of us to learn from and contribute to each others'
code.

好的程式碼有一個非常重要的特點就是擁有好的風格。
一致的命名、一致的順序、 以及一致的格式讓程式碼看起來是一樣的。
這非常有利於發揮我們視力系統強大的模式匹配能力。
如果我們整個 Dart 生態系統中都使用統一的風格，
那麼這將讓我們彼此之間更容易的互相學習和互相貢獻。
它使我們所有人都可以更容易地學習併為彼此的程式碼做出貢獻。

## Identifiers

## 識別符號

Identifiers come in three flavors in Dart.

在 Dart 中識別符號有三種類型。

*   `UpperCamelCase` names capitalize the first letter of each word, including
    the first.

    `UpperCamelCase` 每個單詞的首字母都大寫，包含第一個單詞。

*   `lowerCamelCase` names capitalize the first letter of each word, *except*
    the first which is always lowercase, even if it's an acronym.

    `lowerCamelCase` 除了第一個字母始終是小寫（即使是縮略詞），每個單詞的首字母都大寫。

*   `lowercase_with_underscores` names use only lowercase letters,
    even for acronyms,
    and separate words with `_`.

    `lowercase_with_underscores` 只是用小寫字母單詞，即使是縮略詞，
    並且單詞之間使用 `_` 連線。


### DO name types using `UpperCamelCase`.

### **要** 使用 `UpperCamelCase` 風格命名型別。

{% include linter-rule-mention.md rule="camel_case_types" %}

Classes, enum types, typedefs, and type parameters should capitalize the first
letter of each word (including the first word), and use no separators.

Classes（類別名稱）、 enums（列舉型別）、 typedefs（型別定義）、
以及 type parameters（型別引數）應該把每個單詞的首字母都大寫（包含第一個單詞）， 
不使用分隔符。

{:.good}
<?code-excerpt "style_good.dart (type-names)"?>
{% prettify dart tag=pre+code %}
class SliderMenu { ... }

class HttpRequest { ... }

typedef Predicate<T> = bool Function(T value);
{% endprettify %}

This even includes classes intended to be used in metadata annotations.

這裡包括使用元資料註解的類別。

{:.good}
<?code-excerpt "style_good.dart (annotation-type-names)"?>
{% prettify dart tag=pre+code %}
class Foo {
  const Foo([Object? arg]);
}

@Foo(anArg)
class A { ... }

@Foo()
class B { ... }
{% endprettify %}

If the annotation class's constructor takes no parameters, you might want to
create a separate `lowerCamelCase` constant for it.

如果註解類別的建構函式是無參函式，
則可以使用一個 `lowerCamelCase` 風格的常量來初始化這個註解。

{:.good}
<?code-excerpt "style_good.dart (annotation-const)"?>
{% prettify dart tag=pre+code %}
const foo = Foo();

@foo
class C { ... }
{% endprettify %}

[Linter rule]: /guides/language/analysis-options#the-analysis-options-file

### DO name extensions using `UpperCamelCase`.

### **要** 使用 `UpperCamelCase` 風格型別作為副檔名

{% include linter-rule-mention.md rule="camel_case_extensions" %}

Like types, [extensions][] should capitalize the first letter of each word
(including the first word),
and use no separators.

與型別命名一樣，[擴充][extensions] 的名稱也應大寫每個單詞的首字母
（包括第一個單詞），並且不使用分隔符。

{:.good}
<?code-excerpt "style_good.dart (extension-names)"?>
{% prettify dart tag=pre+code %}
extension MyFancyList<T> on List<T> { ... }

extension SmartIterable<T> on Iterable<T> { ... }
{% endprettify %}

[extensions]: /guides/language/extension-methods

### DO name libraries, packages, directories, and source files using `lowercase_with_underscores`. {#do-name-libraries-and-source-files-using-lowercase_with_underscores}

### **要** 在`庫`，`package`，`資料夾`，`原始檔` 中使用 `lowercase_with_underscores` 方式命名。 {#do-name-libraries-and-source-files-using-lowercase_with_underscores}

{% include linter-rule-mention.md rule1="library_names" rule2="file_names" %}
<!-- source for rules (update these if you update the guideline):
https://github.com/dart-lang/linter/blob/master/lib/src/rules/library_names.dart
https://github.com/dart-lang/linter/blob/master/lib/src/rules/file_names.dart -->

Some file systems are not case-sensitive, so many projects require filenames to
be all lowercase. Using a separating character allows names to still be readable
in that form. Using underscores as the separator ensures that the name is still
a valid Dart identifier, which may be helpful if the language later supports
symbolic imports.

一些檔案系統不區分大小寫，所以很多專案要求檔名必須是小寫字母。
使用分隔符這種形式可以保證命名的可讀性。使用下劃線作為分隔符可確保名稱仍然是有效的Dart識別符號，
如果語言後續支援符號匯入，這將會起到非常大的幫助。

{:.good}
<?code-excerpt "style_lib_good.dart" replace="/foo\///g"?>
{% prettify dart tag=pre+code %}
library peg_parser.source_scanner;

import 'file_system.dart';
import 'slider_menu.dart';
{% endprettify %}

{:.bad}
<?code-excerpt "style_lib_good.dart" replace="/foo\///g;/file./file-/g;/slider_menu/SliderMenu/g;/source_scanner/SourceScanner/g;/peg_parser/pegparser/g"?>
{% prettify dart tag=pre+code %}
library pegparser.SourceScanner;

import 'file-system.dart';
import 'SliderMenu.dart';
{% endprettify %}

{{site.alert.note}}

  This guideline specifies *how* to name a library *if you choose to name it*. 
  It is fine to _omit_ the library directive in a file if you want.

  如果你 **選擇命名庫**，本準則給定了 **如何** 為庫取名。
  如果需要，可以在檔案中 **省略** 庫指令。

{{site.alert.end}}

### DO name import prefixes using `lowercase_with_underscores`.

### **要** 用 `lowercase_with_underscores` 風格命名庫和原始檔名。

{% include linter-rule-mention.md rule="library_prefixes" %}

{:.good}
<?code-excerpt "style_lib_good.dart (import-as)" replace="/(package):examples\/effective_dart\/foo.dart[^']*/$1:angular_components\/angular_components.dart/g; /(package):examples\/effective_dart\/bar.dart[^']*/$1:js\/js.dart/g"?>
{% prettify dart tag=pre+code %}
import 'dart:math' as math;
import 'package:angular_components/angular_components.dart' as angular_components;
import 'package:js/js.dart' as js;
{% endprettify %}

{:.bad}
<?code-excerpt "style_lib_good.dart (import-as)" replace="/(package):examples\/effective_dart\/foo.dart[^']*/$1:angular_components\/angular_components.dart/g; /as angular_components/as angularComponents/g; /(package):examples\/effective_dart\/bar.dart[^']*/$1:js\/js.dart/g; / math/ Math/g;/as js/as JS/g"?>
{% prettify dart tag=pre+code %}
import 'dart:math' as Math;
import 'package:angular_components/angular_components.dart' as angularComponents;
import 'package:js/js.dart' as JS;
{% endprettify %}


### DO name other identifiers using `lowerCamelCase`.

### **要** 使用 `lowerCamelCase` 風格來命名其他的識別符號。

{% include linter-rule-mention.md rule="non_constant_identifier_names" %}

Class members, top-level definitions, variables, parameters, and named
parameters should capitalize the first letter of each word *except* the first
word, and use no separators.

類成員、最上層定義、變數、引數以及命名引數等
*除了*第一個單詞，每個單詞首字母都應大寫，並且不使用分隔符。

{:.good}
<?code-excerpt "style_good.dart (misc-names)"?>
{% prettify dart tag=pre+code %}
var count = 3;

HttpRequest httpRequest;

void align(bool clearItems) {
  // ...
}
{% endprettify %}


### PREFER using `lowerCamelCase` for constant names.

### **推薦** 使用 `lowerCamelCase` 來命名常量。

{% include linter-rule-mention.md rule="constant_identifier_names" %}

In new code, use `lowerCamelCase` for constant variables, including enum values.

在新的程式碼中，使用 `lowerCamelCase` 來命名常量，包括列舉的值。

{:.good}
<?code-excerpt "style_good.dart (const-names)"?>
{% prettify dart tag=pre+code %}
const pi = 3.14;
const defaultTimeout = 1000;
final urlScheme = RegExp('^([a-z]+):');

class Dice {
  static final numberGenerator = Random();
}
{% endprettify %}

{:.bad}
<?code-excerpt "style_bad.dart (const-names)"?>
{% prettify dart tag=pre+code %}
const PI = 3.14;
const DefaultTimeout = 1000;
final URL_SCHEME = RegExp('^([a-z]+):');

class Dice {
  static final NUMBER_GENERATOR = Random();
}
{% endprettify %}

You may use `SCREAMING_CAPS` for consistency with existing code,
as in the following cases:

您可以使用 `SCREAMING_CAPS` 與現有程式碼保持一致，比如：

* When adding code to a file or library that already uses `SCREAMING_CAPS`.

  將程式碼新增到已使用 `SCREAMING_CAPS` 的檔案或庫時。

* When generating Dart code that's parallel to Java code—for example, 
  in enumerated types generated from [protobufs.][]

  產生與 Java 程式碼並行的 Dart 程式碼時。例如，來自 [protobufs][] 的列舉型別。

{{site.alert.note}}
  We initially used Java's `SCREAMING_CAPS` style for constants. We
  changed for a few reasons:

  我們一開始使用 Java `SCREAMING_CAPS` 風格來命名常量。
  我們之所以不再使用，是因為：

  *   `SCREAMING_CAPS` looks bad for many cases, particularly enum values for
      things like CSS colors.

      `SCREAMING_CAPS` 很多情況下看起來比較糟糕，尤其類似於 CSS 顏色這類別的列舉值。

  *   Constants are often changed to final non-const variables, which would
      necessitate a name change.

      常量常常被修改為 final 型別的非常量變數，這種情況你還需要修改變數的名字為小寫字母形式。

  *   The `values` property automatically defined on an enum type is const and
      lowercase.

      在列舉型別中自動定義的 `values` 屬性為常量並且是小寫字母形式的。

{{site.alert.end}}

[protobufs.]: {{site.pub-pkg}}/protobuf
[protobufs]: {{site.pub-pkg}}/protobuf


### DO capitalize acronyms and abbreviations longer than two letters like words.

### 把超過兩個字母的首字母大寫縮略詞和縮寫詞當做一般單詞來對待。

Capitalized acronyms can be hard to read, and
multiple adjacent acronyms can lead to ambiguous names.
For example, given a name that starts with `HTTPSFTP`, there's no way
to tell if it's referring to HTTPS FTP or HTTP SFTP.

首字母大寫縮略詞比較難閱讀，
特別是多個縮略詞連載一起的時候會引起歧義。
例如，一個以 `HTTPSFTP` 開頭的名字，
沒有辦法判斷它是指 HTTPS FTP 還是 HTTP SFTP。

To avoid this, acronyms and abbreviations are capitalized like regular words.

為了避免上面的情況，縮略詞和縮寫詞要像普通單詞一樣首字母大寫。

**Exception:** Two-letter *acronyms* like IO (input/output) are fully
capitalized: `IO`. On the other hand, two-letter *abbreviations* like
ID (identification) are still capitalized like regular words: `Id`.

**例外情況** 兩個字母情況下，類似 IO (input/output) 這樣的 **縮略詞** 要全大寫。
另外，兩個字母的 **縮寫詞** 比如 ID (identification) 與其他常規單詞一樣，
首字母大寫即可: `Id`。

{:.good}
{% prettify dart tag=pre+code %}
class HttpConnection {}
class DBIOPort {}
class TVVcr {}
class MrRogers {}

var httpRequest = ...
var uiHandler = ...
var userId = ...
Id id;
{% endprettify %}

{:.bad}
{% prettify dart tag=pre+code %}
class HTTPConnection {}
class DbIoPort {}
class TvVcr {}
class MRRogers {}

var hTTPRequest = ...
var uIHandler = ...
var userID = ...
ID iD;
{% endprettify %}


### PREFER using `_`, `__`, etc. for unused callback parameters.

Sometimes the type signature of a callback function requires a parameter,
but the callback implementation doesn't _use_ the parameter.
In this case, it's idiomatic to name the unused parameter `_`.
If the function has multiple unused parameters, use additional
underscores to avoid name collisions: `__`, `___`, etc.

{:.good}
<?code-excerpt "style_good.dart (unused-callback-params)"?>
{% prettify dart tag=pre+code %}
futureOfVoid.then((_) {
  print('Operation complete.');
});
{% endprettify %}

This guideline is only for functions that are both *anonymous and local*.
These functions are usually used immediately in a context where it's
clear what the unused parameter represents.
In contrast, top-level functions and method declarations don't have that context,
so their parameters must be named so that it's clear what each parameter is for,
even if it isn't used.


### DON'T use a leading underscore for identifiers that aren't private.

Dart uses a leading underscore in an identifier to mark members and top-level
declarations as private. This trains users to associate a leading underscore
with one of those kinds of declarations. They see "_" and think "private".

There is no concept of "private" for local variables, parameters, local
functions, or library prefixes. When one of those has a name that starts with an
underscore, it sends a confusing signal to the reader. To avoid that, don't use
leading underscores in those names.


### DON'T use prefix letters.

### **不要**使用字首字母

[Hungarian notation](https://en.wikipedia.org/wiki/Hungarian_notation) and
other schemes arose in the time of BCPL, when the compiler didn't do much to
help you understand your code. Because Dart can tell you the type, scope,
mutability, and other properties of your declarations, there's no reason to
encode those properties in identifier names.

在編譯器無法幫助你瞭解自己程式碼的時，
[匈牙利命名法](https://en.wikipedia.org/wiki/Hungarian_notation)
和其他方案出現在了 BCPL ，
但是因為 Dart 可以提示你宣告的型別，範圍，可變性和其他屬性，
所以沒有理由在識別符號名稱中對這些屬性進行編碼。

{:.good}
{% prettify dart tag=pre+code %}
defaultTimeout
{% endprettify %}

{:.bad}
{% prettify dart tag=pre+code %}
kDefaultTimeout
{% endprettify %}


## Ordering

## 順序

To keep the preamble of your file tidy, we have a prescribed order that
directives should appear in. Each "section" should be separated by a blank line.

為了使檔案前面部分保持整潔，我們規定了關鍵字出現順序的規則。
每個“部分”應該使用空行分割。

A single linter rule handles all the ordering guidelines:
[directives_ordering.](/tools/linter-rules#directives_ordering)


### DO place "dart:" imports before other imports.

### **要** 把 "dart:" 匯入陳述式放到其他匯入陳述式之前。

{% include linter-rule-mention.md rule="directives_ordering" %}

{:.good}
<?code-excerpt "style_lib_good.dart (dart-import-first)" replace="/\w+\/effective_dart\///g"?>
{% prettify dart tag=pre+code %}
import 'dart:async';
import 'dart:html';

import 'package:bar/bar.dart';
import 'package:foo/foo.dart';
{% endprettify %}


### DO place "package:" imports before relative imports.

### **要** 把 "package:" 匯入陳述式放到專案相關匯入陳述式之前。

{% include linter-rule-mention.md rule="directives_ordering" %}

{:.good}
<?code-excerpt "style_lib_good.dart (pkg-import-before-local)" replace="/\w+\/effective_dart\///g;/'foo/'util/g"?>
{% prettify dart tag=pre+code %}
import 'package:bar/bar.dart';
import 'package:foo/foo.dart';

import 'util.dart';
{% endprettify %}


### DO specify exports in a separate section after all imports.

### **要** 把匯出 (export) 陳述式作為一個單獨的部分放到所有匯入陳述式之後。

{% include linter-rule-mention.md rule="directives_ordering" %}

{:.good}
<?code-excerpt "style_lib_good.dart (export)"?>
{% prettify dart tag=pre+code %}
import 'src/error.dart';
import 'src/foo_bar.dart';

export 'src/error.dart';
{% endprettify %}

{:.bad}
<?code-excerpt "style_lib_bad.dart (export)"?>
{% prettify dart tag=pre+code %}
import 'src/error.dart';
export 'src/error.dart';
import 'src/foo_bar.dart';
{% endprettify %}


### DO sort sections alphabetically.

### **要** 按照字母順序來排序每個部分中的陳述式。

{% include linter-rule-mention.md rule="directives_ordering" %}

{:.good}
<?code-excerpt "style_lib_good.dart (sorted)" replace="/\w+\/effective_dart\///g"?>
{% prettify dart tag=pre+code %}
import 'package:bar/bar.dart';
import 'package:foo/foo.dart';

import 'foo.dart';
import 'foo/foo.dart';
{% endprettify %}

{:.bad}
<?code-excerpt "style_lib_bad.dart (sorted)" replace="/\w+\/effective_dart\///g"?>
{% prettify dart tag=pre+code %}
import 'package:foo/foo.dart';
import 'package:bar/bar.dart';

import 'foo/foo.dart';
import 'foo.dart';
{% endprettify %}


## Formatting

## 格式化

Like many languages, Dart ignores whitespace. However, *humans* don't. Having a
consistent whitespace style helps ensure that human readers see code the same
way the compiler does.

和其他大部分語言一樣， Dart 忽略空格。但是，*人*不會。
具有一致的空格風格有助於幫助我們能夠用編譯器相同的方式理解程式碼。

### DO format your code using `dart format`.

### **要** 使用 `dart format` 格式化你的程式碼。

Formatting is tedious work and is particularly time-consuming during
refactoring. Fortunately, you don't have to worry about it. We provide a
sophisticated automated code formatter called [`dart format`][] that does it for
you. We have [some documentation][dart format docs] on the rules it applies, but the
official whitespace-handling rules for Dart are *whatever `dart format` produces*.

格式化是一項繁瑣的工作，尤其在重構過程中特別耗時。
慶幸的是，你不必擔心。
我們提供了一個名為 [`dart format`][] 的優秀的自動程式碼格式化程式，它可以為你完成格式化工作。
我們有一些關於它適用的規則的 [文件][dart format docs] ，
Dart 中任何官方的空格處理規則由 *`dart format` 產生*。

The remaining formatting guidelines are for the few things `dart format` cannot fix
for you.

其餘格式指南用於 `dart format` 無法修復的一些規則。

[`dart format`]: /tools/dart-format
[dart format docs]: https://github.com/dart-lang/dart_style/wiki/Formatting-Rules

### CONSIDER changing your code to make it more formatter-friendly.

### **考慮** 修改你的程式碼讓格式更友好。

The formatter does the best it can with whatever code you throw at it, but it
can't work miracles. If your code has particularly long identifiers, deeply
nested expressions, a mixture of different kinds of operators, etc. the
formatted output may still be hard to read.

無論你扔給格式化程式什麼樣程式碼，它都會盡力去處理，
但是格式化程式不會創造奇蹟。
如果程式碼裡有特別長的識別符號，深層巢狀(Nesting)的表示式，混合的不同型別運算子等。
格式化輸出的程式碼可能任然很難閱讀。

When that happens, reorganize or simplify your code. Consider shortening a local
variable name or hoisting out an expression into a new local variable. In other
words, make the same kinds of modifications that you'd make if you were
formatting the code by hand and trying to make it more readable. Think of
`dart format` as a partnership where you work together, sometimes iteratively, 
to produce beautiful code.

當有這樣的情況發生時，那麼就需要重新組織或簡化你的程式碼。
考慮縮短區域變數名或者將表示式抽取為一個新的區域變數。
換句話說，你應該做一些手動格式化並增加程式碼的可讀性的修改。
在工作中應該把 `dart format` 看做一個合作伙伴，
在程式碼的編寫和迭代過程中互相協作輸出優質的程式碼。


### AVOID lines longer than 80 characters.

### **避免** 單行超過 80 個字元。

{% include linter-rule-mention.md rule="lines_longer_than_80_chars" %}

Readability studies show that long lines of text are harder to read because your
eye has to travel farther when moving to the beginning of the next line. This is
why newspapers and magazines use multiple columns of text.

可讀性研究表明，長行的文字不易閱讀，
長行文字移動到下一行的開頭時，眼睛需要移動更長的距離。
這也是為什麼報紙和雜誌會使用多列樣式的文字排版。

If you really find yourself wanting lines longer than 80 characters, our
experience is that your code is likely too verbose and could be a little more
compact. The main offender is usually `VeryLongCamelCaseClassNames`. Ask
yourself, "Does each word in that type name tell me something critical or
prevent a name collision?" If not, consider omitting it.

如果你真的發現你需要的文字長度超過了 80 個字元，
根據我們的經驗，你的程式碼很可能過於冗長，
而且有方式可以讓它更緊湊。
最常見的的一種情況就是使用 `VeryLongCamelCaseClassNames` （非常長的類別名稱字和變數名字）。
當遇到這種情況時，請自問一下：“那個型別名稱中的每個單詞都會告訴我一些關鍵的內容或阻止名稱衝突嗎？”，
如果不是，考慮刪除它。

Note that `dart format` does 99% of this for you, but the last 1% is you. 
It does not split long string literals to fit in 80 columns, 
so you have to do that manually.

注意，`dart format` 能自動處理 99% 的情況，但是剩下的 1% 需要你自己處理。
`dart format` 不會把很長的字串字面量分割為 80 個字元的列，
所以這種情況你**需要**自己手工確保每行不超過 80 個字元。

**Exception:** When a URI or file path occurs in a comment or string (usually in
an import or export), it may remain whole even if it causes the line to go over
80 characters. This makes it easier to search source files for a path.

**例外：** 當 URI 及檔案路徑出現在註釋或字串中時（通常在匯入和匯出陳述式中），
即使文字超出行限制，也可能會保留在一行中。
這樣可以更輕鬆地搜尋給定路徑的原始檔。

**Exception:** Multi-line strings can contain lines longer than 80 characters
because newlines are significant inside the string and splitting the lines into
shorter ones can alter the program.

### DO use curly braces for all flow control statements. {#do-use-curly-braces-for-all-flow-control-structures}

### **要** 對所有流控制結構使用花括號。

{% include linter-rule-mention.md rule="curly_braces_in_flow_control_structures" %}

Doing so avoids the [dangling else][] problem.

這樣可以避免 [dangling else][]（else懸掛）的問題。

[dangling else]: https://en.wikipedia.org/wiki/Dangling_else

{:.good}
<?code-excerpt "style_good.dart (curly-braces)"?>
{% prettify dart tag=pre+code %}
if (isWeekDay) {
  print('Bike to work!');
} else {
  print('Go dancing or read a book!');
}
{% endprettify %}

**Exception:** When you have an `if` statement with no `else` clause and the
whole `if` statement fits on one line, you can omit the braces if you prefer:

這裡有一個例外：一個沒有 `else` 的 `if` 陳述式，
並且這個 `if` 陳述式以及它的執行體適合在一行中實現。
在這種情況下，如果您願意，可以不用括號：

{:.good}
<?code-excerpt "style_good.dart (one-line-if)"?>
{% prettify dart tag=pre+code %}
if (arg == null) return defaultValue;
{% endprettify %}

If the body wraps to the next line, though, use braces:

但是，如果執行體包含下一行，請使用大括號：

{:.good}
<?code-excerpt "style_good.dart (one-line-if-wrap)"?>
{% prettify dart tag=pre+code %}
if (overflowChars != other.overflowChars) {
  return overflowChars < other.overflowChars;
}
{% endprettify %}

{:.bad}
<?code-excerpt "style_bad.dart (one-line-if-wrap)"?>
{% prettify dart tag=pre+code %}
if (overflowChars != other.overflowChars)
  return overflowChars < other.overflowChars;
{% endprettify %}
