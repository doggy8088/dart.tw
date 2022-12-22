---
title: A tour of the core libraries
title: Dart 語言核心函式庫一覽
description: Learn about the major features in Dart's libraries.
description: 學習更多關於 Dart 語言核心函式庫的特性。
short-title: Library tour
---
<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /(^|\n) *\/\/\s+ignore:[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore:[^\n]+\n/$1\n/g"?>
<?code-excerpt plaster="none"?>

This page shows you how to use the major features in Dart’s core libraries.
It’s just an overview, and by no means comprehensive.
Whenever you need more details about a class,
consult the [Dart API reference.][Dart API]

本頁介紹如何使用 Dart 核心函式庫中的主要功能。這只是一個概覽，並不全面。
當你需要有關類別的更多詳細資訊時，請參閱[Dart API 參考][Dart API]。

[dart:core](#dartcore---numbers-collections-strings-and-more)
<br> Built-in types, collections, and other core functionality.
  This library is automatically imported into every Dart program.

[dart:core](#dartcore---numbers-collections-strings-and-more)
<br> 內建型別，集合和其他核心功能。
  該庫會被自動匯入到所有的 Dart 程式。

[dart:async](#dartasync---asynchronous-programming)
<br> Support for asynchronous programming, with classes such as Future and Stream.

[dart:async](#dartasync---asynchronous-programming)
<br> 支援非同步程式設計，包括Future和Stream等類別。

[dart:math](#dartmath---math-and-random)
<br> Mathematical constants and functions, plus a random number generator.

[dart:math](#dartmath---math-and-random)
<br> 數學常數和函式，以及隨機數產生器。

[dart:convert](#dartconvert---decoding-and-encoding-json-utf-8-and-more)
<br> Encoders and decoders for converting between different data representations, including JSON and UTF-8.

[dart:convert](#dartconvert---decoding-and-encoding-json-utf-8-and-more)
<br> 用於在不同資料表示之間進行轉換的編碼器和解碼器，包括 JSON 和 UTF-8。

[dart:html](#darthtml)
<br> DOM and other APIs for browser-based apps.

[dart:html](#darthtml)
<br> 用於基於瀏覽器應用的 DOM 和其他 API。

[dart:io](#dartio)
<br> I/O for programs that can use the Dart VM,
  including Flutter apps, servers, and command-line scripts.

[dart:io](#dartio)
<br> 伺服器和命令列應用程式的 I/O 操作，
  包括 Flutter 應用，伺服器端應用，以及命令列指令碼。

This page is just an overview;
it covers only a few dart:* libraries
and no third-party libraries.

本章只是一個概述；
只涵蓋了幾個 dart:* 庫，
不包括第三方庫。

Other places to find library information are the
[pub.dev site]({{site.pub}}) and the
[Dart web developer library guide][webdev libraries].
You can find API documentation for all dart:* libraries in the
[Dart API reference][Dart API] or, if you're using Flutter,
the [Flutter API reference.][docs.flutter]

更多庫資訊可以在
[Pub site]({{site.pub}}) 和
[Dart web developer library guide.][webdev libraries] 查詢。
所有 dart:* 庫的 API 文件可以在
[Dart API reference][Dart API] 查詢， 如果使用的是 Flutter
可以在 [Flutter API reference.][docs.flutter] 查詢。

{{site.alert.info}}

  **DartPad tip:** You can play with the code in this page by copying it into a
  [DartPad.]({{site.dartpad}})

  **DartPad 提示：**
  可以透過將該頁中的程式碼複製到 [DartPad]({{site.dartpad}}) 中進行示範。

{{site.alert.end}}


## dart:core - numbers, collections, strings, and more

## dart:core - 數字，集合，字串等

The dart:core library ([API reference][dart:core])
provides a small but critical set of built-in functionality.
This library is automatically imported into every Dart program.

dart:core 庫 ([API reference][dart:core])
提供了一個少量但是重要的內建功能集合。
該庫會被自動匯入每個 Dart 程式。


### Printing to the console

### 控制檯列印

The top-level `print()` method takes a single argument (any Object)
and displays that object's string value (as returned by `toString()`)
in the console.

最上層 `print()` 方法接受一個引數 任意物件）
並輸出顯示這個物件的字串值(由 `toString()` 返回)
到控制檯。

<?code-excerpt "misc/test/library_tour/core_test.dart (print)"?>
```dart
print(anObject);
print('I drink $tea.');
```

For more information on basic strings and `toString()`, see
[Strings](/guides/language/language-tour#strings) in the language tour.

有關基本字串和 `toString()` 的更多資訊，參考
[Strings](/guides/language/language-tour#strings) in the language tour.


### Numbers

### 數字

The dart:core library defines the num, int, and double classes, which
have some basic utilities for working with numbers.

dart:core 函式庫定義了 num ，int 以及 double 類，
這些類別擁有一定的工具方法來處理數字。

You can convert a string into an integer or double with the `parse()`
methods of int and double, respectively:

使用 int 和 double 的 `parse()` 方法將字串轉換為整型或雙浮點型物件：

<?code-excerpt "misc/test/library_tour/core_test.dart (int|double.parse)"?>
```dart
assert(int.parse('42') == 42);
assert(int.parse('0x42') == 66);
assert(double.parse('0.50') == 0.5);
```

Or use the parse() method of num, which creates an integer if possible
and otherwise a double:

或者使用 num 的 parse() 方法，該方法可能會建立一個整數型別，否則為浮點型物件：

<?code-excerpt "misc/test/library_tour/core_test.dart (num-parse)"?>
```dart
assert(num.parse('42') is int);
assert(num.parse('0x42') is int);
assert(num.parse('0.50') is double);
```

To specify the base of an integer, add a `radix` parameter:

透過新增 `radix` 引數，指定整數的進位制基數：

<?code-excerpt "misc/test/library_tour/core_test.dart (radix)"?>
```dart
assert(int.parse('42', radix: 16) == 66);
```

Use the `toString()` method to convert an
int or double to a string. To specify the number of digits to the right
of the decimal, use [toStringAsFixed().][toStringAsFixed()] To specify the
number of significant digits in the string, use
[toStringAsPrecision():][toStringAsPrecision()]

使用 `toString()` 方法將整型或雙精度浮點型別轉換為字串型別。
使用 [toStringAsFixed().][toStringAsFixed()] 指定小數點右邊的位數，
使用 [toStringAsPrecision():][toStringAsPrecision()] 指定字串中的有效數字的位數。

<?code-excerpt "misc/test/library_tour/core_test.dart (toString())"?>
```dart
// Convert an int to a string.
assert(42.toString() == '42');

// Convert a double to a string.
assert(123.456.toString() == '123.456');

// Specify the number of digits after the decimal.
assert(123.456.toStringAsFixed(2) == '123.46');

// Specify the number of significant figures.
assert(123.456.toStringAsPrecision(2) == '1.2e+2');
assert(double.parse('1.2e+2') == 120.0);
```

For more information, see the API documentation for
[int,][int] [double,][double] and [num.][num] Also see
the [dart:math section](#dartmath---math-and-random).


### Strings and regular expressions

### 字元和正則表示式

A string in Dart is an immutable sequence of UTF-16 code units.
The language tour has more information about
[strings](/guides/language/language-tour#strings).
You can use regular expressions (RegExp objects)
to search within strings and to
replace parts of strings.

在 Dart 中一個字串是一個固定不變的 UTF-16 編碼單元序列。
語言概覽中有更多關於 [strings](/guides/language/language-tour#strings) 的內容。
使用正則表示式 (RegExp 物件) 可以在字串內搜尋和替換部分字串。


The String class defines such methods as `split()`, `contains()`,
`startsWith()`, `endsWith()`, and more.

String 定義了例如 `split()`， `contains()`，
`startsWith()`， `endsWith()` 等方法。

#### Searching inside a string

#### 在字串中搜索

You can find particular locations within a string, as well as check
whether a string begins with or ends with a particular pattern. For
example:

可以在字串內查詢特定字串的位置，
以及檢查字串是否以特定字串作為開頭或結尾。例如：

<?code-excerpt "misc/test/library_tour/core_test.dart (contains-etc)"?>
```dart
// Check whether a string contains another string.
assert('Never odd or even'.contains('odd'));

// Does a string start with another string?
assert('Never odd or even'.startsWith('Never'));

// Does a string end with another string?
assert('Never odd or even'.endsWith('even'));

// Find the location of a string inside a string.
assert('Never odd or even'.indexOf('odd') == 6);
```

#### Extracting data from a string

#### 從字串中提取資料

You can get the individual characters from a string as Strings or ints,
respectively. To be precise, you actually get individual UTF-16 code
units; high-numbered characters such as the treble clef symbol
('\\u{1D11E}') are two code units apiece.

可以獲取字串中的單個字元，將其作為字串或者整數。
確切地說，實際上獲取的是單獨的UTF-16編碼單元;
諸如高音譜號符號 ('\\u{1D11E}') 之類別的高編號字元分別為兩個編碼單元。

You can also extract a substring or split a string into a list of
substrings:

你也可以獲取字串中的子字串或者將一個字串分割為子字串列表：

<?code-excerpt "misc/test/library_tour/core_test.dart (substring-etc)"?>
```dart
// Grab a substring.
assert('Never odd or even'.substring(6, 9) == 'odd');

// Split a string using a string pattern.
var parts = 'progressive web apps'.split(' ');
assert(parts.length == 3);
assert(parts[0] == 'progressive');

// Get a UTF-16 code unit (as a string) by index.
assert('Never odd or even'[0] == 'N');

// Use split() with an empty string parameter to get
// a list of all characters (as Strings); good for
// iterating.
for (final char in 'hello'.split('')) {
  print(char);
}

// Get all the UTF-16 code units in the string.
var codeUnitList = 'Never odd or even'.codeUnits.toList();
assert(codeUnitList[0] == 78);
```

{{site.alert.note}}

  In many cases, you want to work with
  Unicode grapheme clusters
  as opposed to pure code units.
  These are characters as they are perceived
  by the user (for example, "🇬🇧" is one
  user-perceived character but several
  UTF-16 code units).
  For this, the Dart team provides the
  [`characters` package.]({{site.pub-pkg}}/characters)

在一些情況下，你可能會希望使用「顯性」 Unicode 字元，而不是用純程式碼表示。
這種字元是使用者可感知的，比如 "🇨🇳"，就是一個使用者可感知的字元 (emoji)，而它其實也是一個 UTF-16 的字元 (`U+1F1E8 U+1F1F3`)。
為此，Dart 團隊提供了 [`characters` 這個 package.]({{site.pub-pkg}}/characters) 供開發者使用。

{{site.alert.end}}

#### Converting to uppercase or lowercase

#### 首字母大小寫轉換

You can easily convert strings to their uppercase and lowercase
variants:

可以輕鬆的對字串的首字母大小寫進行轉換：

<?code-excerpt "misc/test/library_tour/core_test.dart (toUpperCase-toLowerCase)"?>
```dart
// Convert to uppercase.
assert('web apps'.toUpperCase() == 'WEB APPS');

// Convert to lowercase.
assert('WEB APPS'.toLowerCase() == 'web apps');
```

{{site.alert.note}}

  These methods don't work for every language. For example, the Turkish
  alphabet's dotless *I* is converted incorrectly.

  這些方法不是在所有語言上都有效的。例如，土耳其字母表的無點 *I* 轉換是不正確的。

{{site.alert.end}}


#### Trimming and empty strings

#### Trimming 和空字串

Remove all leading and trailing white space with `trim()`. To check
whether a string is empty (length is zero), use `isEmpty`.

使用 `trim()` 移除首尾空格。
使用 `isEmpty` 檢查一個字串是否為空（長度為 0）。

<?code-excerpt "misc/test/library_tour/core_test.dart (trim-etc)"?>
```dart
// Trim a string.
assert('  hello  '.trim() == 'hello');

// Check whether a string is empty.
assert(''.isEmpty);

// Strings with only white space are not empty.
assert('  '.isNotEmpty);
```

#### Replacing part of a string

#### 替換部分字串

Strings are immutable objects, which means you can create them but you
can’t change them. If you look closely at the [String API reference,][String]
you’ll notice that
none of the methods actually changes the state of a String. For example,
the method `replaceAll()` returns a new String without changing the
original String:

字串是不可變的物件，也就是說字串可以建立但是不能被修改。
如果仔細閱讀了 [String API docs,][String]
你會注意到，沒有一個方法實際的改變了字串的狀態。
例如，方法 `replaceAll()` 返回一個新字串，
並沒有改變原始字串：

<?code-excerpt "misc/test/library_tour/core_test.dart (replace)"?>
```dart
var greetingTemplate = 'Hello, NAME!';
var greeting = greetingTemplate.replaceAll(RegExp('NAME'), 'Bob');

// greetingTemplate didn't change.
assert(greeting != greetingTemplate);
```

#### Building a string

#### 建構一個字串

To programmatically generate a string, you can use StringBuffer. A
StringBuffer doesn’t generate a new String object until `toString()` is
called. The `writeAll()` method has an optional second parameter that
lets you specify a separator—in this case, a space.

要以程式碼方式產生字串，可以使用 StringBuffer 。
在呼叫 `toString()` 之前， StringBuffer 不會產生新字串物件。
`writeAll()` 的第二個引數為可選引數，用來指定分隔符，
本例中使用空格作為分隔符。

<?code-excerpt "misc/test/library_tour/core_test.dart (StringBuffer)"?>
```dart
var sb = StringBuffer();
sb
  ..write('Use a StringBuffer for ')
  ..writeAll(['efficient', 'string', 'creation'], ' ')
  ..write('.');

var fullString = sb.toString();

assert(fullString == 'Use a StringBuffer for efficient string creation.');
```

#### Regular expressions

#### 正則表示式

The RegExp class provides the same capabilities as JavaScript regular
expressions. Use regular expressions for efficient searching and pattern
matching of strings.

RegExp 類提供與 JavaScript 正則表示式相同的功能。
使用正則表示式可以對字串進行高效搜尋和模式匹配。

<?code-excerpt "misc/test/library_tour/core_test.dart (RegExp)"?>
```dart
// Here's a regular expression for one or more digits.
var numbers = RegExp(r'\d+');

var allCharacters = 'llamas live fifteen to twenty years';
var someDigits = 'llamas live 15 to 20 years';

// contains() can use a regular expression.
assert(!allCharacters.contains(numbers));
assert(someDigits.contains(numbers));

// Replace every match with another string.
var exedOut = someDigits.replaceAll(numbers, 'XX');
assert(exedOut == 'llamas live XX to XX years');
```

You can work directly with the RegExp class, too. The Match class
provides access to a regular expression match.

<?code-excerpt "misc/test/library_tour/core_test.dart (match)"?>
```dart
var numbers = RegExp(r'\d+');
var someDigits = 'llamas live 15 to 20 years';

// Check whether the reg exp has a match in a string.
assert(numbers.hasMatch(someDigits));

// Loop through all matches.
for (final match in numbers.allMatches(someDigits)) {
  print(match.group(0)); // 15, then 20
}
```

#### More information

#### 更多資訊

Refer to the [String API reference][String] for a full list of
methods. Also see the API reference for [StringBuffer,][StringBuffer]
[Pattern,][Pattern] [RegExp,][RegExp] and [Match.][Match]

有關完整的方法列表，
請參考 [String API docs][String]。
另請參考 [StringBuffer，][StringBuffer]
[Pattern，][Pattern] [RegExp，][RegExp] 和 [Match][Match]
的 API 文件。

### Collections

### 集合

Dart ships with a core collections API, which includes classes for
lists, sets, and maps.

Dart 附帶了核心集合 API ，其中包括 list、set 和 map 類別。

{{site.alert.tip}}
  
  To practice using APIs that are available to both lists and sets,
  follow the [Iterable collections codelab](/codelabs/iterables).
  
  如果你需要嘗試一下對列表 (list) 和集合 (set) 都可用的 API，
  請試試看 [遍歷集合](/codelabs/iterables) 這個 codelab 課程。
  
{{site.alert.end}}

#### Lists

As the language tour shows, you can use literals to create and
initialize [lists](#lists). Alternatively, use one of the List
constructors. The List class also defines several methods for adding
items to and removing items from lists.

如語言概覽中介紹，[lists](#lists) 可以透過字面量來建立和初始化。
另外，也可以使用 List 的建構函式。
List 類還定義了若干方法，用於向列表新增或刪除專案。

<?code-excerpt "misc/test/library_tour/core_test.dart (List)"?>
```dart
// Create an empty list of strings.
var grains = <String>[];
assert(grains.isEmpty);

// Create a list using a list literal.
var fruits = ['apples', 'oranges'];

// Add to a list.
fruits.add('kiwis');

// Add multiple items to a list.
fruits.addAll(['grapes', 'bananas']);

// Get the list length.
assert(fruits.length == 5);

// Remove a single item.
var appleIndex = fruits.indexOf('apples');
fruits.removeAt(appleIndex);
assert(fruits.length == 4);

// Remove all elements from a list.
fruits.clear();
assert(fruits.isEmpty);

// You can also create a List using one of the constructors.
var vegetables = List.filled(99, 'broccoli');
assert(vegetables.every((v) => v == 'broccoli'));
```

Use `indexOf()` to find the index of an object in a list:

使用 `indexOf()` 方法查詢一個物件在 list 中的下標值。

<?code-excerpt "misc/test/library_tour/core_test.dart (indexOf)"?>
```dart
var fruits = ['apples', 'oranges'];

// Access a list item by index.
assert(fruits[0] == 'apples');

// Find an item in a list.
assert(fruits.indexOf('apples') == 0);
```

Sort a list using the `sort()` method. You can provide a sorting
function that compares two objects. This sorting function must return \<
0 for *smaller*, 0 for the *same*, and \> 0 for *bigger*. The following
example uses `compareTo()`, which is defined by
[Comparable][] and implemented by String.

使用 `sort()` 方法排序一個 list 。
你可以提供一個排序函式用於比較兩個物件。
比較函式在 *小於* 時返回 \ <0，*相等* 時返回 0，*bigger* 時返回 \> 0 。
下面範例中使用 `compareTo()` 函式，
該函式在 [Comparable][] 中定義，
並被 String 類實現。

<?code-excerpt "misc/test/library_tour/core_test.dart (compareTo)"?>
```dart
var fruits = ['bananas', 'apples', 'oranges'];

// Sort a list.
fruits.sort((a, b) => a.compareTo(b));
assert(fruits[0] == 'apples');
```

Lists are parameterized types
([generics](/guides/language/language-tour#generics)),
so you can specify the type that a list
should contain:

列表是引數化型別（[泛型](/guides/language/language-tour#generics)），
因此可以指定 list 應該包含的元素型別：

<?code-excerpt "misc/test/library_tour/core_test.dart (List-of-String)"?>
```dart
// This list should contain only strings.
var fruits = <String>[];

fruits.add('apples');
var fruit = fruits[0];
assert(fruit is String);
```

{:.fails-sa}
<?code-excerpt "misc/lib/library_tour/core/collections.dart (List-of-String)"?>
```dart
fruits.add(5); // Error: 'int' can't be assigned to 'String'
```

{{site.alert.note}}

  In many cases, you don't
  need to explicitly specify generic
  types, because Dart will
  [infer](/guides/language/type-system#type-inference)
  them for you.
  A list like `['Dash', 'Dart']` is understood
  to be a `List<String>` (read: list of strings).

  在大多數情況下，你不需要顯式指定泛型，因為 Dart 幫你會
  [推斷](/guides/language/type-system#type-inference)
  它們的型別。
  類似於 `['Dash', 'Dart']` 這樣的 list 很容易可以推斷為 `List<String>`。

  But there are times when you _should_ specify
  the generic type. Like, for example, when Dart doesn't have
  anything to infer from: `[]` could be a list of any
  combination of things.
  That's often not what you want, so you write `<String>[]`
  or `<Person>[]` or something similar.

  但有些時候你 **需要** 指定泛型。
  比如：Dart 無法從 `[]` 中推斷出任何型別，它可以是任何型別元素的組合。
  這時推斷的型別基本不會是你想要的，所以你需要寫下
  `<String>[]` 或者 `<Person>[]` 這樣的型別宣告。

{{site.alert.end}}

Refer to the [List API reference][List] for a full list of methods.

全部的方法介紹 ，請參考 [List API docs][List]。

#### Sets

A set in Dart is an unordered collection of unique items. Because a set
is unordered, you can’t get a set’s items by index (position).

在 Dart 中，set 是一個無序的，元素唯一的集合。
因為一個 set 是無序的，所以無法透過下標（位置）獲取 set 中的元素。

<?code-excerpt "misc/test/library_tour/core_test.dart (Set)"?>
```dart
// Create an empty set of strings.
var ingredients = <String>{};

// Add new items to it.
ingredients.addAll(['gold', 'titanium', 'xenon']);
assert(ingredients.length == 3);

// Adding a duplicate item has no effect.
ingredients.add('gold');
assert(ingredients.length == 3);

// Remove an item from a set.
ingredients.remove('gold');
assert(ingredients.length == 2);

// You can also create sets using
// one of the constructors.
var atomicNumbers = Set.from([79, 22, 54]);
```

Use `contains()` and `containsAll()` to check whether one or more
objects are in a set:

使用 `contains()` 和 `containsAll()` 來檢查一個或多個元素是否在 set 中：

<?code-excerpt "misc/test/library_tour/core_test.dart (contains)"?>
```dart
var ingredients = Set<String>();
ingredients.addAll(['gold', 'titanium', 'xenon']);

// Check whether an item is in the set.
assert(ingredients.contains('titanium'));

// Check whether all the items are in the set.
assert(ingredients.containsAll(['titanium', 'xenon']));
```

An intersection is a set whose items are in two other sets.

交集是另外兩個 set 中的公共元素組成的 set。

<?code-excerpt "misc/test/library_tour/core_test.dart (intersection)"?>
```dart
var ingredients = Set<String>();
ingredients.addAll(['gold', 'titanium', 'xenon']);

// Create the intersection of two sets.
var nobleGases = Set.from(['xenon', 'argon']);
var intersection = ingredients.intersection(nobleGases);
assert(intersection.length == 1);
assert(intersection.contains('xenon'));
```

Refer to the [Set API reference][Set] for a full list of methods.

全部的方法介紹 ，請參考 [Set API docs][Set]。

#### Maps

A map, commonly known as a *dictionary* or *hash*, is an unordered
collection of key-value pairs. Maps associate a key to some value for
easy retrieval. Unlike in JavaScript, Dart objects are not maps.

map 是一個無序的 key-value （鍵值對）集合，就是大家熟知的 *dictionary* 或者 *hash*。
map 將 kay 與 value 關聯，以便於檢索。和 JavaScript 不同，Dart 物件不是 map。

You can declare a map using a terse literal syntax, or you can use a
traditional constructor:

宣告 map 可以使用簡潔的字面量語法，也可以使用傳統建構函式：

<?code-excerpt "misc/test/library_tour/core_test.dart (Map)"?>
```dart
// Maps often use strings as keys.
var hawaiianBeaches = {
  'Oahu': ['Waikiki', 'Kailua', 'Waimanalo'],
  'Big Island': ['Wailea Bay', 'Pololu Beach'],
  'Kauai': ['Hanalei', 'Poipu']
};

// Maps can be built from a constructor.
var searchTerms = Map();

// Maps are parameterized types; you can specify what
// types the key and value should be.
var nobleGases = Map<int, String>();
```

You add, get, and set map items using the bracket syntax. Use `remove()`
to remove a key and its value from a map.

透過大括號語法可以為 map 新增，獲取，設定元素。
使用 `remove()` 方法從 map 中移除鍵值對。

<?code-excerpt "misc/test/library_tour/core_test.dart (remove)"?>
```dart
var nobleGases = {54: 'xenon'};

// Retrieve a value with a key.
assert(nobleGases[54] == 'xenon');

// Check whether a map contains a key.
assert(nobleGases.containsKey(54));

// Remove a key and its value.
nobleGases.remove(54);
assert(!nobleGases.containsKey(54));
```

You can retrieve all the values or all the keys from a map:

可以從一個 map 中檢索出所有的 key 或所有的 value：

<?code-excerpt "misc/test/library_tour/core_test.dart (keys)"?>
```dart
var hawaiianBeaches = {
  'Oahu': ['Waikiki', 'Kailua', 'Waimanalo'],
  'Big Island': ['Wailea Bay', 'Pololu Beach'],
  'Kauai': ['Hanalei', 'Poipu']
};

// Get all the keys as an unordered collection
// (an Iterable).
var keys = hawaiianBeaches.keys;

assert(keys.length == 3);
assert(Set.from(keys).contains('Oahu'));

// Get all the values as an unordered collection
// (an Iterable of Lists).
var values = hawaiianBeaches.values;
assert(values.length == 3);
assert(values.any((v) => v.contains('Waikiki')));
```

To check whether a map contains a key, use `containsKey()`. Because map
values can be null, you cannot rely on simply getting the value for the
key and checking for null to determine the existence of a key.

使用 `containsKey()` 方法檢查一個 map 中是否包含某個key 。
因為 map 中的 value 可能會是 null ，
所有透過 key 獲取 value，並透過判斷 value 是否為 null 來判斷 key 是否存在是不可靠的。

<?code-excerpt "misc/test/library_tour/core_test.dart (containsKey)"?>
```dart
var hawaiianBeaches = {
  'Oahu': ['Waikiki', 'Kailua', 'Waimanalo'],
  'Big Island': ['Wailea Bay', 'Pololu Beach'],
  'Kauai': ['Hanalei', 'Poipu']
};

assert(hawaiianBeaches.containsKey('Oahu'));
assert(!hawaiianBeaches.containsKey('Florida'));
```

Use the `putIfAbsent()` method when you want to assign a value to a key
if and only if the key does not already exist in a map. You must provide
a function that returns the value.

如果當且僅當該 key 不存在於 map 中，且要為這個 key 賦值，
可使用 `putIfAbsent()` 方法。
該方法需要一個方法返回這個 value。

<?code-excerpt "misc/test/library_tour/core_test.dart (putIfAbsent)"?>
```dart
var teamAssignments = <String, String>{};
teamAssignments.putIfAbsent('Catcher', () => pickToughestKid());
assert(teamAssignments['Catcher'] != null);
```

Refer to the [Map API reference][Map] for a full list of methods.

全部的方法介紹 ，請參考 [Map API docs][Map]。

#### Common collection methods

#### 公共集合方法

List, Set, and Map share common functionality found in many collections.
Some of this common functionality is defined by the Iterable class,
which List and Set implement.

List, Set, 和 Map 共享許多集合中的常用功能。
其中一些常見功能由 Iterable 類定義，
這些函式由 List 和 Set 實現。

{{site.alert.note}}

  Although Map doesn’t implement Iterable, you can get Iterables from it using
  the Map `keys` and `values` properties.

  雖然Map沒有實現 Iterable，但可以使用 Map `keys` 和 `values` 屬性從中獲取 Iterable 物件。

{{site.alert.end}}

Use `isEmpty` or `isNotEmpty` to check whether a list, set, or map has items:

使用 `isEmpty` 和 `isNotEmpty` 方法可以檢查 list， set 或 map 物件中是否包含元素：

<?code-excerpt "misc/test/library_tour/core_test.dart (isEmpty)"?>
```dart
var coffees = <String>[];
var teas = ['green', 'black', 'chamomile', 'earl grey'];
assert(coffees.isEmpty);
assert(teas.isNotEmpty);
```

To apply a function to each item in a list, set, or map, you can use
`forEach()`:

使用 `forEach()` 可以讓 list， set 或 map 物件中的每個元素都使用一個方法。

<?code-excerpt "misc/test/library_tour/core_test.dart (List.forEach)"?>
```dart
var teas = ['green', 'black', 'chamomile', 'earl grey'];

teas.forEach((tea) => print('I drink $tea'));
```

When you invoke `forEach()` on a map, your function must take two
arguments (the key and value):

當在 map 物件上呼叫 `forEach() 方法時，函式必須帶兩個引數（key 和 value）：

<?code-excerpt "misc/test/library_tour/core_test.dart (Map.forEach)"?>
```dart
hawaiianBeaches.forEach((k, v) {
  print('I want to visit $k and swim at $v');
  // I want to visit Oahu and swim at
  // [Waikiki, Kailua, Waimanalo], etc.
});
```

Iterables provide the `map()` method, which gives you all the results in
a single object:

Iterable 提供 `map()` 方法，這個方法將所有結果返回到一個物件中。

<?code-excerpt "misc/test/library_tour/core_test.dart (List.map)"?>
```dart
var teas = ['green', 'black', 'chamomile', 'earl grey'];

var loudTeas = teas.map((tea) => tea.toUpperCase());
loudTeas.forEach(print);
```

{{site.alert.note}}

  The object returned by `map()` is an Iterable that’s *lazily evaluated*: your
  function isn’t called until you ask for an item from the returned object.

  `map()` 方法返回的物件是一個 *懶求值 (lazily evaluated) *物件：
  只有當存取物件裡面的元素時，函式才會被呼叫。

{{site.alert.end}}

To force your function to be called immediately on each item, use
`map().toList()` or `map().toSet()`:

使用 `map().toList()` 或 `map().toSet()` ，
可以強制在每個專案上立即呼叫函式。

<?code-excerpt "misc/test/library_tour/core_test.dart (toList)"?>
```dart
var loudTeas = teas.map((tea) => tea.toUpperCase()).toList();
```

Use Iterable’s `where()` method to get all the items that match a
condition. Use Iterable’s `any()` and `every()` methods to check whether
some or all items match a condition.

使用 Iterable 的 `where()` 方法可以獲取所有匹配條件的元素。
使用 Iterable 的 `any()` 和 `every()`
方法可以檢查部分或者所有元素是否匹配某個條件。

{% comment %}
PENDING: Change example as suggested by floitsch to have (maybe)
cities instead of isDecaffeinated.
{% endcomment %}


<?code-excerpt "misc/test/library_tour/core_test.dart (where-etc)"?>
```dart
var teas = ['green', 'black', 'chamomile', 'earl grey'];

// Chamomile is not caffeinated.
bool isDecaffeinated(String teaName) => teaName == 'chamomile';

// Use where() to find only the items that return true
// from the provided function.
var decaffeinatedTeas = teas.where((tea) => isDecaffeinated(tea));
// or teas.where(isDecaffeinated)

// Use any() to check whether at least one item in the
// collection satisfies a condition.
assert(teas.any(isDecaffeinated));

// Use every() to check whether all the items in a
// collection satisfy a condition.
assert(!teas.every(isDecaffeinated));
```

For a full list of methods, refer to the [Iterable API reference,][Iterable]
as well as those for [List,][List] [Set,][Set] and [Map.][Map]

有關方法的完整列表，請參考 [Iterable API docs,][Iterable] 以及
[List,][List] [Set,][Set] and [Map.][Map]

### URIs

The [Uri class][Uri] provides
functions to encode and decode strings for use in URIs (which you might
know as *URLs*). These functions handle characters that are special for
URIs, such as `&` and `=`. The Uri class also parses and exposes the
components of a URI—host, port, scheme, and so on.

在使用 URI（可能你會稱它為 *URLs*） 時，[Uri 類][Uri] 提供對字串的編解碼操作。
這些函式用來處理 URI 特有的字元，例如 `＆` 和 `=` 。
Uri 類還可以解析和處理 URI—host，port，scheme等元件。

{% comment %}
{PENDING: show
constructors: Uri.http, Uri.https, Uri.file, per floitsch's suggestion}
{% endcomment %}

#### Encoding and decoding fully qualified URIs

#### 編碼和解碼完整合法的URI

To encode and decode characters *except* those with special meaning in a
URI (such as `/`, `:`, `&`, `#`), use the `encodeFull()` and
`decodeFull()` methods. These methods are good for encoding or decoding
a fully qualified URI, leaving intact special URI characters.

使用 `encodeFull()` 和 `decodeFull()` 方法，
對 URI 中除了特殊字元（例如 `/`， `:`， `&`， `#`）以外的字元進行編解碼，
這些方法非常適合編解碼完整合法的 URI，並保留 URI 中的特殊字元。

<?code-excerpt "misc/test/library_tour/core_test.dart (encodeFull)"?>
```dart
var uri = 'https://example.org/api?foo=some message';

var encoded = Uri.encodeFull(uri);
assert(encoded == 'https://example.org/api?foo=some%20message');

var decoded = Uri.decodeFull(encoded);
assert(uri == decoded);
```

Notice how only the space between `some` and `message` was encoded.

注意上面程式碼只編碼了 `some` 和 `message` 之間的空格。

#### Encoding and decoding URI components

#### 編碼和解碼 URI 元件

To encode and decode all of a string’s characters that have special
meaning in a URI, including (but not limited to) `/`, `&`, and `:`, use
the `encodeComponent()` and `decodeComponent()` methods.

使用 `encodeComponent()` 和 `decodeComponent()` 方法，
對 URI 中具有特殊含義的所有字串字元，
特殊字元包括（但不限於）`/`， `&`， 和  `:`。

<?code-excerpt "misc/test/library_tour/core_test.dart (encodeComponent)"?>
```dart
var uri = 'https://example.org/api?foo=some message';

var encoded = Uri.encodeComponent(uri);
assert(
    encoded == 'https%3A%2F%2Fexample.org%2Fapi%3Ffoo%3Dsome%20message');

var decoded = Uri.decodeComponent(encoded);
assert(uri == decoded);
```

Notice how every special character is encoded. For example, `/` is
encoded to `%2F`.

注意上面程式碼編碼了所有的字元。例如 `/` 被編碼為 `%2F`。

#### Parsing URIs

#### 解析 URI

If you have a Uri object or a URI string, you can get its parts using
Uri fields such as `path`. To create a Uri from a string, use the
`parse()` static method:

使用 Uri 物件的欄位（例如 `path`），
來獲取一個 Uri 物件或者 URI 字串的一部分。
使用 `parse()` 靜態方法，可以使用字串建立 Uri 物件。

<?code-excerpt "misc/test/library_tour/core_test.dart (Uri.parse)"?>
```dart
var uri = Uri.parse('https://example.org:8080/foo/bar#frag');

assert(uri.scheme == 'https');
assert(uri.host == 'example.org');
assert(uri.path == '/foo/bar');
assert(uri.fragment == 'frag');
assert(uri.origin == 'https://example.org:8080');
```

See the [Uri API reference][Uri] for more URI components that you can get.

有關 URI 元件的更多內容，參考 [Uri API docs][Uri]。

#### Building URIs

#### 建構 URI

You can build up a URI from individual parts using the `Uri()`
constructor:

使用 `Uri()` 建構函式，可以將各元件部分建構成 URI 。

<?code-excerpt "misc/test/library_tour/core_test.dart (Uri)"?>
```dart
var uri = Uri(
    scheme: 'https',
    host: 'example.org',
    path: '/foo/bar',
    fragment: 'frag',
    queryParameters: {'lang': 'dart'});
assert(uri.toString() == 'https://example.org/foo/bar?lang=dart#frag');
```

If you don't need to specify a fragment,
to create a URI with a http or https scheme,
you can instead use the [`Uri.http`][] or [`Uri.https`][] factory constructors:

<?code-excerpt "misc/test/library_tour/core_test.dart (Uri-http)"?>
```dart
var httpUri = Uri.http('example.org', '/foo/bar', {'lang': 'dart'});
var httpsUri = Uri.https('example.org', '/foo/bar', {'lang': 'dart'});

assert(httpUri.toString() == 'http://example.org/foo/bar?lang=dart');
assert(httpsUri.toString() == 'https://example.org/foo/bar?lang=dart');
```

[`Uri.http`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Uri/Uri.http.html
[`Uri.https`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Uri/Uri.https.html

### Dates and times

### 日期和時間

A DateTime object is a point in time. The time zone is either UTC or the
local time zone.

DateTime 物件代表某個時刻，時區可以是 UTC 或者 本地時區。

You can create DateTime objects using several constructors:

DateTime 物件可以透過若干建構函式建立：

<?code-excerpt "misc/test/library_tour/core_test.dart (DateTime)"?>
```dart
// Get the current date and time.
var now = DateTime.now();

// Create a new DateTime with the local time zone.
var y2k = DateTime(2000); // January 1, 2000

// Specify the month and day.
y2k = DateTime(2000, 1, 2); // January 2, 2000

// Specify the date as a UTC time.
y2k = DateTime.utc(2000); // 1/1/2000, UTC

// Specify a date and time in ms since the Unix epoch.
y2k = DateTime.fromMillisecondsSinceEpoch(946684800000, isUtc: true);

// Parse an ISO 8601 date.
y2k = DateTime.parse('2000-01-01T00:00:00Z');
```

The `millisecondsSinceEpoch` property of a date returns the number of
milliseconds since the “Unix epoch”—January 1, 1970, UTC:

日期中 `millisecondsSinceEpoch` 屬性返回自
“Unix 紀元（January 1, 1970, UTC）”以來的毫秒數：

<?code-excerpt "misc/test/library_tour/core_test.dart (millisecondsSinceEpoch)"?>
```dart
// 1/1/2000, UTC
var y2k = DateTime.utc(2000);
assert(y2k.millisecondsSinceEpoch == 946684800000);

// 1/1/1970, UTC
var unixEpoch = DateTime.utc(1970);
assert(unixEpoch.millisecondsSinceEpoch == 0);
```

Use the Duration class to calculate the difference between two dates and
to shift a date forward or backward:

<?code-excerpt "misc/test/library_tour/core_test.dart (Duration)"?>
```dart
var y2k = DateTime.utc(2000);

// Add one year.
var y2001 = y2k.add(const Duration(days: 366));
assert(y2001.year == 2001);

// Subtract 30 days.
var december2000 = y2001.subtract(const Duration(days: 30));
assert(december2000.year == 2000);
assert(december2000.month == 12);

// Calculate the difference between two dates.
// Returns a Duration object.
var duration = y2001.difference(y2k);
assert(duration.inDays == 366); // y2k was a leap year.
```

{{site.alert.warning}}

  Using a Duration to shift a DateTime by days can be problematic, due to clock
  shifts (to daylight saving time, for example). Use UTC dates if you must shift
  days.

  由於時鐘轉換（例如，夏令時）的原因，
  使用 Duration 對 DateTime 按天移動可能會有問題。
  如果要按照天數來位移時間，請使用 UTC 日期。

{{site.alert.end}}

For a full list of methods,
refer to the API reference for [DateTime][] and [Duration.][Duration]

參考 [DateTime][] 和 [Duration][] API 文件瞭解全部方法列表。

### Utility classes

### 工具類

The core library contains various utility classes, useful for sorting,
mapping values, and iterating.

核心函式庫包含各種工具類，可用於排序，對映值以及迭代。

#### Comparing objects

#### 比較物件

Implement the [Comparable][]
interface to indicate that an object can be compared to another object,
usually for sorting. The `compareTo()` method returns \< 0 for
*smaller*, 0 for the *same*, and \> 0 for *bigger*.

如果實現了 [Comparable][] 介面，
也就是說可以將該物件與另一個物件進行比較，
通常用於排序。
`compareTo()` 方法在 *小於* 時返回 \< 0，
在 *相等* 時返回 0，
在 *大於* 時返回  \> 0。

<?code-excerpt "misc/lib/library_tour/core/comparable.dart"?>
```dart
class Line implements Comparable<Line> {
  final int length;
  const Line(this.length);

  @override
  int compareTo(Line other) => length - other.length;
}

void main() {
  var short = const Line(1);
  var long = const Line(100);
  assert(short.compareTo(long) < 0);
}
```

#### Implementing map keys

Each object in Dart automatically provides an integer hash code, and
thus can be used as a key in a map. However, you can override the
`hashCode` getter to generate a custom hash code. If you do, you might
also want to override the `==` operator. Objects that are equal (via
`==`) must have identical hash codes. A hash code doesn’t have to be
unique, but it should be well distributed.

在 Dart 中每個物件會預設提供一個整數的雜湊值，
因此在 map 中可以作為 key 來使用，
重寫 `hashCode` 的 getter 方法來產生自訂雜湊值。
如果重寫 `hashCode` 的 getter 方法，那麼可能還需要重寫 `==` 運算子。
相等的（透過 `==` ）物件必須擁有相同的雜湊值。
雜湊值並不要求是唯一的， 但是應該具有良好的分佈形態。

{{site.alert.tip}}

  To consistently and easily implement the `hashCode` getter,
  consider using the static hashing methods provided by the `Object` class.

  想要以一致且簡便的方式實現 `hashCode` getter，
  你可以嘗試 `Object` 類提供的靜態方法。

  To generate a single hash code for multiple properties of an object,
  you can use [`Object.hash()`][].
  To generate a hash code for a collection,
  you can use either [`Object.hashAll()`][] (if element order matters)
  or [`Object.hashAllUnordered()`][].

  想為物件內的多個屬性產生單一的雜湊值，你可以使用 [`Object.hash()`][]。
  想為一組內容產生雜湊值，你可以使用 [`Object.hashAll()`][]
  （如果其中的元素需要保持順序），或者 [`Object.hashAllUnordered()`][]。

{{site.alert.end}}

[`Object.hash()`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Object/hash.html
[`Object.hashAll()`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Object/hashAll.html
[`Object.hashAllUnordered()`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Object/hashAllUnordered.html

{% comment %}
Note: There’s disagreement over whether to include identical() in the ==
implementation. It might improve speed, at least when you need to
compare many fields. They don’t do identical() automatically because, by
convention, NaN != NaN.
{% endcomment %}

<?code-excerpt "misc/lib/library_tour/core/hash_code.dart"?>
```dart
class Person {
  final String firstName, lastName;

  Person(this.firstName, this.lastName);

  // Override hashCode using the static hashing methods
  // provided by the `Object` class.
  @override
  int get hashCode => Object.hash(firstName, lastName);

  // You should generally implement operator `==` if you
  // override `hashCode`.
  @override
  bool operator ==(dynamic other) {
    return other is Person &&
        other.firstName == firstName &&
        other.lastName == lastName;
  }
}

void main() {
  var p1 = Person('Bob', 'Smith');
  var p2 = Person('Bob', 'Smith');
  var p3 = 'not a person';
  assert(p1.hashCode == p2.hashCode);
  assert(p1 == p2);
  assert(p1 != p3);
}
```

#### Iteration

#### 迭代

The [Iterable][] and [Iterator][] classes
support sequential access to a collection of values.
To practice using these collections,
follow the [Iterable collections codelab](/codelabs/iterables).

[Iterable][] 和 [Iterator][] 類支援 for-in 迴圈。
當建立一個類別的時候，繼承或者實現 Iterable，
可以為該類提供用於 for-in 迴圈的 Iterators。
實現 Iterator 來定義實際的遍歷操作。

If you create a class that can provide Iterators for use in for-in loops,
extend (if possible) or implement Iterable.
Implement Iterator to define the actual iteration ability.

如果你在 for-in 迴圈裡要建立一個可以提供 Iterator 的類，
如果可以，請選擇 extend 或者 implement Iterable 的方式。
Implement Iterator 來定義一個實際的迭代能力。

<?code-excerpt "misc/lib/library_tour/core/iterator.dart"?>
```dart
class Process {
  // Represents a process...
}

class ProcessIterator implements Iterator<Process> {
  @override
  Process get current => ...
  @override
  bool moveNext() => ...
}

// A mythical class that lets you iterate through all
// processes. Extends a subclass of [Iterable].
class Processes extends IterableBase<Process> {
  @override
  final Iterator<Process> iterator = ProcessIterator();
}

void main() {
  // Iterable objects can be used with for-in.
  for (final process in Processes()) {
    // Do something with the process.
  }
}
```

### Exceptions

### 例外

The Dart core library defines many common exceptions and errors.
Exceptions are considered conditions that you can plan ahead for and
catch. Errors are conditions that you don’t expect or plan for.

Dart 核心函式庫定義了很多公共的例外和錯誤類別。
例外通常是一些可以預見和預知的情況。
錯誤是無法預見或者預防的情況。

A couple of the most common errors are:

兩個最常見的錯誤：

[NoSuchMethodError][]

:   Thrown when a receiving object (which might be null) does not
    implement a method.

    當方法的接受物件（可能為null）沒有實現該方法時丟擲。

[ArgumentError][]

:   Can be thrown by a method that encounters an unexpected argument.

    當方法在接受到一個不合法引數時丟擲。

Throwing an application-specific exception is a common way to indicate
that an error has occurred. You can define a custom exception by
implementing the Exception interface:

通常透過丟擲一個應用特定的例外，來表示應用發生了錯誤。
透過實現 Exception 介面來自訂例外：

<?code-excerpt "misc/lib/library_tour/core/exception.dart"?>
```dart
class FooException implements Exception {
  final String? msg;

  const FooException([this.msg]);

  @override
  String toString() => msg ?? 'FooException';
}
```

For more information, see
[Exceptions](/guides/language/language-tour#exceptions)
(in the language tour) and the [Exception API reference.][Exception]

更多內容，參考 [Exceptions](#exceptions) 以及
[Exception API 文件。][Exception]

### Weak references and finalizers

### 弱參考和終結器 (finalizers)

Dart is a [garbage-collected][] language,
which means that any Dart object
that isn't referenced
can be disposed by the garbage collector. 
This default behavior might not be desirable in
some scenarios involving native resources or 
if the target object can't be modified.

Dart 語言支援 [垃圾回收 (GC)][garbage-collected]，
即所有未被參考的 Dart 物件最終都會被垃圾回收並銷燬。
某些涉及到原生資源和目標物件無法修改的場景，
GC 的行為可能不會符合預期。

A [WeakReference][]
stores a reference to the target object
that does not affect how it is 
collected by the garbage collector.
Another option is to use an [Expando][]
to add properties to an object.

[WeakReference][] 會儲存目標物件的參考，
並且不會影響目標物件被 GC。
另一種方案是使用 [Expando][] 對物件新增一些屬性。

A [Finalizer][] can be used to execute a callback function
after an object is no longer referenced.
However, it is not guaranteed to execute this callback.

[終結器 (Finalizer)][Finalizer]
可以在物件已不再被參考時執行一個回呼(Callback)函式。
然而，終結器的回呼(Callback)並不保證一定會執行。

A [NativeFinalizer][]
provides stronger guarantees
for interacting with native code using [dart:ffi][];
its callback is invoked at least once
after the object is no longer referenced.
Also, it can be used to close native resources
such as a database connection or open files.

[NativeFinalizer][] 為使用 [dart:ffi][]
與原生互動的程式碼提供了更加強力的回呼(Callback)保證。
它的回呼(Callback)會在物件不再參考後至少呼叫一次。
同時，它也可以用來關閉原生資源，例如資料庫連結和開啟的檔案。

To ensure that an object won't be
garbage collected and finalized too early,
classes can implement the [Finalizable][] interface.
When a local variable is Finalizable, 
it won't be garbage collected
until the code block where it is declared has exited.

想要確保一個物件不會過早地被回收，
其對應的類可以實現 [Finalizable][] 介面。
當一個方法內的變數是 Finalizable，直到程式碼執行完畢後它才會被回收。

{{site.alert.version-note}}

  Support for weak references and finalizers was added in Dart 2.17.

  弱參考和終結器的支援在 Dart 2.17 後開始支援。

{{site.alert.end}}

## dart:async - asynchronous programming

## dart:async - 非同步程式設計

Asynchronous programming often uses callback functions, but Dart
provides alternatives: [Future][] and [Stream][] objects. A
Future is like a promise for a result to be provided sometime in the
future. A Stream is a way to get a sequence of values, such as events.
Future, Stream, and more are in the
dart:async library ([API reference][dart:async]).

非同步程式設計通常使用回呼(Callback)方法來實現，但是 Dart
提供了其他方案：[Future][] 和 [Stream][] 物件。
Future 類似與 JavaScript 中的 Promise ，
代表在將來某個時刻會返回一個結果。
Stream 類可以用來獲取一系列的值，比如，一系列事件。
Future， Stream，以及更多內容，參考
dart:async library ([API reference][dart:async])。

{{site.alert.note}}

  You don't always need to use the Future or Stream APIs directly. The Dart
  language supports asynchronous coding using keywords such as `async` and
  `await`. See the [asynchronous programming codelab](/codelabs/async-await) for
  details.

  你並不總是需要直接使用 Future 或 Stream 的 API。
  Dart 語言支援使用關鍵字（例如，`async` 和 `await` ）來實現非同步程式設計。
  更多詳情，參考這個 codelab 瞭解更多：[asynchronous programming codelab](/codelabs/async-await)。

{{site.alert.end}}

The dart:async library works in both web apps and command-line apps. To
use it, import dart:async:

dart:async 庫可以工作在 web 應用及 command-line 應用。
透過 import dart:async 來使用。

<?code-excerpt "misc/lib/library_tour/async/future.dart (import)"?>
```dart
import 'dart:async';
```

{{site.alert.version-note}}

  As of Dart 2.1, you don't need to import dart:async to use the Future and
  Stream APIs, because dart:core exports those classes.

  從 Dart 2.1 開始，使用 Future 和 Stream 不需要匯入 dart:async ，
  因為 dart:core 庫 export 了這些類別。

{{site.alert.end}}

### Future

Future objects appear throughout the Dart libraries, often as the object
returned by an asynchronous method. When a future *completes*, its value
is ready to use.

在 Dart 庫中隨處可見 Future 物件，通常非同步函式返回的物件就是一個 Future。
當一個 future *完成執行後*，future 中的值就已經可以使用了。


#### Using await

#### 使用 await

Before you directly use the Future API, consider using `await` instead.
Code that uses `await` expressions can be easier to understand
than code that uses the Future API.

在直接使用 Future API 前，首先應該考慮 `await` 來替代。
程式碼中使用 `await` 表示式會比直接使用 Future API 更容易理解。

Consider the following function.  It uses Future's `then()` method
to execute three asynchronous functions in a row,
waiting for each one to complete before executing the next one.

閱讀思考下面程式碼。
程式碼使用 Future 的 `then()` 方法在同一行執行了三個非同步函式，
要等待上一個執行完成，再執行下一個任務。

<?code-excerpt "misc/lib/library_tour/async/future.dart (runUsingFuture)"?>
```dart
void runUsingFuture() {
  // ...
  findEntryPoint().then((entryPoint) {
    return runExecutable(entryPoint, args);
  }).then(flushThenExit);
}
```

The equivalent code with await expressions
looks more like synchronous code:

透過 await 表示式實現等價的程式碼，
看起來非常像同步程式碼：

<?code-excerpt "misc/lib/library_tour/async/future.dart (runUsingAsyncAwait)"?>
```dart
Future<void> runUsingAsyncAwait() async {
  // ...
  var entryPoint = await findEntryPoint();
  var exitCode = await runExecutable(entryPoint, args);
  await flushThenExit(exitCode);
}
```

An `async` function can catch exceptions from Futures.
For example:

`async` 函式能夠捕獲來自 Future 的例外。
例如：

<?code-excerpt "misc/lib/library_tour/async/future.dart (catch)"?>
```dart
var entryPoint = await findEntryPoint();
try {
  var exitCode = await runExecutable(entryPoint, args);
  await flushThenExit(exitCode);
} catch (e) {
  // Handle the error...
}
```

{{site.alert.important}}

  Async functions return Futures. If you don't want your function to return a
  future, then use a different solution. For example, you might call an `async`
  function from your function.

  `async` 函式 返回 Future 物件。
  如果你不希望你的函式返回一個 future 物件，
  可以使用其他方案。
  例如，你可以在你的方法中呼叫一個 async 方法。

{{site.alert.end}}

For more information on using `await` and related Dart language features,
see the [asynchronous programming codelab](/codelabs/async-await).

更多關於 `await` 的使用及相關的 Dart 語言特徵，參考
[Asynchrony support](/guides/language/language-tour#asynchrony-support)。

#### Basic usage

#### 基本用法

{% comment %}
[PENDING: Delete much of the following content in favor of the tutorial coverage?]
{% endcomment %}

You can use `then()` to schedule code that runs when the future completes. For
example, `HttpRequest.getString()` returns a Future, since HTTP requests
can take a while. Using `then()` lets you run some code when that Future
has completed and the promised string value is available:

當 future 執行完成後，`then()` 中的程式碼會被執行。
`then()` 中的程式碼會在 future 完成後被執行。
例如，`HttpRequest.getString()` 返回一個 future 物件，
因為 HTTP 請求可能需要一段時間。
當 Future 完成並且保證字串值有效後，
使用 `then()` 來執行你需要的程式碼：

<?code-excerpt "misc/lib/library_tour/async/basic.dart (then)"?>
```dart
HttpRequest.getString(url).then((String result) {
  print(result);
});
```

Use `catchError()` to handle any errors or exceptions that a Future
object might throw.

使用 `catchError()` 來處理一些 Future 物件可能丟擲的錯誤或者例外。

<?code-excerpt "misc/lib/library_tour/async/basic.dart (catchError)"?>
```dart
HttpRequest.getString(url).then((String result) {
  print(result);
}).catchError((e) {
  // Handle or ignore the error.
});
```

The `then().catchError()` pattern is the asynchronous version of
`try`-`catch`.

`then().catchError()` 組合是 `try`-`catch` 的非同步版本。

{{site.alert.important}}

  Be sure to invoke `catchError()` on the result of `then()`—not on the result
  of the original Future. Otherwise, the `catchError()` can handle errors only
  from the original Future's computation, but not from the handler registered by
  `then()`.

  確保呼叫 `catchError()` 方式在 `then()` 的結果上，而不是在原來的 Future 物件上呼叫。
  否則的話，`catchError()` 就只能處理原來 Future 物件丟擲的例外，
  而無法處理 `then()` 程式碼裡面的例外。

{{site.alert.end}}

#### Chaining multiple asynchronous methods

#### 鏈式非同步程式設計

The `then()` method returns a Future, providing a useful way to run
multiple asynchronous functions in a certain order. 
If the callback registered with `then()` returns a Future, 
`then()` returns a Future that will complete
with the same result as the Future returned from the callback. 
If the callback returns a value of any other type,
`then()` creates a new Future that completes with the value.

`then()` 方法返回一個 Future 物件，
這樣就提供了一個非常好的方式讓多個非同步方法按順序依次執行。
如果用 `then()` 註冊的回呼(Callback)返回一個 Future ，
那麼 `then()` 返回一個等價的 Future 。
如果回呼(Callback)返回任何其他型別的值，
那麼 `then()` 會建立一個以該值完成的新 Future 。

<?code-excerpt "misc/lib/library_tour/async/future.dart (then-chain)"?>
```dart
Future result = costlyQuery(url);
result
    .then((value) => expensiveWork(value))
    .then((_) => lengthyComputation())
    .then((_) => print('Done!'))
    .catchError((exception) {
  /* Handle exception... */
});
```

In the preceding example, the methods run in the following order:

在上面的範例中，方法按下面順序執行：

1.  `costlyQuery()`
2.  `expensiveWork()`
3.  `lengthyComputation()`

Here is the same code written using await:

這是使用 await 編寫的等效程式碼：

<?code-excerpt "misc/lib/library_tour/async/future.dart (then-chain-as-await)"?>
```dart
try {
  final value = await costlyQuery(url);
  await expensiveWork(value);
  await lengthyComputation();
  print('Done!');
} catch (e) {
  /* Handle exception... */
}
```


#### Waiting for multiple futures

#### 等待多個 Future

Sometimes your algorithm needs to invoke many asynchronous functions and
wait for them all to complete before continuing. Use the [Future.wait()][]
static method to manage multiple Futures and wait for them to complete:

有時程式碼邏輯需要呼叫多個非同步函式，
並等待它們全部完成後再繼續執行。
使用 [Future.wait()][] 靜態方法管理多個 Future 以及等待它們完成：

<?code-excerpt "misc/lib/library_tour/async/future.dart (wait)" replace="/elideBody;/\/* ... *\//g"?>
```dart
Future<void> deleteLotsOfFiles() async =>  ...
Future<void> copyLotsOfFiles() async =>  ...
Future<void> checksumLotsOfOtherFiles() async =>  ...

await Future.wait([
  deleteLotsOfFiles(),
  copyLotsOfFiles(),
  checksumLotsOfOtherFiles(),
]);
print('Done with all the long steps!');
```


### Stream

Stream objects appear throughout Dart APIs, representing sequences of
data. For example, HTML events such as button clicks are delivered using
streams. You can also read a file as a stream.

在 Dart API 中 Stream 物件隨處可見，Stream 用來表示一系列資料。
例如，HTML 中的按鈕點選就是透過 stream 傳遞的。
同樣也可以將檔案作為資料流來讀取。


#### Using an asynchronous for loop

#### 非同步迴圈

Sometimes you can use an asynchronous for loop (`await for`)
instead of using the Stream API.

有時，可以使用非同步 for迴圈 `await for` ，來替代 Stream API 。

Consider the following function.
It uses Stream's `listen()` method
to subscribe to a list of files,
passing in a function literal that searches each file or directory.

思考下面範例函式。
它使用 Stream 的 `listen()` 方法來訂閱檔案列表，
傳入一個搜尋檔案或目錄的函式

<?code-excerpt "misc/lib/library_tour/async/stream.dart (listen)" replace="/listen/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
void main(List<String> arguments) {
  // ...
  FileSystemEntity.isDirectory(searchPath).then((isDir) {
    if (isDir) {
      final startingDir = Directory(searchPath);
      startingDir.list().[!listen!]((entity) {
        if (entity is File) {
          searchFile(entity, searchTerms);
        }
      });
    } else {
      searchFile(File(searchPath), searchTerms);
    }
  });
}
{% endprettify %}

The equivalent code with await expressions,
including an asynchronous for loop (`await for`),
looks more like synchronous code:

下面是使用 await 表示式和非同步 for迴圈 (`await for`) 實現的等價的程式碼，
看起來更像是同步程式碼：

<?code-excerpt "misc/lib/library_tour/async/stream.dart (await-for)" replace="/await for/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
void main(List<String> arguments) async {
  // ...
  if (await FileSystemEntity.isDirectory(searchPath)) {
    final startingDir = Directory(searchPath);
    [!await for!] (final entity in startingDir.list()) {
      if (entity is File) {
        searchFile(entity, searchTerms);
      }
    }
  } else {
    searchFile(File(searchPath), searchTerms);
  }
}
{% endprettify %}

{{site.alert.important}}

  Before using `await for`, make sure that it makes the code clearer and that
  you really do want to wait for all of the stream's results. For example, you
  usually should **not** use `await for` for DOM event listeners, because the
  DOM sends endless streams of events. If you use `await for` to register two
  DOM event listeners in a row, then the second kind of event is never handled.

  在使用 `await for` 前，確認這樣能保持程式碼清晰，
  並希望獲取所有 stream 的結果。
  例如，你通常並 **不** 會使用 `await for` 來監聽 DOM 事件，
  因為 DOM 會發送無盡的流事件。
  如果在同一行使用 `await for` 註冊兩個 DOM 事件，
  那麼第二個事件永遠不會被處理。

{{site.alert.end}}

For more information on using `await` and related
Dart language features, see the
[asynchronous programming codelab](/codelabs/async-await).

有關 `await` 的使用及 Dart 語言的相關資訊，參考
[Asynchrony support](/guides/language/language-tour#asynchrony-support)。


#### Listening for stream data

#### 監聽流資料 (stream data)

To get each value as it arrives, either use `await for` or
subscribe to the stream using the `listen()` method:

使用 `await for` 或者使用 `listen()` 方法監聽 stream，
來獲取每個到達的資料流值：

<?code-excerpt "misc/lib/library_tour/async/stream_web.dart (listen)" replace="/listen/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
// Add an event handler to a button.
submitButton.onClick.[!listen!]((e) {
  // When the button is clicked, it runs this code.
  submitData();
});
{% endprettify %}

In this example, the `onClick` property is a `Stream` object provided by
the submit button.

下面範例中，ID 為 "submitInfo" button 提供的 `onClick` 屬性是一個 Stream 物件。

If you care about only one event, you can get it using a property such
as `first`, `last`, or `single`. To test the event before handling it,
use a method such as `firstWhere()`, `lastWhere()`, or `singleWhere()`.

如果只關心其中一個事件，可以使用，例如，`first`， `last`，或 `single` 屬性來獲取。
要在處理時間前對事件進行測試，可以使用，例如 `firstWhere()`， `lastWhere()`， 或 `singleWhere()` 方法。

{% comment %}
{PENDING: example}
{% endcomment %}

If you care about a subset of events, you can use methods such as
`skip()`, `skipWhile()`, `take()`, `takeWhile()`, and `where()`.

如果只關心事件中的一個子集，可以使用，例如，`skip()`， `skipWhile()`，`take()`，`takeWhile()`， 和 `where()`。

{% comment %}
{PENDING: example}
{% endcomment %}


#### Transforming stream data

#### 傳遞流資料 (stream data)

Often, you need to change the format of a stream's data before you can
use it. Use the `transform()` method to produce a stream with a
different type of data:

常常，在使用流資料前需要改變資料的格式。
使用 `transform()` 方法產生具有不同型別資料的流：

<?code-excerpt "misc/lib/library_tour/async/stream.dart (transform)"?>
```dart
var lines =
    inputStream.transform(utf8.decoder).transform(const LineSplitter());
```

This example uses two transformers. First it uses utf8.decoder to
transform the stream of integers into a stream of strings. Then it uses
a LineSplitter to transform the stream of strings into a stream of
separate lines. These transformers are from the dart:convert library (see the
[dart:convert section](#dartconvert---decoding-and-encoding-json-utf-8-and-more)).

上面例子中使用了兩個 transformer 。
第一個使用 utf8.decoder 將整型流轉換為字串流。
接著，使用了 LineSplitter 將字串流轉換為多行字串流。
這些 transformer 來自 dart:convert 庫
（參考[dart:convert section](#dartconvert---decoding-and-encoding-json-utf-8-and-more)）。

{% comment %}
  PENDING: add onDone and onError. (See "Streaming file contents".)
{% endcomment %}


#### Handling errors and completion

#### 處理錯誤和完成

How you specify error and completion handling code
depends on whether you use an asynchronous for loop (`await for`)
or the Stream API.

處理錯誤和完成程式碼方式，
取決於使用的是 非同步 for迴圈（`await for`）還是 Stream API 。

If you use an asynchronous for loop,
then use try-catch to handle errors.
Code that executes after the stream is closed
goes after the asynchronous for loop.

如果使用的是非同步 for迴圈，
那麼透過  try-catch 來處理錯誤。
程式碼位於非同步 for迴圈之後，
會在 stream 被關閉後執行。

<?code-excerpt "misc/lib/library_tour/async/stream.dart (readFileAwaitFor)" replace="/try|catch/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
Future<void> readFileAwaitFor() async {
  var config = File('config.txt');
  Stream<List<int>> inputStream = config.openRead();

  var lines =
      inputStream.transform(utf8.decoder).transform(const LineSplitter());
  [!try!] {
    await for (final line in lines) {
      print('Got ${line.length} characters from stream');
    }
    print('file is now closed');
  } [!catch!] (e) {
    print(e);
  }
}
{% endprettify %}

If you use the Stream API,
then handle errors by registering an `onError` listener.
Run code after the stream is closed by registering
an `onDone` listener.

如果使用的是 Stream API，
那麼透過註冊 `onError` 監聽來處理錯誤。
程式碼位於註冊的 `onDone` 中，
會在 stream 被關閉後執行。

<?code-excerpt "misc/lib/library_tour/async/stream.dart (onDone)" replace="/onDone|onError/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
var config = File('config.txt');
Stream<List<int>> inputStream = config.openRead();

inputStream.transform(utf8.decoder).transform(const LineSplitter()).listen(
    (String line) {
  print('Got ${line.length} characters from stream');
}, [!onDone!]: () {
  print('file is now closed');
}, [!onError!]: (e) {
  print(e);
});
{% endprettify %}


### More information

### 更多內容

For some examples of using Future and Stream in command-line apps,
see the [dart:io tour][dart:io tour].
Also see these articles, codelabs, and tutorials:

更多在 command-line 應用中使用 Future 和 Stream 的例項，參考
[dart:io 概覽][dart:io tour]
也可以參考下列文章和課程：

-   [Asynchronous programming: futures, async, await](/codelabs/async-await)
-   [Futures and error handling](/guides/libraries/futures-error-handling)
-   [Asynchronous programming: streams](/tutorials/language/streams)
-   [Creating streams in Dart](/articles/libraries/creating-streams)
-   [Dart asynchronous programming: Isolates and event loops](https://medium.com/dartlang/dart-asynchronous-programming-isolates-and-event-loops-bffc3e296a6a)

## dart:math - math and random

## dart:math - 數學和隨機數

The dart:math library ([API reference][dart:math])
provides common functionality such as sine and cosine,
maximum and minimum, and constants such as *pi* and *e*. Most of the
functionality in the Math library is implemented as top-level functions.

dart:math 庫（[API reference][dart:math]）
提供通用的功能，例如，正弦和餘弦，
最大值和最小值，以及數學常數，例如 *pi* 和 *e*。
大多數在 Math 庫中的功能是作為最上層函式實現的。

To use this library in your app, import dart:math.

透過匯入 `dart:math` 來引入使用該函式庫。

<?code-excerpt "misc/test/library_tour/math_test.dart (import)"?>
```dart
import 'dart:math';
```


### Trigonometry

### 三角函式

The Math library provides basic trigonometric functions:

Math 庫提供基本的三角函式：

<?code-excerpt "misc/test/library_tour/math_test.dart (trig)"?>
```dart
// Cosine
assert(cos(pi) == -1.0);

// Sine
var degrees = 30;
var radians = degrees * (pi / 180);
// radians is now 0.52359.
var sinOf30degrees = sin(radians);
// sin 30° = 0.5
assert((sinOf30degrees - 0.5).abs() < 0.01);
```

{{site.alert.note}}

  These functions use radians, not degrees!

  這些函式引數單位是弧度，不是角度！

{{site.alert.end}}


### Maximum and minimum

### 最大值和最小值

The Math library provides `max()` and `min()` methods:

Math 庫提供 `max()` 和 `min()` 方法：

<?code-excerpt "misc/test/library_tour/math_test.dart (min-max)"?>
```dart
assert(max(1, 1000) == 1000);
assert(min(1, -1000) == -1000);
```


### Math constants

### 數學常數

Find your favorite constants—*pi*, *e*, and more—in the Math library:

在 Math 庫中可以找到你需要的數學常數，例如，*pi*， *e* 等等：

<?code-excerpt "misc/test/library_tour/math_test.dart (constants)"?>
```dart
// See the Math library for additional constants.
print(e); // 2.718281828459045
print(pi); // 3.141592653589793
print(sqrt2); // 1.4142135623730951
```


### Random numbers

### 隨機數

Generate random numbers with the [Random][] class. You can
optionally provide a seed to the Random constructor.

使用 [Random][] 類產生隨機數。
可以為 Random 建構函式提供一個可選的種子引數。

<?code-excerpt "misc/test/library_tour/math_test.dart (Random)"?>
```dart
var random = Random();
random.nextDouble(); // Between 0.0 and 1.0: [0, 1)
random.nextInt(10); // Between 0 and 9.
```

You can even generate random booleans:

也可以產生隨機布林值序列：

<?code-excerpt "misc/test/library_tour/math_test.dart (Random-bool)"?>
```dart
var random = Random();
random.nextBool(); // true or false
```

{{site.alert.warning}}

  The default implementation of `Random` supplies a stream of pseudorandom bits
  that are unsuitable for cryptographic purposes.
  To create a cryptographically secure random number generator,
  use the [`Random.secure()`][] constructor.

  `Random` 的預設實現提供的是不適合於加密用途的偽隨機位流。
  若你需要建立安全的隨機數產生器，請使用 [`Random.secure()`] 構造。

{{site.alert.end}}

### More information

### 更多內容

Refer to the [Math API reference][dart:math] for a full list of methods.
Also see the API reference for [num,][num] [int,][int] and [double.][double]

完整方法列表參考 [Math API docs][dart:math]。
在 API 文件中參考 [num,][num] [int,][int] 和 [double][double]。

## dart:convert - decoding and encoding JSON, UTF-8, and more

## dart:convert - 編解碼JSON，UTF-8等

The dart:convert library ([API reference][dart:convert])
has converters for JSON and UTF-8, as well as support for creating
additional converters. [JSON][] is a simple text format for representing
structured objects and collections. [UTF-8][] is a common variable-width
encoding that can represent every character in the Unicode character
set.

dart:convert 庫 （[API reference][dart:convert]）提供 JSON 和 UTF-8 轉換器，
以及建立其他轉換器。
[JSON][] 是一種用於表示結構化物件和集合的簡單文字格式。
[UTF-8][] 是一種常見的可變寬度編碼，可以表示Unicode字集中的每個字元。

To use this library, import dart:convert.

使用時，透過 import dart:convert 引入。

<?code-excerpt "misc/test/library_tour/convert_test.dart (import)"?>
```dart
import 'dart:convert';
```


### Decoding and encoding JSON

### 編解碼JSON

Decode a JSON-encoded string into a Dart object with `jsonDecode()`:

使用 `jsonDecode()` 解碼 JSON 編碼的字串為 Dart 物件：

<?code-excerpt "misc/test/library_tour/convert_test.dart (json-decode)"?>
```dart
// NOTE: Be sure to use double quotes ("),
// not single quotes ('), inside the JSON string.
// This string is JSON, not Dart.
var jsonString = '''
  [
    {"score": 40},
    {"score": 80}
  ]
''';

var scores = jsonDecode(jsonString);
assert(scores is List);

var firstScore = scores[0];
assert(firstScore is Map);
assert(firstScore['score'] == 40);
```

Encode a supported Dart object into a JSON-formatted string with
`jsonEncode()`:

使用 `jsonEncode()` 編碼 Dart 物件為 JSON 格式的字串：

<?code-excerpt "misc/test/library_tour/convert_test.dart (json-encode)"?>
```dart
var scores = [
  {'score': 40},
  {'score': 80},
  {'score': 100, 'overtime': true, 'special_guest': null}
];

var jsonText = jsonEncode(scores);
assert(jsonText ==
    '[{"score":40},{"score":80},'
        '{"score":100,"overtime":true,'
        '"special_guest":null}]');
```

Only objects of type int, double, String, bool, null, List, or Map (with
string keys) are directly encodable into JSON. List and Map objects are
encoded recursively.

只有 int， double， String， bool, null, List, 或者 Map 型別物件可以直接編碼成 JSON。
List 和 Map 物件進行遞迴編碼。

You have two options for encoding objects that aren't directly
encodable. The first is to invoke `jsonEncode()` with a second argument: a
function that returns an object that is directly encodable. Your second
option is to omit the second argument, in which case the encoder calls
the object's `toJson()` method.

不能直接編碼的物件有兩種方式對其編碼。
第一種方式是呼叫 `jsonEncode()` 時賦值第二個引數，
這個引數是一個函式，
該函式返回一個能夠直接編碼的物件
第二種方式是省略第二個引數，著這種情況下編碼器呼叫物件的 `toJson()` 方法。

For more examples and links to JSON-related packages, see
[Using JSON](/guides/json).

更多範例及 JSON 包相關連結，參考 [JSON Support](/guides/json) 。


### Decoding and encoding UTF-8 characters

### 編解碼 UTF-8 字元

Use `utf8.decode()` to decode UTF8-encoded bytes to a Dart string:

使用 `utf8.decode()` 解碼 UTF8 編碼的字元創為 Dart 字串：

<?code-excerpt "misc/test/library_tour/convert_test.dart (utf8-decode)" replace="/ \/\/line-br.*//g"?>
```dart
List<int> utf8Bytes = [
  0xc3, 0x8e, 0xc3, 0xb1, 0xc5, 0xa3, 0xc3, 0xa9,
  0x72, 0xc3, 0xb1, 0xc3, 0xa5, 0xc5, 0xa3, 0xc3,
  0xae, 0xc3, 0xb6, 0xc3, 0xb1, 0xc3, 0xa5, 0xc4,
  0xbc, 0xc3, 0xae, 0xc5, 0xbe, 0xc3, 0xa5, 0xc5,
  0xa3, 0xc3, 0xae, 0xe1, 0xbb, 0x9d, 0xc3, 0xb1
];

var funnyWord = utf8.decode(utf8Bytes);

assert(funnyWord == 'Îñţérñåţîöñåļîžåţîờñ');
```

To convert a stream of UTF-8 characters into a Dart string, specify
`utf8.decoder` to the Stream `transform()` method:

將 UTF-8 字串流轉換為 Dart 字串，
為 Stream 的 `transform()` 方法上指定 `utf8.decoder`：

<?code-excerpt "misc/test/library_tour/io_test.dart (utf8-decoder)" replace="/utf8.decoder/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
var lines = [!utf8.decoder!].bind(inputStream).transform(const LineSplitter());
try {
  await for (final line in lines) {
    print('Got ${line.length} characters from stream');
  }
  print('file is now closed');
} catch (e) {
  print(e);
}
{% endprettify %}

Use `utf8.encode()` to encode a Dart string as a list of UTF8-encoded
bytes:

使用 `utf8.encode()` 將 Dart 字串編碼為一個 UTF8 編碼的位元組流：

<?code-excerpt "misc/test/library_tour/convert_test.dart (utf8-encode)" replace="/ \/\/line-br.*//g"?>
```dart
List<int> encoded = utf8.encode('Îñţérñåţîöñåļîžåţîờñ');

assert(encoded.length == utf8Bytes.length);
for (int i = 0; i < encoded.length; i++) {
  assert(encoded[i] == utf8Bytes[i]);
}
```


### Other functionality

### 其他功能

The dart:convert library also has converters for ASCII and ISO-8859-1
(Latin1). For details, see the [API reference for the dart:convert library.][dart:convert]

dart:convert 庫同樣包含 ASCII 和 ISO-8859-1 (Latin1) 轉換器。
更多詳情，參考 [API docs for the dart:convert library。][dart:convert]


## dart:html - browser-based apps {#darthtml}

## dart:html - 基於瀏覽器應用 {#darthtml}

{% include_relative _dart-html-tour.md %}

## dart:io - I/O for servers and command-line apps {#dartio}

## dart:io - 伺服器和命令列應用程式的 I/O 。 {#dartio}

{% include_relative _dart-io-tour.md %}

## Summary

## 總結

This page introduced you to the most commonly used functionality in
Dart’s built-in libraries. It didn’t cover all the built-in
libraries, however. Others that you might want to look into include
[dart:collection][] and [dart:typed\_data,][dart:typed\_data]
as well as platform-specific libraries like the
[Dart web development libraries][webdev libraries]
and the [Flutter libraries.][docs.flutter]

本頁向您介紹了 Dart 內建庫中最常用的功能。
但是，並沒有涵蓋所有內建庫。
您可能想要檢視的其他內容包括 [dart:collection][] 和 [dart:typed\_data,][dart:typed\_data] ，
以及特定於平台的函式庫，如 [Dart web development libraries][webdev libraries] 和 [Flutter libraries.][docs.flutter] 。

You can get yet more libraries by using the [pub package manager](/guides/packages). The
[collection,]({{site.pub-pkg}}/collection)
[crypto,]({{site.pub-pkg}}/crypto)
[http,]({{site.pub-pkg}}/http)
[intl,]({{site.pub-pkg}}/intl) and
[test]({{site.pub-pkg}}/test) libraries are just a
sampling of what you can install using pub.

您可以使用 [pub 包管理](/guides/packages) 工具獲得更多庫。
[collection,]({{site.pub}}/packages/collection)
[crypto,]({{site.pub}}/packages/crypto)
[http,]({{site.pub}}/packages/http)
[intl,]({{site.pub}}/packages/intl) 以及
[test]({{site.pub}}/packages/test)
以上只是簡單的列舉了一些可以透過 pub 安裝的函式庫。

To learn more about the Dart language, see the
[language tour][].

要了解有關 Dart 語言的更多資訊，請參考 [語言概覽][language tour]。

[ArgumentError]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/ArgumentError-class.html
[Assert]: /guides/language/language-tour#assert
[Comparable]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Comparable-class.html
[Dart API]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}
[DateTime]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/DateTime-class.html
[Duration]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Duration-class.html
[Exception]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Exception-class.html
[Expando]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Expando-class.html
[Finalizable]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/Finalizable-class.html
[Finalizer]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Finalizer-class.html
[Future.wait()]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-async/Future/wait.html
[Future]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-async/Future-class.html
[IndexedDB]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-indexed_db/dart-indexed_db-library.html
[Iterable]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Iterable-class.html
[Iterator]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Iterator-class.html
[JSON]: https://www.json.org/
[List]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/List-class.html
[Map]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Map-class.html
[Match]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Match-class.html
[NativeFinalizer]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/NativeFinalizer-class.html
[NoSuchMethodError]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/NoSuchMethodError-class.html
[Object]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Object-class.html
[Pattern]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Pattern-class.html
[Random]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-math/Random-class.html
[RegExp]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/RegExp-class.html
[Set]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Set-class.html
[Stream]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-async/Stream-class.html
[StringBuffer]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/StringBuffer-class.html
[String]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/String-class.html
[Symbol]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Symbol-class.html
[UTF-8]: https://en.wikipedia.org/wiki/UTF-8
[Uri]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Uri-class.html
[WeakReference]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/WeakReference-class.html
[`Random.secure()`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-math/Random/Random.secure.html
[dart:async]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-async/dart-async-library.html
[dart:collection]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-collection/dart-collection-library.html
[dart:convert]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-convert/dart-convert-library.html
[dart:core]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/dart-core-library.html
[dart:ffi]: /guides/libraries/c-interop
[dart:io tour]: #dartio
[dart:math]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-math/dart-math-library.html
[dart:typed\_data]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-typed_data/dart-typed_data-library.html
[docs.flutter]: {{site.flutter-api}}
[double]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/double-class.html
[garbage-collected]: https://medium.com/flutter/flutter-dont-fear-the-garbage-collector-d69b3ff1ca30
[int]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/int-class.html
[language tour]: /guides/language/language-tour
[num]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/num-class.html
[toStringAsFixed()]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/num/toStringAsFixed.html
[toStringAsPrecision()]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/num/toStringAsPrecision.html
[weak reference]: https://en.wikipedia.org/wiki/Weak_reference
[web audio]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-web_audio/dart-web_audio-library.html
[webdev libraries]: /web/libraries
