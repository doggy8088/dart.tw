---
title: Language samples
title: Dart 程式語言概覽
description: Examples of idiomatic Dart with links to larger examples.
description: 展示常見的 Dart 語言的用法。
---

This collection is not exhaustive—it's just a brief
introduction to the language for people who like to learn by example.
You might also want to check out the language and library tours,
or the [Dart cheatsheet codelab](/codelabs/dart-cheatsheet).

本文向你展示的 Dart 語言用法並不全面&mdash;&mdash;
這裡只是對那些喜歡透過範例瞭解語言的人提供一個簡單的介紹。
你也許會對 [Dart 語言的速查表 CodeLab](/codelabs/dart-cheatsheet) 或
Dart 語言概覽和函式庫概覽更感興趣。

<div class="card-grid no_toc_section">
  <div class="card">
    <h3><a href="/guides/language/language-tour">Language tour</a></h3>
    <h3><a href="/guides/language/language-tour">語言概覽</a></h3>
    <p>
      A comprehensive tour, with examples, of the Dart language.
      Most of the <em>read more</em> links in this page
      point to the language tour.
    </p>
    <p>
      包含範例的 Dart 語言全面概覽。
      本文中大部分的 <strong>閱讀更多</strong> 連結均會跳轉到此概覽中。
    </p>
  </div>
  <div class="card">
    <h3><a href="/guides/libraries/library-tour">Library tour</a></h3>
    <h3><a href="/guides/libraries/library-tour">函式庫概覽</a></h3>
    <p>
      An example-based introduction to the Dart core libraries.
      See how to use the built-in types, collections,
      dates and times, streams, and more.
    </p>
    <p>
      透過各種範例向你介紹 Dart 的核心庫。
      透過此概覽你可以瞭解更多關於內建型別、集合、
      日期時間、非同步 Stream 以及其它 Dart 核心功能的相關資訊。
    </p>
  </div>
</div>


## Hello World

## 你好，世界！

Every app has a `main()` function.
To display text on the console, you can use the top-level `print()` function:

每個應用都有一個 `main()` 函式。你可以使用最上層函式
`print()` 來將一段文字輸出顯示到控制檯：

<?code-excerpt "misc/test/samples_test.dart (hello-world)"?>
```dart
void main() {
  print('Hello, World!');
}
```


## Variables

## 變數

Even in type-safe Dart code, most variables don't need explicit types,
thanks to type inference:

雖然 Dart 是程式碼型別安全的語言，但是由於其支援型別推斷，
因此大多數變數不需要明確地指定型別：

<?code-excerpt "misc/test/samples_test.dart (var)"?>
```dart
var name = 'Voyager I';
var year = 1977;
var antennaDiameter = 3.7;
var flybyObjects = ['Jupiter', 'Saturn', 'Uranus', 'Neptune'];
var image = {
  'tags': ['saturn'],
  'url': '//path/to/saturn.jpg'
};
```

