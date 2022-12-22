---
title: Dart cheatsheet codelab
title: Dart 速查表 codelab
description: Interactively learn (or relearn) some of Dart's unique features.
description: 用互動的形式學習（或回顧）Dart 的獨特之處。
js: [{url: 'https://dartpad.cn/inject_embed.dart.js', defer: true}]
---
<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /(^|\n) *\/\/\s+ignore:[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore:[^\n]+\n/$1\n/g"?>

The Dart language is designed to be easy to learn for
coders coming from other languages,
but it has a few unique features.
This codelab—which is based on a
[Dart language cheatsheet](/guides/language/cheatsheet)
written by and for Google engineers—walks you through
the most important of these language features.

Dart 語言旨在讓從其他程式語言轉來的開發者們能夠輕鬆學習，但也有它的獨特之處。
本篇將基於谷歌工程師編寫的
[Dart 語言速查表](/guides/language/cheatsheet)
為你介紹一些最重要的語言特性。

The embedded editors in this codelab have partially completed code snippets.
You can use these editors to test your knowledge by completing the code and
clicking the **Run** button.
If you need help, click the **Hint** button.
To run the code formatter ([dart format](/tools/dart-format)), click **Format**.
The **Reset** button erases your work and
restores the editor to its original state.

在這篇 codelab 中的嵌入式編輯器已經完成了部分程式碼片段。
你可以在這些編輯器上將程式碼補充完整，然後點選 **Run (執行)** 按鈕進行測試。
如果你需要幫助，請點選 **Hint (提示)** 按鈕。
要執行程式碼格式化 ([dart format](/tools/dartfmt))，
點選 **Format (格式化)** 按鈕，**Reset (重置)** 按鈕將會清除你的操作，
並把編輯器恢復到初始狀態。

{{site.alert.note}}

  This page uses embedded DartPads to display runnable examples.
  
  本頁面內嵌了一些 DartPads 做例子展示，

  {% include dartpads-embedded-troubleshooting.md %}
  
{{site.alert.end}}

## String interpolation

## 字串插值

To put the value of an expression inside a string, use `${expression}`.
If the expression is an identifier, you can omit the `{}`.

為了將表示式的值放在字串中，請使用 `${expression}`。若表示式為單個識別符號，則可以省略 `{}`。

Here are some examples of using string interpolation:

下面是一些使用字串插值的例子：

| String  | | Result |
|-----------------------------+-+ -------|
| 字串  | | 結果 |
| `'${3 + 2}'`                | | `'5'` |
| `'${"word".toUpperCase()}'` | | `'WORD'` |
| `'$myObject'`               | | The value of `myObject.toString()`|
| `'$myObject'`               | | `myObject.toString()` 的值|

### Code example
{: .no_toc}

### 程式碼範例

The following function takes two integers as parameters.
Make it return a string containing both integers separated by a space.
For example, `stringify(2, 3)` should return `'2 3'`.

下面的方法接收兩個整形變數作為引數，
然後讓它返回一個包含以空格分隔的整數的字串。
例如，`stringify(2, 3)` 應該返回 `'2 3'`。

```dart:run-dartpad:ga_id-string_interpolation
{$ begin main.dart $}
String stringify(int x, int y) {
  TODO('Return a formatted string here');
}
{$ end main.dart $}
{$ begin solution.dart $}
String stringify(int x, int y) {
  return '$x $y';
}
{$ end solution.dart $}
{$ begin test.dart $}

void main() {
  try {
    final str = stringify(2, 3); 

    if (str == '2 3') {
      _result(true);
    } else if (str == '23') {
      _result(false, ['Test failed. It looks like you forgot the space!']);
    } else {
      _result(false, ['That\'s not quite right. Keep trying!']);
    }
  } on UnimplementedError {
    _result(false, ['Test failed. Did you implement the method?']);
  } catch (e) {
    _result(false, ['Tried calling stringify(2, 3), but received an exception: ${e.runtimeType}']);
  }
}
{$ end test.dart $}
{$ begin hint.txt $}
Both x and y are simple values,
and Dart's string interpolation will handle
converting them to string representations.
All you need to do is use the $ operator to
reference them inside single quotes, with a space in between.
{$ end hint.txt $}
```

## Nullable variables

## 可空的變數

Dart 2.12 introduced sound null safety,
meaning that (when you [enable null safety](/null-safety#enable-null-safety))
values can’t be null unless you say they can be.
In other words, types are non-nullable by default.

Dart 2.12 引入了健全的空安全，這意味著在您
[啟用了空安全](/null-safety#enable-null-safety) 時，
除非變數顯式宣告為可空型別，否則它們將不能為空。
換句話說，型別預設是不可為空的。

For example, consider the following code,
which is **invalid** because (with null safety)
a variable of type `int` can't have the value `null`:

舉個例子，下面的程式碼在空安全下是有錯誤的，
因為 `int` 型別的變數不能為 `null`：

<?code-excerpt "misc/bin/cheatsheet/nullable.dart (invalid-null)" replace="/null;/[!null!];/g"?>
```dart
int a = [!null!]; // INVALID in null-safe Dart.
```

When creating a variable in Dart 2.12 or higher,
you can add `?` to the type to indicate
that the variable can be `null`:

在 Dart 2.12 或更高版本的 Dart 中（需要限制 SDK 為 2.12 及以上），
你可以透過在型別後新增 `?` 來表示該型別可空：

<?code-excerpt "misc/bin/cheatsheet/nullable.dart (valid-null)" replace="/int\?/[!int?!]/g"?>
```dart
[!int?!] a = null; // Valid in null-safe Dart.
```

You can simplify that code a bit because, in all versions of Dart,
`null` is the default value for uninitialized variables:

在所有 Dart 版本中，`null` 在未初始化的變數裡都是預設值，
所以你可以這樣簡化你的程式碼：

<?code-excerpt "misc/bin/cheatsheet/nullable.dart (simple-null)"?>
```dart
int? a; // The initial value of a is null.
```

For more information about null safety in Dart,
read the [sound null safety guide](/null-safety).

想了解更多有關 Dart 的空安全的內容，請閱讀 [健全的空安全](/null-safety)。

### Code example
{: .no_toc}

### 程式碼範例

Try to declare two variables below:

試著定義以下兩種變數：

- A nullable `String` named `name` with the value `'Jane'`.

  一個可空的 `String`，名為 `name`，值為 `'Jane'`。

- A nullable `String` named `address` with the value `null`.

  一個可空的 `String`，名為 `address`，值為 `null`。

Ignore all initial errors in the DartPad.

可以忽略以下程式碼一開始在 DartPad 中的錯誤。

```dart:run-dartpad:ga_id-nullable_variables
{$ begin main.dart $}
// Declare the two variables here
{$ end main.dart $}
{$ begin solution.dart $}
String? name = 'Jane';
String? address;
{$ end solution.dart $}
{$ begin test.dart $}

void main() {
  try {
    if (name == 'Jane' && address == null) {
      // verify that "name" is nullable
      name = null;
      _result(true);
    } else {
      _result(false, ['That\'s not quite right. Keep trying!']);
    }
  } catch (e) {
    _result(false, ['Tried calling stringify(2, 3), but received an exception: ${e.runtimeType}']);
  }
}
{$ end test.dart $}
{$ begin hint.txt $}
Declare the two variables as "String" followed by "?".
Then, assign "Jane" to "name"
and leave "address" uninitialized.
{$ end hint.txt $}
```

## Null-aware operators

## 避空運算子

Dart offers some handy operators for dealing with values that might be null. One is the
`??=` assignment operator, which assigns a value to a variable only if that
variable is currently null:

Dart 提供了一系列方便的運算子用於處理可能會為空值的變數。
其中一個是 `??=` 賦值運算子，僅當該變數為空值時才為其賦值：

<?code-excerpt "misc/test/cheatsheet/null_aware_test.dart (null-aware-operators)"?>
```dart
int? a; // = null
a ??= 3;
print(a); // <-- Prints 3.

a ??= 5;
print(a); // <-- Still prints 3.
```

Another null-aware operator is `??`,
which returns the expression on its left unless
that expression's value is null,
in which case it evaluates and returns the expression on its right:

另外一個避空運算子是 `??`，如果該運算子左邊的表示式返回的是空值，
則會計算並返回右邊的表示式。

<?code-excerpt "misc/test/cheatsheet/null_aware_test.dart (null-aware-operators-2)"?>
```dart
print(1 ?? 3); // <-- Prints 1.
print(null ?? 12); // <-- Prints 12.
```

### Code example
{: .no_toc}

### 程式碼範例

Try substituting in the `??=` and `??` operators
to implement the described behavior in the following snippet.

嘗試在下面的程式碼片段中交替使用 `??=` 和 `??` 運運算元，實現期望的需求。

Ignore all initial errors in the DartPad.

可以忽略以下程式碼一開始在 DartPad 中的錯誤。

```dart:run-dartpad:height-255px:ga_id-null_aware
{$ begin main.dart $}
String? foo = 'a string';
String? bar; // = null

// Substitute an operator that makes 'a string' be assigned to baz.
String? baz = foo /* TODO */ bar;

void updateSomeVars() {
  // Substitute an operator that makes 'a string' be assigned to bar.
  bar /* TODO */ 'a string';
}
{$ end main.dart $}
{$ begin solution.dart $}
String? foo = 'a string';
String? bar; // = null

// Substitute an operator that makes 'a string' be assigned to baz.
String? baz = foo ?? bar;

void updateSomeVars() {
  // Substitute an operator that makes 'a string' be assigned to bar.
  bar ??= 'a string';
}
{$ end solution.dart $}
{$ begin test.dart $}
void main() {
  final errs = <String>[];
  
  try {
    updateSomeVars();
    
    if (foo != 'a string') {
      errs.add('Looks like foo somehow ended up with the wrong value.');
    } else if (bar != 'a string') {
      errs.add('Looks like bar ended up with the wrong value.');
    } else if (baz != 'a string') {
      errs.add('Looks like baz ended up with the wrong value.');
    }
  } catch (e) {
    errs.add('Tried calling updateSomeVars and received an exception: ${e.runtimeType}.');
  }
  
  if (errs.isEmpty) {
   _result(true);
  } else {
    _result(false, errs);
  }
}
{$ end test.dart $}
{$ begin hint.txt $}
All you need to do in this exercise is
replace the TODO comments with either ?? or ??=.
Read the codelab text to make sure you understand both,
and then give it a try.
{$ end hint.txt $}
```


## Conditional property access

## 條件屬性存取

To guard access to a property or method of an object that might be null,
put a question mark (`?`) before the dot (`.`):

要保護可能會為空的屬性的正常存取，請在點（`.`）之前加一個問號（`?`）。

<?code-excerpt "misc/test/cheatsheet/null_aware_test.dart (conditional-property-access)" replace="/result = //g; /;//g;"?>
```dart
myObject?.someProperty
```

The preceding code is equivalent to the following:

上述程式碼等效於以下內容：

<?code-excerpt "misc/test/cheatsheet/null_aware_test.dart (conditional-property-access-equivalent)" replace="/result = //g; /;//g;"?>
```dart
(myObject != null) ? myObject.someProperty : null
```

You can chain multiple uses of `?.` together in a single expression:

你可以在一個表示式中連續使用多個 `?.`：

<?code-excerpt "misc/test/cheatsheet/null_aware_test.dart (conditional-property-access-multiple)" replace="/result = //g; /;//g;"?>
```dart
myObject?.someProperty?.someMethod()
```

The preceding code returns null (and never calls `someMethod()`) if either
`myObject` or `myObject.someProperty` is
null.

如果 `myObject` 或 `myObject.someProperty` 為空，則前面的程式碼返回 null（並不再呼叫 `someMethod`）。

### Code example
{: .no_toc}

### 程式碼範例

Try using conditional property access to finish the code snippet below.

嘗試使用條件屬性存取來完成下面的程式碼片段。

```dart:run-dartpad:ga_id-conditional-property_access
{$ begin main.dart $}
// This method should return the uppercase version of `str`
// or null if `str` is null.
String? upperCaseIt(String? str) {
  // Try conditionally accessing the `toUpperCase` method here.
}
{$ end main.dart $}
{$ begin solution.dart $}
// This method should return the uppercase version of `str`
// or null if `str` is null.
String? upperCaseIt(String? str) {
  return str?.toUpperCase();
}
{$ end solution.dart $}
{$ begin test.dart $}
void main() {
  final errs = <String>[];
  
  try {
    String? one = upperCaseIt(null);

    if (one != null) {
      errs.add('Looks like you\'re not returning null for null inputs.');
    }
  } catch (e) {
    errs.add('Tried calling upperCaseIt(null) and got an exception: ${e.runtimeType}.');
  }
  
  try {
    String? two = upperCaseIt('asdf');

    if (two == null) {
      errs.add('Looks like you\'re returning null even when str has a value.');
    } else if (two != 'ASDF') {
      errs.add('Tried upperCaseIt(\'asdf\'), but didn\'t get \'ASDF\' in response.');
    }
  } catch (e) {
    errs.add('Tried calling upperCaseIt(\'asdf\') and got an exception: ${e.runtimeType}.');
  }
  
  if (errs.isEmpty) {
   _result(true);
  } else {
   _result(false, errs);
  }  
}
{$ end test.dart $}
{$ begin hint.txt $}
If this exercise wanted you to conditionally lowercase a string,
you could do it like this: str?.toLowerCase()
{$ end hint.txt $}
```

## Collection literals

## 集合字面量 (Collection literals) 

Dart has built-in support for lists, maps, and sets.
You can create them using literals:

Dart 內建了對 list、map 以及 set 的支援。
你可以透過字面量直接建立它們：

<?code-excerpt "misc/test/cheatsheet/collections_test.dart (collection-literals-inferred)"?>
```dart
final aListOfStrings = ['one', 'two', 'three'];
final aSetOfStrings = {'one', 'two', 'three'};
final aMapOfStringsToInts = {
  'one': 1,
  'two': 2,
  'three': 3,
};
```

Dart's type inference can assign types to these variables for you.
In this case, the inferred types are `List<String>`,
`Set<String>`, and `Map<String, int>`.

Dart 的型別推斷可以自動幫你分配這些變數的型別。在這個例子中，推斷型別是 `List<String>`、`Set<String>`和 `Map<String, int>`。

Or you can specify the type yourself:

你也可以手動指定型別：

<?code-excerpt "misc/test/cheatsheet/collections_test.dart (collection-literals-specified)"?>
```dart
final aListOfInts = <int>[];
final aSetOfInts = <int>{};
final aMapOfIntToDouble = <int, double>{};
```

Specifying types is handy when you initialize a list with contents of a subtype,
but still want the list to be `List<BaseType>`:

在使用子類別型的內容初始化列表，但仍希望列表為 `List <BaseType>` 時，指定其型別很方便：

<?code-excerpt "misc/test/cheatsheet/collections_test.dart (collection-literals-subtypes)"?>
```dart
final aListOfBaseType = <BaseType>[SubType(), SubType()];
```

### Code example
{: .no_toc}

### 程式碼範例

Try setting the following variables to the indicated values. Replace the existing null values.

嘗試將以下變數設定為指定的值。替換當前的 null 值。

```dart:run-dartpad:height-400px:ga_id-collection_literals
{$ begin main.dart $}
// Assign this a list containing 'a', 'b', and 'c' in that order:
final aListOfStrings = null;

// Assign this a set containing 3, 4, and 5:
final aSetOfInts = null;

// Assign this a map of String to int so that aMapOfStringsToInts['myKey'] returns 12:
final aMapOfStringsToInts = null;

// Assign this an empty List<double>:
final anEmptyListOfDouble = null;

// Assign this an empty Set<String>:
final anEmptySetOfString = null;

// Assign this an empty Map of double to int:
final anEmptyMapOfDoublesToInts = null;
{$ end main.dart $}
{$ begin solution.dart $}
// Assign this a list containing 'a', 'b', and 'c' in that order:
final aListOfStrings = ['a', 'b', 'c'];

// Assign this a set containing 3, 4, and 5:
final aSetOfInts = {3, 4, 5};

// Assign this a map of String to int so that aMapOfStringsToInts['myKey'] returns 12:
final aMapOfStringsToInts = {'myKey': 12};

// Assign this an empty List<double>:
final anEmptyListOfDouble = <double>[];

// Assign this an empty Set<String>:
final anEmptySetOfString = <String>{};

// Assign this an empty Map of double to int:
final anEmptyMapOfDoublesToInts = <double, int>{};
{$ end solution.dart $}
{$ begin test.dart $}
void main() {
  final errs = <String>[];
  
  if (aListOfStrings is! List<String>) {
    errs.add('aListOfStrings should have the type List<String>.');
  } else if (aListOfStrings.length != 3) {
    errs.add('aListOfStrings has ${aListOfStrings.length} items in it, rather than the expected 3.');
  } else if (aListOfStrings[0] != 'a' || aListOfStrings[1] != 'b' || aListOfStrings[2] != 'c') {
    errs.add('aListOfStrings doesn\'t contain the correct values (\'a\', \'b\', \'c\').');
  }

  if (aSetOfInts is! Set<int>) {
    errs.add('aSetOfInts should have the type Set<int>.');
  } else if (aSetOfInts.length != 3) {
    errs.add('aSetOfInts has ${aSetOfInts.length} items in it, rather than the expected 3.');
  } else if (!aSetOfInts.contains(3) || !aSetOfInts.contains(4) || !aSetOfInts.contains(5)) {
    errs.add('aSetOfInts doesn\'t contain the correct values (3, 4, 5).');
  }

  if (aMapOfStringsToInts is! Map<String, int>) {
    errs.add('aMapOfStringsToInts should have the type Map<String, int>.');
  } else if (aMapOfStringsToInts['myKey'] != 12) {
    errs.add('aMapOfStringsToInts doesn\'t contain the correct values (\'myKey\': 12).');
  }

  if (anEmptyListOfDouble is! List<double>) {
    errs.add('anEmptyListOfDouble should have the type List<double>.');
  } else if (anEmptyListOfDouble.isNotEmpty) {
    errs.add('anEmptyListOfDouble should be empty.');
  }

  if (anEmptySetOfString is! Set<String>) {
    errs.add('anEmptySetOfString should have the type Set<String>.');
  } else if (anEmptySetOfString.isNotEmpty) {
    errs.add('anEmptySetOfString should be empty.');
  }

  if (anEmptyMapOfDoublesToInts is! Map<double, int>) {
    errs.add('anEmptyMapOfDoublesToInts should have the type Map<double, int>.');
  } else if (anEmptyMapOfDoublesToInts.isNotEmpty) {
    errs.add('anEmptyMapOfDoublesToInts should be empty.');
  }

  if (errs.isEmpty) {
    _result(true);
  } else {
    _result(false, errs);
  }
}
{$ end test.dart $}
{$ begin hint.txt $}
This exercise is fairly straightforward.
Just add a list, set, or map literal after each equals sign.
See the codelab text for the correct syntax to use.
{$ end hint.txt $}
```

## Arrow syntax

## 箭頭語法

You might have seen the `=>` symbol in Dart code.
This arrow syntax is a way to define a function that executes the
expression to its right and returns its value.

你也許已經在 Dart 程式碼中見到過 `=>` 符號。這種箭頭語法是一種定義函式的方法，
該函式將在其右側執行表示式並返回其值。

For example, consider this call to the `List` class's
`any()` method:

例如，考慮呼叫這個 `List` 類中的 `any` 方法：

<?code-excerpt "misc/test/cheatsheet/arrow_functions_test.dart (has-empty-long)"?>
```dart
bool hasEmpty = aListOfStrings.any((s) {
  return s.isEmpty;
});
```

Here’s a simpler way to write that code:

這裡是一個更簡單的程式碼實現：

<?code-excerpt "misc/test/cheatsheet/arrow_functions_test.dart (has-empty-short)"?>
```dart
bool hasEmpty = aListOfStrings.any((s) => s.isEmpty);
```

### Code example
{: .no_toc}

### 程式碼範例

Try finishing the following statements, which use arrow syntax.

嘗試使用箭頭語法完成下面陳述式：

```dart:run-dartpad:height-345px:ga_id-arrow_syntax
{$ begin main.dart $}
class MyClass {
  int value1 = 2;
  int value2 = 3;
  int value3 = 5;
  
  // Returns the product of the above values:
  int get product => TODO();
  
  // Adds 1 to value1:
  void incrementValue1() => TODO();
  
  // Returns a string containing each item in the
  // list, separated by commas (e.g. 'a,b,c'): 
  String joinWithCommas(List<String> strings) => TODO();
}
{$ end main.dart $}
{$ begin solution.dart $}
class MyClass {
  int value1 = 2;
  int value2 = 3;
  int value3 = 5;

  // Returns the product of the above values:
  int get product => value1 * value2 * value3;
  
  // Adds 1 to value1:
  void incrementValue1() => value1++; 
  
  // Returns a string containing each item in the
  // list, separated by commas (e.g. 'a,b,c'): 
  String joinWithCommas(List<String> strings) => strings.join(',');
}
{$ end solution.dart $}
{$ begin test.dart $}
void main() {
  final obj = MyClass();
  final errs = <String>[];
  
  try {
    final product = obj.product;
    
    if (product != 30) {
      errs.add('The product property returned $product instead of the expected value (30).'); 
    } 
  } on UnimplementedError {
    _result(false, ['Tried to use MyClass.product but failed. Did you implement the method?']);
    return;
  } catch (e) {
    _result(false, ['Tried to use MyClass.product, but encountered an exception: ${e.runtimeType}.']);
    return;
  }

  try {
    obj.incrementValue1();
    
    if (obj.value1 != 3) {
      errs.add('After calling incrementValue, value1 was ${obj.value1} instead of the expected value (3).'); 
    } 
  } on UnimplementedError {
    _result(false, ['Tried to use MyClass.incrementValue1 but failed. Did you implement the method?']);
    return;
  } catch (e) {
    _result(false, ['Tried to use MyClass.incrementValue1, but encountered an exception: ${e.runtimeType}.']);
    return;
  }

  try {
    final joined = obj.joinWithCommas(['one', 'two', 'three']);
    
    if (joined != 'one,two,three') {
      errs.add('Tried calling joinWithCommas([\'one\', \'two\', \'three\']) and received $joined instead of the expected value (\'one,two,three\').'); 
    } 
  } on UnimplementedError {
    _result(false, ['Tried to use MyClass.joinWithCommas but failed. Did you implement the method?']);
    return;
  } catch (e) {
    _result(false, ['Tried to use MyClass.joinWithCommas, but encountered an exception: ${e.runtimeType}.']);
    return;
  }

  if (errs.isEmpty) {
    _result(true);
  } else {
    _result(false, errs);
  }
}
{$ end test.dart $}
{$ begin hint.txt $}
For the product, you can just multiply the three values together.
For incrementValue1, you can use the increment operator (++).
For joinWithCommas, try using the join method found in the List class.
{$ end hint.txt $}
```

## Cascades

## 級聯

To perform a sequence of operations on the same object, use cascades (`..`).
We've all seen an expression like this:

要對同一物件執行一系列操作，請使用級聯（`..`）。我們都看到過這樣的表示式：

<?code-excerpt "misc/bin/cheatsheet/cascades.dart (no-cascade)" replace="/;//g"?>
```dart
myObject.someMethod()
```

It invokes `someMethod()` on `myObject`, and the result of
the expression is the return value of `someMethod()`.

它在 `myObject` 上呼叫 `someMethod` 方法，而表示式的結果是 `someMethod` 的返回值。

Here's the same expression with a cascade:

下面是一個使用級連語法的相同表示式：

<?code-excerpt "misc/bin/cheatsheet/cascades.dart (uses-cascade)" replace="/;//g"?>
```dart
myObject..someMethod()
```

Although it still invokes `someMethod()` on `myObject`, the result
of the expression **isn't** the return value—it's a reference to `myObject`!

Using cascades, you can chain together operations that
would otherwise require separate statements.
For example, consider the following code,
which uses the conditional member access operator (`?.`)
to read properties of `button` if it isn't `null`:

雖然它仍然在 `myObject` 上呼叫了 `someMethod`，
但表示式的結果卻**不是**該方法返回值，
而是是 `myObject` 物件的參考！
使用級聯，你可以將需要單獨操作的陳述式連結在一起。
例如，下方的程式碼使用了空判斷呼叫符 (`?.`) 在 `button` 不為 `null` 時獲取屬性：

<?code-excerpt "misc/bin/cheatsheet/cascades.dart (query-without-cascades)"?>
```dart
var button = querySelector('#confirm');
button?.text = 'Confirm';
button?.classes.add('important');
button?.onClick.listen((e) => window.alert('Confirmed!'));
button?.scrollIntoView();
```

To instead use cascades, 
you can start with the _null-shorting_ cascade (`?..`), 
which guarantees that none of the cascade operations
are attempted on a `null` object.
Using cascades shortens the code
and makes the `button` variable unnecessary:

現在你可以在第一個級聯位置，使用 **空判斷** 級聯運運算元 (`?..`)，
它可以確保級聯操作均在例項不為 `null` 時執行。
使用空判斷級聯後，你也不再需要 `button` 變量了：

<?code-excerpt "misc/bin/cheatsheet/cascades.dart (query-with-cascades)"?>
```dart
querySelector('#confirm')
  ?..text = 'Confirm'
  ..classes.add('important')
  ..onClick.listen((e) => window.alert('Confirmed!'))
  ..scrollIntoView();
```

### Code example
{: .no_toc}

### 程式碼範例

Use cascades to create a single statement that
sets the `anInt`, `aString`, and `aList` properties of a `BigObject`
to `1`, `'String!'`, and `[3.0]` (respectively)
and then calls `allDone()`.

使用級聯建立一個陳述式，
分別將 `BigObject` 的 `anInt` 屬性設為 `1`、`aString` 
屬性設為 `String!`、`aList` 屬性設定為
`[3.0]` 然後呼叫 `allDone()`。

```dart:run-dartpad:height-345px:ga_id-cascades
{$ begin main.dart $}
class BigObject {
  int anInt = 0;
  String aString = '';
  List<double> aList = [];
  bool _done = false;
  
  void allDone() {
    _done = true;
  }
}

BigObject fillBigObject(BigObject obj) {
  // Create a single statement that will update and return obj:
  return TODO('obj..');
}
{$ end main.dart $}
{$ begin solution.dart $}
class BigObject {
  int anInt = 0;
  String aString = '';
  List<double> aList = [];
  bool _done = false;
  
  void allDone() {
    _done = true;
  }
}

BigObject fillBigObject(BigObject obj) {
  return obj
    ..anInt = 1
    ..aString = 'String!'
    ..aList.add(3)
    ..allDone();
}
{$ end solution.dart $}
{$ begin test.dart $}
void main() {
  BigObject obj;

  try {
    obj = fillBigObject(BigObject());
  } on UnimplementedError {
    _result(false, ['Tried to call fillBigObject but failed. Did you implement the method?']);
    return;
  } catch (e) {
    _result(false, [
      'Caught an exception of type ${e.runtimeType} while running fillBigObject'
    ]);
    return;
  }

  final errs = <String>[];

  if (obj.anInt != 1) {
    errs.add(
        'The value of anInt was ${obj.anInt} rather than the expected (1).');
  }

  if (obj.aString != 'String!') {
    errs.add(
        'The value of aString was \'${obj.aString}\' rather than the expected (\'String!\').');
  }

  if (obj.aList.length != 1) {
    errs.add(
        'The length of aList was ${obj.aList.length} rather than the expected value (1).');
  } else {
    if (obj.aList[0] != 3.0) {
      errs.add(
          'The value found in aList was ${obj.aList[0]} rather than the expected (3.0).');
    }
  }
  
  if (!obj._done) {
    errs.add('It looks like allDone() wasn\'t called.');
  }

  if (errs.isEmpty) {
    _result(true);
  } else {
    _result(false, errs);
  }
}
{$ end test.dart $}
{$ begin hint.txt $}
The best solution for this exercise starts with obj.. and
has four assignment operations chained together.
Try starting with `return obj..anInt = 1`,
then add another cascade (..) and start the next assignment.
{$ end hint.txt $}
```


## Getters and setters

You can define getters and setters
whenever you need more control over a property
than a simple field allows.

任何需要對屬性進行更多控制而不是允許簡單欄位存取的時候，你都可以自訂 getter 和 setter。

For example, you can make sure a property's value is valid:

例如，你可以用來確保屬性值合法：

<?code-excerpt "misc/lib/cheatsheet/getters_setters.dart"?>
```dart
class MyClass {
  int _aProperty = 0;

  int get aProperty => _aProperty;

  set aProperty(int value) {
    if (value >= 0) {
      _aProperty = value;
    }
  }
}
```

You can also use a getter to define a computed property:

你還可以使用 getter 來定義計算屬性：

<?code-excerpt "misc/lib/cheatsheet/getter_compute.dart"?>
```dart
class MyClass {
  final List<int> _values = [];

  void addValue(int value) {
    _values.add(value);
  }

  // A computed property.
  int get count {
    return _values.length;
  }
}
```

### Code example
{: .no_toc}

### 程式碼範例

Imagine you have a shopping cart class that keeps a private `List<double>`
of prices.
Add the following:

想象你有一個購物車類，其中有一個私有的 `List<double>` 型別的 prices 屬性。新增以下內容：

* A getter called `total` that returns the sum of the prices

  一個名為 `total` 的 getter，用於返回總價格。

* A setter that replaces the list with a new one,
  as long as the new list doesn't contain any negative prices
  (in which case the setter should throw an `InvalidPriceException`).

  只要新列表不包含任何負價格，
  setter 就會用新的列表替換列表
  （在這種情況下，setter 應該丟擲 `InvalidPriceException`）。

Ignore all initial errors in the DartPad.

可以忽略以下程式碼一開始在 DartPad 中的錯誤。

```dart:run-dartpad:height-240px:ga_id-getters_setters
{$ begin main.dart $}
class InvalidPriceException {}

class ShoppingCart {
  List<double> _prices = [];
  
  // Add a "total" getter here:

  // Add a "prices" setter here:
}
{$ end main.dart $}
{$ begin solution.dart $}
class InvalidPriceException {}

class ShoppingCart {
  List<double> _prices = [];
  
  double get total => _prices.fold(0, (e, t) => e + t);
  
  set prices(List<double> value) {
    if (value.any((p) => p < 0)) {
      throw InvalidPriceException();
    }
    
    _prices = value;
  }
}
{$ end solution.dart $}
{$ begin test.dart $}
void main() {
  var foundException = false;
  
  try {
    final cart = ShoppingCart();
    cart.prices = [12.0, 12.0, -23.0];
  } on InvalidPriceException {
    foundException = true;
  } catch (e) {
    _result(false, ['Tried setting a negative price and received a ${e.runtimeType} instead of an InvalidPriceException.']);
    return;
  }
  
  if (!foundException) {
    _result(false, ['Tried setting a negative price and didn\'t get an InvalidPriceException.']);
    return;
  }
  
  final secondCart = ShoppingCart();
  
  try {
    secondCart.prices = [1.0, 2.0, 3.0];
  } catch(e) {
    _result(false, ['Tried setting prices with a valid list, but received an exception: ${e.runtimeType}.']);
    return;
  }
  
  if (secondCart._prices.length != 3) {
    _result(false, ['Tried setting prices with a list of three values, but _prices ended up having length ${secondCart._prices.length}.']);
    return;
  }

  if (secondCart._prices[0] != 1.0 || secondCart._prices[1] != 2.0 || secondCart._prices[2] != 3.0) {
    final vals = secondCart._prices.map((p) => p.toString()).join(', ');
    _result(false, ['Tried setting prices with a list of three values (1, 2, 3), but incorrect ones ended up in the price list ($vals) .']);
    return;
  }
  
  var sum = 0.0;
  
  try {
    sum = secondCart.total;
  } catch (e) {
    _result(false, ['Tried to get total, but received an exception: ${e.runtimeType}.']);
    return;
  }
  
  if (sum != 6.0) {
    _result(false, ['After setting prices to (1, 2, 3), total returned $sum instead of 6.']);
    return;
  }
  
  _result(true);
}
{$ end test.dart $}
{$ begin hint.txt $}
Two functions are handy for this exercise. 
One is `fold`, which can reduce a list to a single value
(try it to calculate the total).
The other is `any`, which can check each item in a list
with a function you give it
(try using it to check if there are any negative prices in the prices setter).
{$ end hint.txt $}
```


## Optional positional parameters

## 可選位置引數

Dart has two kinds of function parameters: positional and named. 
Positional parameters are the kind you're likely familiar with:

Dart 有兩種傳參方法：位置引數和命名引數。位置引數你可能會比較熟悉：

<?code-excerpt "misc/lib/cheatsheet/optional_positional_args.dart (optional-positional-args)"?>
```dart
int sumUp(int a, int b, int c) {
  return a + b + c;
}
// ···
  int total = sumUp(1, 2, 3);
```

With Dart, you can make these positional parameters optional by wrapping them in brackets:

在 Dart 裡，你可以將這些引數包裹在方括號中，使其變成可選位置引數：

<?code-excerpt "misc/lib/cheatsheet/optional_positional_args.dart (optional-positional-args-2)" replace="/total2/total/g"?>
```dart
int sumUpToFive(int a, [int? b, int? c, int? d, int? e]) {
  int sum = a;
  if (b != null) sum += b;
  if (c != null) sum += c;
  if (d != null) sum += d;
  if (e != null) sum += e;
  return sum;
}
// ···
  int total = sumUpToFive(1, 2);
  int otherTotal = sumUpToFive(1, 2, 3, 4, 5);
```

Optional positional parameters are always last
in a function's parameter list.
Their default value is null unless you provide another default value:

可選位置引數永遠放在方法引數列表的最後。
除非你給它們提供一個預設值，否則預設為 null:

<?code-excerpt "misc/lib/cheatsheet/optional_positional_args2.dart"?>
```dart
int sumUpToFive(int a, [int b = 2, int c = 3, int d = 4, int e = 5]) {
// ···
}
// ···
  int newTotal = sumUpToFive(1);
  print(newTotal); // <-- prints 15
```

### Code example
{: .no_toc}

### 程式碼範例

Implement a function called `joinWithCommas()` that accepts one to
five integers, then returns a string of those numbers separated by commas.
Here are some examples of function calls and returned values:

實現一個名為 `joinWithCommas` 的方法，它接收一至五個整數，
然後返回由逗號分隔的包含這些數字的字串。
以下是方法呼叫和返回值的一些範例：

| <t>Function call</t><t>方法呼叫</t> | | <t>Returned value</t><t>返回值</t> |
|---------------------------------+-+----------------|
| `joinWithCommas(1)`             | | `'1'`          |
| `joinWithCommas(1, 2, 3)`       | | `'1,2,3'`      |
| `joinWithCommas(1, 1, 1, 1, 1)` | | `'1,1,1,1,1'`  |

<br>

```dart:run-dartpad:ga_id-optional_positional_parameters
{$ begin main.dart $}
String joinWithCommas(int a, [int? b, int? c, int? d, int? e]) {
  return TODO();
}
{$ end main.dart $}
{$ begin solution.dart $}
String joinWithCommas(int a, [int? b, int? c, int? d, int? e]) {
  var total = '$a';
  if (b != null) total = '$total,$b';
  if (c != null) total = '$total,$c';
  if (d != null) total = '$total,$d';
  if (e != null) total = '$total,$e';
  return total;
}
{$ end solution.dart $}
{$ begin test.dart $}
void main() {
  final errs = <String>[];
  
  try {
    final value = joinWithCommas(1);
    
    if (value != '1') {
      errs.add('Tried calling joinWithCommas(1) and got $value instead of the expected (\'1\').'); 
    } 
  } on UnimplementedError {
    _result(false, ['Tried to call joinWithCommas but failed. Did you implement the method?']);
    return;
  } catch (e) {
    _result(false, ['Tried calling joinWithCommas(1), but encountered an exception: ${e.runtimeType}.']);
    return;
  }

  try {
    final value = joinWithCommas(1, 2, 3);
    
    if (value != '1,2,3') {
      errs.add('Tried calling joinWithCommas(1, 2, 3) and got $value instead of the expected (\'1,2,3\').'); 
    } 
  } on UnimplementedError {
    _result(false, ['Tried to call joinWithCommas but failed. Did you implement the method?']);
    return;
  } catch (e) {
    _result(false, ['Tried calling joinWithCommas(1, 2 ,3), but encountered an exception: ${e.runtimeType}.']);
    return;
  }

  try {
    final value = joinWithCommas(1, 2, 3, 4, 5);
    
    if (value != '1,2,3,4,5') {
      errs.add('Tried calling joinWithCommas(1, 2, 3, 4, 5) and got $value instead of the expected (\'1,2,3,4,5\').'); 
    } 
  } on UnimplementedError {
    _result(false, ['Tried to call joinWithCommas but failed. Did you implement the method?']);
    return;
  } catch (e) {
    _result(false, ['Tried calling stringify(1, 2, 3, 4 ,5), but encountered an exception: ${e.runtimeType}.']);
    return;
  }

  if (errs.isEmpty) {
    _result(true);
  } else {
    _result(false, errs);
  }
}
{$ end test.dart $}
{$ begin hint.txt $}
The b, c, d, and e parameters are null if they aren't provided by caller.
The important thing, then, is to check whether those arguments are null
before you add them to the final string.
{$ end hint.txt $}
```

<a id="optional-named-parameters"></a>
## Named parameters

## 命名引數

Using a curly brace syntax at the end of the parameter list,
you can define parameters that have names.

你可以在引數列表的靠後位置使用花括號 (`{}`) 來定義命名引數。

Named parameters are optional
unless they're explicitly marked as `required`.

除非顯式使用 `required` 進行標記，否則命名引數預設是可選的。

<?code-excerpt "misc/lib/cheatsheet/named_parameters.dart"?>
```dart
void printName(String firstName, String lastName, {String? middleName}) {
  print('$firstName ${middleName ?? ''} $lastName');
}
// ···
  printName('Dash', 'Dartisan');
  printName('John', 'Smith', middleName: 'Who');
  // Named arguments can be placed anywhere in the argument list
  printName('John', middleName: 'Who', 'Smith');
```

As you might expect,
the default value of a nullable named parameter is `null`,
but you can provide a custom default value.

正如你所料，這些引數預設為 null，但你也可以為其提供預設值。

If the type of a parameter is non-nullable,
then you must either provide a default value
(as shown in the following code)
or mark the parameter as `required`
(as shown in the
[constructor section](#using-this-in-a-constructor)).

如果一個引數的型別是非空的，那麼你必須要提供一個預設值（如下方程式碼所示），
或者將其標記為 `required`（如 [構造部分](#using-this-in-a-constructor)所示）。

<?code-excerpt "misc/test/cheatsheet/arguments_test.dart (defaulted-middle)" replace="/ = ''/[! = ''!]/g;"?>
```dart
void printName(String firstName, String lastName, {String middleName[! = ''!]}) {
  print('$firstName $middleName $lastName');
}
```

A function can't have both optional positional and named parameters.

一個方法不能同時使用可選位置引數和可選命名引數。

### Code example
{: .no_toc}

### 程式碼範例

Add a `copyWith()` instance method to the `MyDataObject`
class. It should take three named, nullable parameters:

向 `MyDataObject` 類新增一個 `copyWith()` 例項方法，
它應該包含三個可空的命名引數。

* `int? newInt`
* `String? newString`
* `double? newDouble`

Your `copyWith()` method should return a new `MyDataObject`
based on the current instance,
with data from the preceding parameters (if any)
copied into the object's properties.
For example, if `newInt` is non-null,
then copy its value into `anInt`.

`copyWith` 方法應該根據當前例項返回一個新的
`MyDataObject` 並將前面引數（如果有的話）的資料複製到物件的屬性中。
例如，如果 `newInt` 不為空，則將其值複製到 `anInt` 中。

Ignore all initial errors in the DartPad.

可以忽略以下程式碼一開始在 DartPad 中的錯誤。

```dart:run-dartpad:height-310px:ga_id-optional_named_parameters
{$ begin main.dart $}
class MyDataObject {
  final int anInt;
  final String aString;
  final double aDouble;

  MyDataObject({
     this.anInt = 1,
     this.aString = 'Old!',
     this.aDouble = 2.0,
  });

  // Add your copyWith method here:
}
{$ end main.dart $}
{$ begin solution.dart $}
class MyDataObject {
  final int anInt;
  final String aString;
  final double aDouble;

  MyDataObject({
     this.anInt = 1,
     this.aString = 'Old!',
     this.aDouble = 2.0,
  });

  MyDataObject copyWith({int? newInt, String? newString, double? newDouble}) {
    return MyDataObject(
      anInt: newInt ?? this.anInt,
      aString: newString ?? this.aString,
      aDouble: newDouble ?? this.aDouble,
    );
  }
}
{$ end solution.dart $}
{$ begin test.dart $}
void main() {
  final source = MyDataObject();
  final errs = <String>[];
  
  try {
    final copy = source.copyWith(newInt: 12, newString: 'New!', newDouble: 3.0);
    
    if (copy.anInt != 12) {
      errs.add('Called copyWith(newInt: 12, newString: \'New!\', newDouble: 3.0), and the new object\'s anInt was ${copy.anInt} rather than the expected value (12).');
    }
    
    if (copy.aString != 'New!') {
      errs.add('Called copyWith(newInt: 12, newString: \'New!\', newDouble: 3.0), and the new object\'s aString was ${copy.aString} rather than the expected value (\'New!\').');
    }
    
    if (copy.aDouble != 3) {
      errs.add('Called copyWith(newInt: 12, newString: \'New!\', newDouble: 3.0), and the new object\'s aDouble was ${copy.aDouble} rather than the expected value (3).');
    }
  } catch (e) {
    _result(false, ['Called copyWith(newInt: 12, newString: \'New!\', newDouble: 3.0) and got an exception: ${e.runtimeType}']);
  }
  
  try {
    final copy = source.copyWith();
    
    if (copy.anInt != 1) {
      errs.add('Called copyWith(), and the new object\'s anInt was ${copy.anInt} rather than the expected value (1).');
    }
    
    if (copy.aString != 'Old!') {
      errs.add('Called copyWith(), and the new object\'s aString was ${copy.aString} rather than the expected value (\'Old!\').');
    }
    
    if (copy.aDouble != 2) {
      errs.add('Called copyWith(), and the new object\'s aDouble was ${copy.aDouble} rather than the expected value (2).');
    }
  } catch (e) {
    _result(false, ['Called copyWith() and got an exception: ${e.runtimeType}']);
  }
  
  if (errs.isEmpty) {
    _result(true);
  } else {
    _result(false, errs);
  }
}
{$ end test.dart $}
{$ begin hint.txt $}
The copyWith method shows up in a lot of classes and libraries.
Yours should do a few things:
use optional named parameters,
create a new instance of MyDataObject,
and use the data from the parameters to fill it
(or the data from the current instance if the parameters are null).
This is a chance to get more practice with the ?? operator!
{$ end hint.txt $}
```


## Exceptions

## 例外

Dart code can throw and catch exceptions. In contrast to Java, all of Dart’s exceptions are unchecked
exceptions. Methods don't declare which exceptions they might throw, and you aren't required to catch
any exceptions.

Dart 程式碼可以丟擲和捕獲例外。與 Java 相比，Dart 的所有例外都是 unchecked exception。方法不會宣告它們可能丟擲的例外，你也不需要捕獲任何例外。

Dart provides `Exception` and `Error` types, but you're
allowed to throw any non-null object:

雖然 Dart 提供了 Exception 和 Error 型別，但是你可以丟擲任何非空物件：

<?code-excerpt "misc/test/cheatsheet/exceptions_test.dart (simple-throws)"?>
```dart
throw Exception('Something bad happened.');
throw 'Waaaaaaah!';
```

Use the `try`, `on`, and `catch` keywords when handling exceptions:

使用 `try`、`on` 以及 `catch` 關鍵字來處理例外： 

<?code-excerpt "misc/test/cheatsheet/exceptions_test.dart (try-on-catch)"?>
```dart
try {
  breedMoreLlamas();
} on OutOfLlamasException {
  // A specific exception
  buyMoreLlamas();
} on Exception catch (e) {
  // Anything else that is an exception
  print('Unknown exception: $e');
} catch (e) {
  // No specified type, handles all
  print('Something really unknown: $e');
}
```

The `try` keyword works as it does in most other languages.
Use the `on` keyword to filter for specific exceptions by type,
and the `catch` keyword to get a reference to the exception object.

If you can't completely handle the exception, use the `rethrow` keyword
to propagate the exception:

如果你無法完全處理該例外，請使用 `rethrow` 關鍵字再次丟擲例外：

<?code-excerpt "misc/test/cheatsheet/exceptions_test.dart (try-catch)"?>
```dart
try {
  breedMoreLlamas();
} catch (e) {
  print('I was just trying to breed llamas!');
  rethrow;
}
```

To execute code whether or not an exception is thrown,
use `finally`:

要執行一段無論是否丟擲例外都會執行的程式碼，請使用 `finally`：

<?code-excerpt "misc/test/cheatsheet/exceptions_test.dart (try-catch-finally)"?>
```dart
try {
  breedMoreLlamas();
} catch (e) {
  // ... handle exception ...
} finally {
  // Always clean up, even if an exception is thrown.
  cleanLlamaStalls();
}
```

### Code example
{: .no_toc}

### 程式碼範例

Implement `tryFunction()` below. It should execute an untrustworthy method and
then do the following:

在下面實現 `tryFunction()` 方法。它應該會執行一個不可靠的方法，然後做以下操作：

* If `untrustworthy()` throws an `ExceptionWithMessage`,
  call `logger.logException` with the exception type and message
  (try using `on` and `catch`).

  如果 `untrustworthy()` 丟擲了 `ExceptionWithMessage`，則呼叫 `logger.logException` 並傳入使用例外型別和訊息（嘗試使用 `on` 和 `catch`）。

* If `untrustworthy()` throws an `Exception`,
  call `logger.logException` with the exception type
  (try using `on` for this one).

  如果 `untrustworthy()` 丟擲了一個 `Exception`，則呼叫 `logger.logException` 並傳入使用例外型別（這次請嘗試使用 `on`）。

* If `untrustworthy()` throws any other object, don't catch the exception.

  如果 `untrustworthy()` 丟擲了其他物件，請不要捕獲該例外。

* After everything's caught and handled, call `logger.doneLogging`
  (try using `finally`).

  捕獲並處理完所有內容後，
  呼叫 `logger.doneLogging`（嘗試使用 `finally`）。

```dart:run-dartpad:height-420px:ga_id-exceptions
{$ begin main.dart $}
typedef VoidFunction = void Function();

class ExceptionWithMessage {
  final String message;
  const ExceptionWithMessage(this.message);
}

// Call logException to log an exception, and doneLogging when finished.
abstract class Logger {
  void logException(Type t, [String? msg]);
  void doneLogging();
}

void tryFunction(VoidFunction untrustworthy, Logger logger) {
  // Invoking this method might cause an exception. Catch and handle
  // them using try-on-catch-finally.
  untrustworthy();
}
{$ end main.dart $}
{$ begin solution.dart $}
typedef VoidFunction = void Function();

class ExceptionWithMessage {
  final String message;
  const ExceptionWithMessage(this.message);
}

abstract class Logger {
  void logException(Type t, [String? msg]);
  void doneLogging();
}

void tryFunction(VoidFunction untrustworthy, Logger logger) {
  try {
    untrustworthy();
  } on ExceptionWithMessage catch (e) {
    logger.logException(e.runtimeType, e.message);
  } on Exception {
    logger.logException(Exception);
  } finally {
    logger.doneLogging();
  }
}
{$ end solution.dart $}
{$ begin test.dart $}
class MyLogger extends Logger {
  Type? lastType;
  String lastMessage = '';
  bool done = false;
  
  void logException(Type t, [String? message]) {
    lastType = t;
    lastMessage = message ?? lastMessage;
  }
  
  void doneLogging() => done = true;  
}

void main() {
  final errs = <String>[];
  var logger = MyLogger();
  
  try {
    tryFunction(() => throw Exception(), logger);
  
    if ('${logger.lastType}' != 'Exception' && '${logger.lastType}' != '_Exception') {
      errs.add('Untrustworthy threw an Exception, but a different type was logged: ${logger.lastType}.');
    }
    
    if (logger.lastMessage != '') {
      errs.add('Untrustworthy threw an Exception with no message, but a message was logged anyway: \'${logger.lastMessage}\'.');
    }
    
    if (!logger.done) {
      errs.add('Untrustworthy threw an Exception, and doneLogging() wasn\'t called afterward.');
    }
  } catch (e) {
    _result(false, ['Untrustworthy threw an exception, and an exception of type ${e.runtimeType} was unhandled by tryFunction.']);
  }
  
  logger = MyLogger();
  
  try {
    tryFunction(() => throw ExceptionWithMessage('Hey!'), logger);
  
    if (logger.lastType != ExceptionWithMessage) {
      errs.add('Untrustworthy threw an ExceptionWithMessage(\'Hey!\'), but a different type was logged: ${logger.lastType}.');
    }
    
    if (logger.lastMessage != 'Hey!') {
      errs.add('Untrustworthy threw an ExceptionWithMessage(\'Hey!\'), but a different message was logged: \'${logger.lastMessage}\'.');
    }
    
    if (!logger.done) {
      errs.add('Untrustworthy threw an ExceptionWithMessage(\'Hey!\'), and doneLogging() wasn\'t called afterward.');
    }
  } catch (e) {
    _result(false, ['Untrustworthy threw an ExceptionWithMessage(\'Hey!\'), and an exception of type ${e.runtimeType} was unhandled by tryFunction.']);
  }
  
  logger = MyLogger();
  bool caughtStringException = false;

  try {
    tryFunction(() => throw 'A String', logger);
  } on String {
    caughtStringException = true;
  }

  if (!caughtStringException) {
    errs.add('Untrustworthy threw a string, and it was incorrectly handled inside tryFunction().');
  }
  
  logger = MyLogger();
  
  try {
    tryFunction(() {}, logger);
  
    if (logger.lastType != null) {
      errs.add('Untrustworthy didn\'t throw an Exception, but one was logged anyway: ${logger.lastType}.');
    }
    
    if (logger.lastMessage != '') {
      errs.add('Untrustworthy didn\'t throw an Exception with no message, but a message was logged anyway: \'${logger.lastMessage}\'.');
    }
    
    if (!logger.done) {
      errs.add('Untrustworthy didn\'t throw an Exception, but doneLogging() wasn\'t called afterward.');
    }
  } catch (e) {
    _result(false, ['Untrustworthy didn\'t throw an exception, but an exception of type ${e.runtimeType} was unhandled by tryFunction anyway.']);
  }
  
  if (errs.isEmpty) {
    _result(true);
  } else {
    _result(false, errs);
  }
}
{$ end test.dart $}
{$ begin hint.txt $}
This exercise looks tricky, but it's really one big `try` statement.
Just call `untrustworthy` inside the `try`, and
then use `on`, `catch`, and `finally` to catch exceptions and
call methods on the logger.
{$ end hint.txt $}
```


## Using `this` in a constructor

## 在構造方法中使用 `this`

Dart provides a handy shortcut for assigning
values to properties in a constructor:
use `this.propertyName` when declaring the constructor:

Dart 提供了一個方便的快捷方式，
用於為構造方法中的屬性賦值：
在宣告構造方法時使用 `this.propertyName`。

<?code-excerpt "misc/lib/cheatsheet/this_constructor.dart (required-positional)"?>
```dart
class MyColor {
  int red;
  int green;
  int blue;

  MyColor(this.red, this.green, this.blue);
}

final color = MyColor(80, 80, 128);
```

This technique works for named parameters, too.
Property names become the names of the parameters:

此技巧同樣也適用於命名引數。屬性名為引數的名稱：

<?code-excerpt "misc/lib/cheatsheet/this_constructor.dart (required-named)" replace="/int.*;/.../g; /olorRN/olor/g;"?>
```dart
class MyColor {
  ...

  MyColor({required this.red, required this.green, required this.blue});
}

final color = MyColor(red: 80, green: 80, blue: 80);
```

In the preceding code, `red`, `green`, and `blue` are marked as `required`
because these `int` values can't be null.
If you add default values, you can omit `required`:

在上面的程式碼中，`red`、`green` 和 `blue` 被標記為 `required`，
因為這些 `int` 數值不能為空。
如果你指定了預設值，你可以忽略 `required`。

對於可選引數，預設值為期望值：

<?code-excerpt "misc/lib/cheatsheet/this_constructor.dart (defaulted)" replace="/olorO/olor/g; /.positional//g; /.named//g;"?>
```dart
MyColor([this.red = 0, this.green = 0, this.blue = 0]);
// or
MyColor({this.red = 0, this.green = 0, this.blue = 0});
```

### Code example
{: .no_toc}

### 程式碼範例

Add a one-line constructor to `MyClass` that uses
`this.` syntax to receive and assign values for
all three properties of the class.

使用 `this` 語法向 `MyClass` 新增一行構造方法，
並接收和分配全部（三個）屬性。

Ignore all initial errors in the DartPad.

可以忽略以下程式碼一開始在 DartPad 中的錯誤。

```dart:run-dartpad:ga_id-this_constructor
{$ begin main.dart $}
class MyClass {
  final int anInt;
  final String aString;
  final double aDouble;
  
  // Create a constructor here.
}
{$ end main.dart $}
{$ begin solution.dart $}
class MyClass {
  final int anInt;
  final String aString;
  final double aDouble;
  
  MyClass(this.anInt, this.aString, this.aDouble);
}
{$ end solution.dart $}
{$ begin test.dart $}
void main() {
  final errs = <String>[];
  
  try {
    final obj = MyClass(1, 'two', 3);
    
    if (obj.anInt != 1) {
      errs.add('Called MyClass(1, \'two\', 3) and got an object with anInt of ${obj.anInt} instead of the expected value (1).');
    }

    if (obj.anInt != 1) {
      errs.add('Called MyClass(1, \'two\', 3) and got an object with aString of \'${obj.aString}\' instead of the expected value (\'two\').');
    }

    if (obj.anInt != 1) {
      errs.add('Called MyClass(1, \'two\', 3) and got an object with aDouble of ${obj.aDouble} instead of the expected value (3).');
    }
  } catch (e) {
    _result(false, ['Called MyClass(1, \'two\', 3) and got an exception of type ${e.runtimeType}.']);
  }
  
  if (errs.isEmpty) {
    _result(true);
  } else {
    _result(false, errs);
  }
}
{$ end test.dart $}
{$ begin hint.txt $}
This exercise has a one-line solution.
Just declare the constructor with
`this.anInt`, `this.aString`, and `this.aDouble`
as its parameters in that order.
{$ end hint.txt $}
```

{% comment %}
This one seems super easy compared to previous ones.
We've already seen it in the Exceptions example,
and I'd already used it in a previous example.
Move it up higher? Or make it more challenging, somehow?
Maybe require both positional and optional named parameters (with defaults)?

這樣相比之前要簡單很多。我們已經在 Exception 的範例中見過它了。並且我已經在之前的範例中使用了。
把它變得更高階？或者以某種方式讓它更具挑戰性？
也許需要位置和可選的命名引數（具有預設值）？

{% endcomment %}

## Initializer lists

Sometimes when you implement a constructor,
you need to do some setup before the constructor body executes.
For example, final fields must have values
before the constructor body executes.
Do this work in an initializer list,
which goes between the constructor's signature and its body:

有時，當你在實現建構函式時，您需要在建構函式體執行之前進行一些初始化。
例如，final 修飾的欄位必須在建構函式體執行之前賦值。
在初始化列表中執行此操作，該列表位於建構函式的簽名與其函式體之間：

<?code-excerpt "misc/lib/language_tour/classes/point_alt.dart (initializer-list-no-comment)"?>
```dart
Point.fromJson(Map<String, double> json)
    : x = json['x']!,
      y = json['y']! {
  print('In Point.fromJson(): ($x, $y)');
}
```

The initializer list is also a handy place to put asserts,
which run only during development:

初始化列表也是放置斷言的便利位置，它僅會在開發期間執行：

<?code-excerpt "misc/lib/cheatsheet/initializer_lists.dart (assert)"?>
```dart
NonNegativePoint(this.x, this.y)
    : assert(x >= 0),
      assert(y >= 0) {
  print('I just made a NonNegativePoint: ($x, $y)');
}
```

### Code example
{: .no_toc}

### 程式碼範例

Complete the `FirstTwoLetters` constructor below.
Use an initializer list to assign the first two characters in `word` to
the `letterOne` and `LetterTwo` properties.
For extra credit, add an `assert` to catch words of less than two characters.

完成下面的 `FirstTwoLetters` 的建構函式。使用的初始化列表將 `word` 的前兩個字元分配給 `letterOne` 和 `LetterTwo` 屬性。要獲得額外的信用，請新增一個 `斷言` 以捕獲少於兩個字元的單詞。

Ignore all initial errors in the DartPad.

可以忽略以下程式碼一開始在 DartPad 中的錯誤。

{% comment %}

Is the assert even executed? I can't see any effect on the test,
which makes me think asserts are ignored.
Also, the test just checks for the presence of any exception, not for
an AssertionError.

Also, my print() wasn't visible in the Output until I fixed my code and/or
the test. That was unexpected.
It'd be cool if Output appeared only if you want it, like Solution does.

FINALLY: Suggest using https://pub.dev/packages/characters
if this is a user-entered string.
{% endcomment %}

```dart:run-dartpad:ga_id-initializer_lists
{$ begin main.dart $}
class FirstTwoLetters {
  final String letterOne;
  final String letterTwo;

  // Create a constructor with an initializer list here:
  FirstTwoLetters(String word)
    ...
}
{$ end main.dart $}
{$ begin solution.dart $}
class FirstTwoLetters {
  final String letterOne;
  final String letterTwo;

  FirstTwoLetters(String word)
      : assert(word.length >= 2),
        letterOne = word[0],
        letterTwo = word[1];
}
{$ end solution.dart $}
{$ begin test.dart $}
void main() {
  final errs = <String>[];

  try {
    final result = FirstTwoLetters('My String');
    
    if (result.letterOne != 'M') {
      errs.add('Called FirstTwoLetters(\'My String\') and got an object with letterOne equal to \'${result.letterOne}\' instead of the expected value (\'M\').');
    }

    if (result.letterTwo != 'y') {
      errs.add('Called FirstTwoLetters(\'My String\') and got an object with letterTwo equal to \'${result.letterTwo}\' instead of the expected value (\'y\').');
    }
  } catch (e) {
    errs.add('Called FirstTwoLetters(\'My String\') and got an exception of type ${e.runtimeType}.');
  }

  bool caughtException = false;
  
  try {
    FirstTwoLetters('');
  } catch (e) {
    caughtException = true;
  }
  
  if (!caughtException) {
    errs.add('Called FirstTwoLetters(\'\') and didn\'t get an exception from the failed assertion.');
  }
  
  if (errs.isEmpty) {
    _result(true);
  } else {
    _result(false, errs);
  }
}
{$ end test.dart $}
{$ begin hint.txt $}
Two assignments need to happen:
letterOne should be word[0], and letterTwo should be word[1].
{$ end hint.txt $}
```


## Named constructors

## 命名構造方法

{% comment %}
Much like JavaScript, Dart doesn't support method overloads
(two methods with the same name but different signatures).
[ISSUE: methods & constructors aren't the same thing,
so I deleted that. We can add it back if we can word it better.]
{% endcomment %}
To allow classes to have multiple constructors,
Dart supports named constructors:

為了允許一個類別具有多個構造方法，
Dart 支援命名構造方法：

<?code-excerpt "misc/lib/cheatsheet/named_constructor.dart (point-class)"?>
```dart
class Point {
  double x, y;

  Point(this.x, this.y);

  Point.origin()
      : x = 0,
        y = 0;
}
```

To use a named constructor, invoke it using its full name:

為了使用命名構造方法，請使用全名呼叫它：

<?code-excerpt "misc/test/cheatsheet/constructor_test.dart (origin-point)"?>
```dart
final myPoint = Point.origin();
```

### Code example
{: .no_toc}

### 程式碼範例

Give the `Color` class a constructor named `Color.black`
that sets all three properties to zero.

給 `Color` 類新增一個叫做 `Color.black` 的方法，
它將會把三個屬性的值都設為 0。

Ignore all initial errors in the DartPad.

可以忽略以下程式碼一開始在 DartPad 中的錯誤。

```dart:run-dartpad:height-240px:ga_id-named_constructors
{$ begin main.dart $}
class Color {
  int red;
  int green;
  int blue;
  
  Color(this.red, this.green, this.blue);

  // Create a named constructor called "Color.black" here:
}
{$ end main.dart $}
{$ begin solution.dart $}
class Color {
  int red;
  int green;
  int blue;
  
  Color(this.red, this.green, this.blue);

  Color.black()
      : red = 0,
        green = 0,
        blue = 0;
}
{$ end solution.dart $}
{$ begin test.dart $}
void main() {
  final errs = <String>[];

  try {
    final result = Color.black();
    
    if (result.red != 0) {
      errs.add('Called Color.black() and got a Color with red equal to ${result.red} instead of the expected value (0).');
    }

    if (result.green != 0) {
      errs.add('Called Color.black() and got a Color with green equal to ${result.green} instead of the expected value (0).');
    }

    if (result.blue != 0) {
  errs.add('Called Color.black() and got a Color with blue equal to ${result.blue} instead of the expected value (0).');
    }
  } catch (e) {
    _result(false, ['Called Color.black() and got an exception of type ${e.runtimeType}.']);
    return;
  }

  if (errs.isEmpty) {
    _result(true);
  } else {
    _result(false, errs);
  }
}
{$ end test.dart $}
{$ begin hint.txt $}
The declaration for your constructor should be `Color.black() {}`.
Inside the braces, set red, green, and blue to zero.
{$ end hint.txt $}
```


## Factory constructors

## 工廠構造方法

Dart supports factory constructors,
which can return subtypes or even null.
To create a factory constructor, use the `factory` keyword:

Dart 支援工廠構造方法。它能夠返回其子類別甚至 null 物件。
要建立一個工廠構造方法，請使用 `factory` 關鍵字。

<?code-excerpt "misc/lib/cheatsheet/factory_constructors.dart"?>
```dart
class Square extends Shape {}

class Circle extends Shape {}

class Shape {
  Shape();

  factory Shape.fromTypeName(String typeName) {
    if (typeName == 'square') return Square();
    if (typeName == 'circle') return Circle();

    throw ArgumentError('Unrecognized $typeName');
  }
}
```

### Code example
{: .no_toc}

### 程式碼範例

Fill in the factory constructor named `IntegerHolder.fromList`,
making it do the following:

填寫名為 `IntegerHolder.fromList` 的工廠構造方法，使其執行以下操作：

* If the list has **one** value,
  create an `IntegerSingle` with that value.

  若列表只有**一個**值，那麼就用它來建立一個 `IntegerSingle`。

* If the list has **two** values,
  create an `IntegerDouble` with the values in order.

  如果這個列表有兩個值，那麼按其順序建立一個 `IntegerDouble`。

* If the list has **three** values,
  create an `IntegerTriple` with the values in order.

  如果這個列表有三個值，那麼按其順序建立一個 `IntegerTriple`。

* Otherwise, throw an `Error`.

  否則，丟擲一個 `Error`。

```dart:run-dartpad:height-415px:ga_id-factory_constructors
{$ begin main.dart $}
class IntegerHolder {
  IntegerHolder();
  
  // Implement this factory constructor.
  factory IntegerHolder.fromList(List<int> list) {
    TODO();
  }
}

class IntegerSingle extends IntegerHolder {
  final int a;
  IntegerSingle(this.a); 
}

class IntegerDouble extends IntegerHolder {
  final int a;
  final int b;
  IntegerDouble(this.a, this.b); 
}

class IntegerTriple extends IntegerHolder {
  final int a;
  final int b;
  final int c;
  IntegerTriple(this.a, this.b, this.c); 
}
{$ end main.dart $}
{$ begin solution.dart $}
class IntegerHolder {
  IntegerHolder();
  
  factory IntegerHolder.fromList(List<int> list) {
    if (list.length == 1) {
      return IntegerSingle(list[0]);
    } else if (list.length == 2) {
      return IntegerDouble(list[0], list[1]);
    } else if (list.length == 3) {
      return IntegerTriple(list[0], list[1], list[2]);
    } else {
      throw Error();
    } 
  }
}

class IntegerSingle extends IntegerHolder {
  final int a;
  IntegerSingle(this.a); 
}

class IntegerDouble extends IntegerHolder {
  final int a;
  final int b;
  IntegerDouble(this.a, this.b); 
}

class IntegerTriple extends IntegerHolder {
  final int a;
  final int b;
  final int c;
  IntegerTriple(this.a, this.b, this.c); 
}
{$ end solution.dart $}
{$ begin test.dart $}
void main() {
  final errs = <String>[];

  bool _throwed = false;
  try {
    IntegerHolder.fromList([]);
  } on UnimplementedError {
    _result(false, ['Test failed. Did you implement the method?']);
    return;
  } on Error {
    _throwed = true;
  } catch (e) {
    _result(false, ['Called IntegerSingle.fromList([]) and got an exception of type ${e.runtimeType}.']);
    return;
  }
  
  if (!_throwed) {
    errs.add('Called IntegerSingle.fromList([]) and didn\'t throw Error.');
  } 

  try {
    final obj = IntegerHolder.fromList([1]);
    
    if (obj is! IntegerSingle) {
      errs.add('Called IntegerHolder.fromList([1]) and got an object of type ${obj.runtimeType} instead of IntegerSingle.');
    } else {
      if (obj.a != 1) {
        errs.add('Called IntegerHolder.fromList([1]) and got an IntegerSingle with an \'a\' value of ${obj.a} instead of the expected (1).');
      }
    }
  } catch (e) {
    _result(false, ['Called IntegerHolder.fromList([]) and got an exception of type ${e.runtimeType}.']);
    return;
  }

  try {
    final obj = IntegerHolder.fromList([1, 2]);
    
    if (obj is! IntegerDouble) {
      errs.add('Called IntegerHolder.fromList([1, 2]) and got an object of type ${obj.runtimeType} instead of IntegerDouble.');
    } else {
      if (obj.a != 1) {
        errs.add('Called IntegerHolder.fromList([1, 2]) and got an IntegerDouble with an \'a\' value of ${obj.a} instead of the expected (1).');
      }
      
      if (obj.b != 2) {
        errs.add('Called IntegerHolder.fromList([1, 2]) and got an IntegerDouble with an \'b\' value of ${obj.b} instead of the expected (2).');
      }
    }
  } catch (e) {
    _result(false, ['Called IntegerHolder.fromList([1, 2]) and got an exception of type ${e.runtimeType}.']);
    return;
  }

  try {
    final obj = IntegerHolder.fromList([1, 2, 3]);
    
    if (obj is! IntegerTriple) {
      errs.add('Called IntegerHolder.fromList([1, 2, 3]) and got an object of type ${obj.runtimeType} instead of IntegerTriple.');
    } else {
      if (obj.a != 1) {
        errs.add('Called IntegerHolder.fromList([1, 2, 3]) and got an IntegerTriple with an \'a\' value of ${obj.a} instead of the expected (1).');
      }
      
      if (obj.b != 2) {
        errs.add('Called IntegerHolder.fromList([1, 2, 3]) and got an IntegerTriple with an \'a\' value of ${obj.b} instead of the expected (2).');
      }

      if (obj.c != 3) {
        errs.add('Called IntegerHolder.fromList([1, 2, 3]) and got an IntegerTriple with an \'a\' value of ${obj.b} instead of the expected (2).');
      }
    }
  } catch (e) {
    _result(false, ['Called IntegerHolder.fromList([1, 2, 3]) and got an exception of type ${e.runtimeType}.']);
    return;
  }

  if (errs.isEmpty) {
    _result(true);
  } else {
    _result(false, errs);
  }
}
{$ end test.dart $}
{$ begin hint.txt $}
Inside the factory constructor,
check the length of the list and create an
IntegerSingle, IntegerDouble, or IntegerTriple as appropriate.
{$ end hint.txt $}
```

## Redirecting constructors

## 重新導向構造方法

Sometimes a constructor’s only purpose is to redirect to
another constructor in the same class.
A redirecting constructor’s body is empty,
with the constructor call appearing after a colon (`:`).

有時一個構造方法僅僅用來重新導向到該類別的另一個構造方法。
重新導向方法沒有主體，它在冒號（`:`）之後呼叫另一個構造方法。

<?code-excerpt "misc/lib/cheatsheet/redirecting_constructors.dart (redirecting-constructors)"?>
```dart
class Automobile {
  String make;
  String model;
  int mpg;

  // The main constructor for this class.
  Automobile(this.make, this.model, this.mpg);

  // Delegates to the main constructor.
  Automobile.hybrid(String make, String model) : this(make, model, 60);

  // Delegates to a named constructor
  Automobile.fancyHybrid() : this.hybrid('Futurecar', 'Mark 2');
}
```

### Code example
{: .no_toc}

### 程式碼範例

Remember the `Color` class from above? Create a named constructor called
`black`, but rather than manually assigning the properties, redirect it to the
default constructor with zeros as the arguments.

還記得我們之前提到的 `Color` 類嗎？建立一個叫做 `black` 的命名構造方法，
但這次我們不要手動分配屬性，而是將 0 作為引數，
重新導向到預設的構造方法。

Ignore all initial errors in the DartPad.

可以忽略以下程式碼一開始在 DartPad 中的錯誤。

```dart:run-dartpad:height-255px:ga_id-redirecting_constructors
{$ begin main.dart $}
class Color {
  int red;
  int green;
  int blue;
  
  Color(this.red, this.green, this.blue);

  // Create a named constructor called "black" here and redirect it
  // to call the existing constructor
}
{$ end main.dart $}
{$ begin solution.dart $}
class Color {
  int red;
  int green;
  int blue;
  
  Color(this.red, this.green, this.blue);

  Color.black() : this(0, 0, 0);
}
{$ end solution.dart $}
{$ begin test.dart $}
void main() {
  final errs = <String>[];

  try {
    final result = Color.black();
    
    if (result.red != 0) {
      errs.add('Called Color.black() and got a Color with red equal to ${result.red} instead of the expected value (0).');
    }

    if (result.green != 0) {
      errs.add('Called Color.black() and got a Color with green equal to ${result.green} instead of the expected value (0).');
    }

    if (result.blue != 0) {
  errs.add('Called Color.black() and got a Color with blue equal to ${result.blue} instead of the expected value (0).');
    }
  } catch (e) {
    _result(false, ['Called Color.black() and got an exception of type ${e.runtimeType}.']);
    return;
  }

  if (errs.isEmpty) {
    _result(true);
  } else {
    _result(false, errs);
  }
}
{$ end test.dart $}
{$ begin hint.txt $}
Your constructor should redirect to `this(0, 0, 0)`.
{$ end hint.txt $}
```


## Const constructors

## Const 構造方法

If your class produces objects that never change, you can make these objects compile-time constants. To
do this, define a `const` constructor and make sure that all instance variables
are final.

如果你的類產生的物件永遠都不會更改，
則可以讓這些物件成為編譯時常量。
為此，請定義 `const` 構造方法並確保所有例項變數都是 final 的。

<?code-excerpt "misc/lib/cheatsheet/redirecting_constructors.dart (const-constructors)"?>
```dart
class ImmutablePoint {
  static const ImmutablePoint origin = ImmutablePoint(0, 0);

  final int x;
  final int y;

  const ImmutablePoint(this.x, this.y);
}
```

### Code example
{: .no_toc}

### 程式碼範例

Modify the `Recipe` class so its instances can be constants,
and create a constant constructor that does the following:

修改 `Recipe` 類，使其例項成為常量，並建立一個執行以下操作的常量構造方法：

* Has three parameters: `ingredients`, `calories`,
  and `milligramsOfSodium` (in that order).

  該方法有三個引數：`ingredients`、`calories` 和 `milligramsOfSodium`。（按照此順序）

* Uses `this.` syntax to automatically assign the parameter values to the
  object properties of the same name.

  使用 `this` 語法自動將引數值分配給同名的物件屬性。

* Is constant, with the `const` keyword just before
  `Recipe` in the constructor declaration.

  在 `Recipe` 的構造方法宣告之前，
  用 `const` 關鍵字使其成為常量。

Ignore all initial errors in the DartPad.

可以忽略以下程式碼一開始在 DartPad 中的錯誤。

```dart:run-dartpad:ga_id-const_constructors
{$ begin main.dart $}
class Recipe {
  List<String> ingredients;
  int calories;
  double milligramsOfSodium;
}
{$ end main.dart $}
{$ begin solution.dart $}
class Recipe {
  final List<String> ingredients;
  final int calories;
  final double milligramsOfSodium;

  const Recipe(this.ingredients, this.calories, this.milligramsOfSodium);
}
{$ end solution.dart $}
{$ begin test.dart $}
void main() {
  final errs = <String>[];

  try {
    const obj = Recipe(['1 egg', 'Pat of butter', 'Pinch salt'], 120, 200);
    
    if (obj.ingredients.length != 3) {
      errs.add('Called Recipe([\'1 egg\', \'Pat of butter\', \'Pinch salt\'], 120, 200) and got an object with ingredient list of length ${obj.ingredients.length} rather than the expected length (3).');
    }
    
    if (obj.calories != 120) {
      errs.add('Called Recipe([\'1 egg\', \'Pat of butter\', \'Pinch salt\'], 120, 200) and got an object with a calorie value of ${obj.calories} rather than the expected value (120).');
    }
    
    if (obj.milligramsOfSodium != 200) {
      errs.add('Called Recipe([\'1 egg\', \'Pat of butter\', \'Pinch salt\'], 120, 200) and got an object with a milligramsOfSodium value of ${obj.milligramsOfSodium} rather than the expected value (200).');
    }
  } catch (e) {
    _result(false, ['Tried calling Recipe([\'1 egg\', \'Pat of butter\', \'Pinch salt\'], 120, 200) and received a null.']);
  }
  
  if (errs.isEmpty) {
    _result(true);
  } else {
    _result(false, errs);
  }
}
{$ end test.dart $}
{$ begin hint.txt $}
To make the constructor const, you'll need to make all the properties final.
{$ end hint.txt $}
```

## What's next?

## 下一步是什麼？

We hope you enjoyed using this codelab to learn or test your knowledge of
some of the most interesting features of the Dart language.
Here are some suggestions for what to do now:

我們希望你能夠喜歡這個 codelab 來學習或測試你對 Dart 語言中一些最有趣的功能的知識。下面是一些有關現在該做什麼的建議：

* Try [other Dart codelabs](/codelabs).

  嘗試閱讀 [其他 Dart codelab](/codelabs)。

* Read the [Dart language tour](/guides/language/language-tour).

  閱讀 [Dart 語言之旅](/guides/language/language-tour)。

* Play with [DartPad.]({{site.dartpad}})

  在 [DartPad]({{site.dartpad}}) 上進行練習。

* [Get the Dart SDK](/get-dart).

  [獲取 Dart SDK](/get-dart)。