[Read more](/guides/language/language-tour#variables) about variables in Dart, 
including default values, the `final` and `const` keywords, and static types.

你可以 [閱讀更多](/guides/language/language-tour#variables) Dart 中關於變數的內容，包括變數的預設值，`final` 和 `const` 關鍵字以及靜態型別等。

## Control flow statements

## 流程控制陳述式

Dart supports the usual control flow statements:

Dart 支援常用的流程控制陳述式：

<?code-excerpt "misc/test/samples_test.dart (control-flow)"?>
```dart
if (year >= 2001) {
  print('21st century');
} else if (year >= 1901) {
  print('20th century');
}

for (final object in flybyObjects) {
  print(object);
}

for (int month = 1; month <= 12; month++) {
  print(month);
}

while (year < 2016) {
  year += 1;
}
```

[Read more](/guides/language/language-tour#control-flow-statements) 
about control flow statements in Dart,
including `break` and `continue`, `switch` and `case`, and `assert`.

你可以 [閱讀更多](/guides/language/language-tour#control-flow-statements) Dart 中關於控制流程陳述式的內容，包括 `break` 和 `continue` 關鍵字、`switch` 陳述式和 `case` 子句以及 `assert` 陳述式。

## Functions

## 函式

[We recommend](/guides/language/effective-dart/design#types)
specifying the types of each function's arguments and return value:

[我們建議](/guides/language/effective-dart/design#types)
為每個函式的引數以及返回值都指定型別：

<?code-excerpt "misc/test/samples_test.dart (functions)"?>
```dart
int fibonacci(int n) {
  if (n == 0 || n == 1) return n;
  return fibonacci(n - 1) + fibonacci(n - 2);
}

var result = fibonacci(20);
```

A shorthand `=>` (_arrow_) syntax is handy for functions that
contain a single statement.
This syntax is especially useful when passing anonymous functions as arguments:

`=>` (**胖箭頭**) 簡寫語法用於僅包含一條陳述式的函式。
該語法在將匿名函式作為引數傳遞時非常有用：

<?code-excerpt "misc/test/samples_test.dart (arrow)"?>
```dart
flybyObjects.where((name) => name.contains('turn')).forEach(print);
```

Besides showing an anonymous function (the argument to `where()`),
this code shows that you can use a function as an argument:
the top-level `print()` function is an argument to `forEach()`.

上面的範例除了向你展示了匿名函式（上例中傳入 `where()` 函式的引數即是一個匿名函式）外，還向你展示了將函式作為引數使用的方式：上面範例將最上層函式 `print()` 作為引數傳給了 `forEach()` 函式。

[Read more](/guides/language/language-tour#functions) about functions in Dart,
including optional parameters, default parameter values, and lexical scope.

你可以 [閱讀更多](/guides/language/language-tour#functions) Dart 中有關函式的內容，包括可選引數、預設引數值以及詞法作用域。

## Comments

## 註釋

Dart comments usually start with `//`.

Dart 通常使用雙斜槓 `//` 作為註釋的開始。

```dart

/// 這是一個文件註釋。
/// 文件註釋用於為庫、類以及類別的成員添加註釋。
/// 像 IDE 和 dartdoc 這樣的工具可以專門處理文件註釋。

/* 也可以像這樣使用單斜槓和星號的註釋方式 */
```

[Read more](/guides/language/language-tour#comments) about comments in Dart,
including how the documentation tooling works.

你可以 [閱讀更多](/guides/language/language-tour#comments) Dart 中有關注釋的內容，包括文件工具的工作原理。

## Imports

## 匯入 (Import)

To access APIs defined in other libraries, use `import`.

使用 `import` 關鍵字來存取在其它庫中定義的 API。

<?code-excerpt "misc/test/samples_test.dart (import)" plaster="none"?>
```
// Importing core libraries
import 'dart:math';

// Importing libraries from external packages
import 'package:test/test.dart';

// Importing files
import 'path/to/my_other_file.dart';
```

[Read more](/guides/language/language-tour#libraries-and-visibility) 
about libraries and visibility in Dart,
including library prefixes, `show` and `hide`, 
and lazy loading through the `deferred` keyword.

你可以 [閱讀更多](/guides/language/language-tour#libraries-and-visibility)
Dart 中有關庫和可見性的內容，包括庫字首、`show` 和 `hide`
關鍵字以及透過 `deferred` 關鍵字實現的延遲載入。

## Classes

## 類別（Class）

Here's an example of a class with three properties, two constructors,
and a method. One of the properties can't be set directly, so it's
defined using a getter method (instead of a variable).

下面的範例中向你展示了一個包含三個屬性、
兩個建構函式以及一個方法的類別。其中一個屬性不能直接賦值，
因此它被定義為一個 getter 方法（而不是變數）。

<?code-excerpt "misc/lib/samples/spacecraft.dart (class)"?>
```dart
class Spacecraft {
  String name;
  DateTime? launchDate;

  // Read-only non-final property
  int? get launchYear => launchDate?.year;

  // Constructor, with syntactic sugar for assignment to members.
  Spacecraft(this.name, this.launchDate) {
    // Initialization code goes here.
  }

  // Named constructor that forwards to the default one.
  Spacecraft.unlaunched(String name) : this(name, null);

  // Method.
  void describe() {
    print('Spacecraft: $name');
    // Type promotion doesn't work on getters.
    var launchDate = this.launchDate;
    if (launchDate != null) {
      int years = DateTime.now().difference(launchDate).inDays ~/ 365;
      print('Launched: $launchYear ($years years ago)');
    } else {
      print('Unlaunched');
    }
  }
}
```

You might use the `Spacecraft` class like this:

你可以像下面這樣使用 `Spacecraft` 類：

<?code-excerpt "misc/test/samples_test.dart (use class)" plaster="none"?>
```dart
var voyager = Spacecraft('Voyager I', DateTime(1977, 9, 5));
voyager.describe();

var voyager3 = Spacecraft.unlaunched('Voyager III');
voyager3.describe();
```

[Read more](/guides/language/language-tour#classes) about classes in Dart,
including initializer lists, optional `new` and `const`, redirecting constructors,
`factory` constructors, getters, setters, and much more.

你可以 [閱讀更多](/guides/language/language-tour#classes)
Dart 中有關類別的內容，包括初始化列表、可選的 `new` 和 `const` 關鍵字、
重新導向建構函式、由 `factory` 關鍵字定義的工廠建構函式
以及 Getter 和 Setter 方法等等。

## Enums

## 列舉型別 (Enum)

Enums are a way of enumerating a predefined set of values or instances
in a way which ensures that there cannot be any other instances of that type.

列舉型別的取值範圍是一組預定義的值或例項。

Here is an example of a simple `enum` that defines
a simple list of predefined planet types:

下面這個簡單的列舉範例定義了一組行星類別：

<?code-excerpt "misc/lib/samples/spacecraft.dart (simple-enum)"?>
```dart
enum PlanetType { terrestrial, gas, ice }
```

Here is an example of an enhanced enum declaration
of a class describing planets,
with a defined set of constant instances,
namely the planets of our own solar system.

下面是一個增強型列舉的範例，定義了一組行星類別的常量例項，即太陽系的行星：

<?code-excerpt "misc/lib/samples/spacecraft.dart (enhanced-enum)"?>
```dart
/// Enum that enumerates the different planets in our solar system
/// and some of their properties.
enum Planet {
  mercury(planetType: PlanetType.terrestrial, moons: 0, hasRings: false),
  venus(planetType: PlanetType.terrestrial, moons: 0, hasRings: false),
  // ···
  uranus(planetType: PlanetType.ice, moons: 27, hasRings: true),
  neptune(planetType: PlanetType.ice, moons: 14, hasRings: true);

  /// A constant generating constructor
  const Planet(
      {required this.planetType, required this.moons, required this.hasRings});

  /// All instance variables are final
  final PlanetType planetType;
  final int moons;
  final bool hasRings;

  /// Enhanced enums support getters and other methods
  bool get isGiant =>
      planetType == PlanetType.gas || planetType == PlanetType.ice;
}
```

You might use the `Planet` enum like this:

你可以這樣使用 `Planet` 列舉：

<?code-excerpt "misc/test/samples_test.dart (use enum)" plaster="none"?>
```dart
final yourPlanet = Planet.earth;

if (!yourPlanet.isGiant) {
  print('Your planet is not a "giant planet".');
}
```

[Read more](/guides/language/language-tour#enums) about enums in Dart,
including enhanced enum requirements, automatically introduced properties,
accessing enumerated value names, switch statement support, and much more.

你可以 [閱讀更多](/guides/language/language-tour#enums) Dart 中有關列舉的內容，
包括增強型列舉的限制條件、列舉預設包含的屬性、如何獲取列舉值的名稱以及在 `switch` 陳述式中使用列舉等等。

## Inheritance

## 類別繼承

Dart has single inheritance.

Dart 支援單繼承。

<?code-excerpt "misc/lib/samples/spacecraft.dart (extends)"?>
```dart
class Orbiter extends Spacecraft {
  double altitude;

  Orbiter(super.name, DateTime super.launchDate, this.altitude);
}
```

[Read more](/guides/language/language-tour#extending-a-class) 
about extending classes, the optional `@override` annotation, and more.

你可以 [閱讀更多](/guides/language/language-tour#extending-a-class) Dart 中有關類繼承的內容，比如可選的 `@override` 註解等等。

## Mixins

Mixins are a way of reusing code in multiple class hierarchies. The following is
a mixin declaration:

Mixin 是一種在多個類層次結構中重用程式碼的方法。下面的是宣告一個 Mixin 的做法：

<?code-excerpt "misc/lib/samples/spacecraft.dart (mixin)"?>
```dart
mixin Piloted {
  int astronauts = 1;

  void describeCrew() {
    print('Number of astronauts: $astronauts');
  }
}
```

To add a mixin's capabilities to a class, just extend the class with the mixin.

現在你只需使用 Mixin 的方式繼承這個類就可將該類中的功能新增給其它類別。

<?code-excerpt "misc/lib/samples/spacecraft.dart (mixin-use)" replace="/with/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
class PilotedCraft extends Spacecraft [!with!] Piloted {
  // ···
}
{% endprettify %}

`PilotedCraft` now has the `astronauts` field as well as the `describeCrew()` method.

自此，`PilotedCraft` 類中就包含了 `astronauts` 欄位以及 `describeCrew()` 方法。

[Read more](/guides/language/language-tour#adding-features-to-a-class-mixins) about mixins.

你可以 [閱讀更多](/guides/language/language-tour#adding-features-to-a-class-mixins) 關於 Mixin 的內容。

## Interfaces and abstract classes

## 介面和抽象類別

Dart has no `interface` keyword. 
Instead, all classes implicitly define an interface. 
Therefore, you can implement any class.

Dart 沒有 `interface` 關鍵字。
相反，所有的類都隱含定義了一個介面。
因此，任意類別都可以作為介面被實現。

<?code-excerpt "misc/lib/samples/spacecraft.dart (implements)"?>
```dart
class MockSpaceship implements Spacecraft {
  // ···
}
```

[Read more](/guides/language/language-tour#implicit-interfaces) about implicit interfaces.

你可以 [閱讀更多](/guides/language/language-tour#implicit-interfaces) 關於隱含介面的內容。

You can create an abstract class 
to be extended (or implemented) by a concrete class. 
Abstract classes can contain abstract methods (with empty bodies).

你可以建立一個被任意具體類擴充（或實現）的抽象類別。
抽象類別可以包含抽象方法（不含方法體的方法）。

<?code-excerpt "misc/lib/samples/spacecraft.dart (abstract)" replace="/abstract/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
[!abstract!] class Describable {
  void describe();

  void describeWithEmphasis() {
    print('=========');
    describe();
    print('=========');
  }
}
{% endprettify %}

Any class extending `Describable` has the `describeWithEmphasis()` method, 
which calls the extender's implementation of `describe()`.

任意一個擴充了 `Describable` 的類別都擁有 `describeWithEmphasis()` 方法，
這個方法又會去呼叫實現類別中實現的 `describe()` 方法。

[Read more](/guides/language/language-tour#abstract-classes) 
about abstract classes and methods.

你可以 [閱讀更多](/guides/language/language-tour#abstract-classes) 關於抽象類別和抽象方法的內容。

## Async

## 非同步

Avoid callback hell and make your code much more readable by
using `async` and `await`.

使用 `async` 和 `await` 關鍵字可以讓你避免
回呼(Callback)地獄 (Callback Hell) 並使你的程式碼更具可讀性。

<?code-excerpt "misc/test/samples_test.dart (async)" replace="/async/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
const oneSecond = Duration(seconds: 1);
// ···
Future<void> printWithDelay(String message) [!async!] {
  await Future.delayed(oneSecond);
  print(message);
}
{% endprettify %}

The method above is equivalent to:

上面的方法相當於：

<?code-excerpt "misc/test/samples_test.dart (Future.then)"?>
```dart
Future<void> printWithDelay(String message) {
  return Future.delayed(oneSecond).then((_) {
    print(message);
  });
}
```

As the next example shows, `async` and `await` help make asynchronous code
easy to read.

如下一個範例所示，`async` 和 `await` 關鍵字有助於使非同步程式碼變得易於閱讀。

<?code-excerpt "misc/test/samples_test.dart (await)"?>
```dart
Future<void> createDescriptions(Iterable<String> objects) async {
  for (final object in objects) {
    try {
      var file = File('$object.txt');
      if (await file.exists()) {
        var modified = await file.lastModified();
        print(
            'File for $object already exists. It was modified on $modified.');
        continue;
      }
      await file.create();
      await file.writeAsString('Start describing $object in this file.');
    } on IOException catch (e) {
      print('Cannot create description for $object: $e');
    }
  }
}
```

You can also use `async*`, which gives you a nice, readable way to build streams.

你也可以使用 `async*` 關鍵字，其可以為你提供一個可讀性更好的方式去產生 Stream。

<?code-excerpt "misc/test/samples_test.dart (async*)"?>
```dart
Stream<String> report(Spacecraft craft, Iterable<String> objects) async* {
  for (final object in objects) {
    await Future.delayed(oneSecond);
    yield '${craft.name} flies by $object';
  }
}
```

[Read more](/guides/language/language-tour#asynchrony-support) about
asynchrony support, including `async` functions, `Future`, `Stream`,
and the asynchronous loop (`await for`).

你可以 [閱讀更多](/guides/language/language-tour#asynchrony-support)
關於非同步支援的內容，
包括非同步函式、`Future`、`Stream` 以及非同步迴圈（`await for`）。

## Exceptions

## 例外

To raise an exception, use `throw`:

使用 `throw` 關鍵字丟擲一個例外：

<?code-excerpt "misc/test/samples_test.dart (throw)"?>
```dart
if (astronauts == 0) {
  throw StateError('No astronauts.');
}
```

To catch an exception, use a `try` statement with `on` or `catch` (or both):

使用 `try` 陳述式配合 `on` 或 `catch`（兩者也可同時使用）
關鍵字來捕獲一個例外:

<?code-excerpt "misc/test/samples_test.dart (try)"?>
```
try {
  for (final object in flybyObjects) {
    var description = await File('$object.txt').readAsString();
    print(description);
  }
} on IOException catch (e) {
  print('Could not describe object: $e');
} finally {
  flybyObjects.clear();
}
```

Note that the code above is asynchronous;
`try` works for both synchronous code and code in an `async` function.

注意上述程式碼是非同步的；同步程式碼以及非同步函式中得程式碼都可以使用 `try` 捕獲例外。

[Read more](/guides/language/language-tour#exceptions) about exceptions, 
including stack traces, `rethrow`, 
and the difference between `Error` and `Exception`.

你可以 [閱讀更多](/guides/language/language-tour#exceptions) 
關於例外的內容，包括堆疊追蹤、`rethrow` 關鍵字以及 Error 和 Exception 之間的區別。

## Other topics

## 其他資源

Many more code samples are in the
[language tour](/guides/language/language-tour) and the
[library tour](/guides/libraries/library-tour).
Also see the [Dart API reference,]({{site.dart-api}})
which often contains examples.

[語言概覽](/guides/language/language-tour) 和
[函式庫概覽](/guides/libraries/library-tour) 中會有更多的程式碼範例。
你也可以查閱 [Dart API 文件]({{site.dart_api}})，裡面也常常會有範例程式碼。
