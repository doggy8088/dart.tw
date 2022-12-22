---
title: "Effective Dart: Usage"
title: 高效 Dart 語言指南：用法範例
description: Guidelines for using language features to write maintainable code.
nextpage:
  url: /guides/language/effective-dart/design
  title: Design
  title: API 設計
prevpage:
  url: /guides/language/effective-dart/documentation
  title: Documentation
  title: 文件
---
<?code-excerpt replace="/([A-Z]\w*)\d\b/$1/g"?>
<?code-excerpt path-base="misc/lib/effective_dart"?>

You can use these guidelines every day in the bodies of your Dart code. *Users*
of your library may not be able to tell that you've internalized the ideas here,
but *maintainers* of it sure will.

每天在你寫的 Dart 程式碼中都會應用到這些準則。
庫的*使用者*可能不需要知道你在其中的一些想法，
但是*維護者*肯定是需要的。

## Libraries

## 庫

These guidelines help you compose your program out of multiple files in a
consistent, maintainable way. To keep these guidelines brief, they use "import"
to cover `import` and `export` directives. The guidelines apply equally to both.

這些準則可以幫助你在多個檔案編寫程式的情況下保證一致性和可維護性。
為了讓準則簡潔，這裡使用“import”來同時代表 `import` 和 `export` 。
準則同時適用於這兩者。

### DO use strings in `part of` directives.

### **要** 在 `part of` 中使用字串。

Many Dart developers avoid using `part` entirely. They find it easier to reason
about their code when each library is a single file. If you do choose to use
`part` to split part of a library out into another file, Dart requires the other
file to in turn indicate which library it's a part of. For legacy reasons, Dart
allows this `part of` directive to use the *name* of the library it's a part of.
That makes it harder for tools to physically find the main library file, and can
make it ambiguous which library the part is actually part of.

很多 Dart 開發者會避免直接使用 `part` 。他們發現當庫僅有一個檔案的時候很容易讀懂程式碼。
如果你確實要使用 `part` 將庫的一部分拆分為另一個檔案，則 Dart 要求另一個檔案指示它所屬庫的路徑。 
由於遺留原因， Dart 允許 `part of` 指令使用它所屬的函式庫的*名稱*。
這使得工具很難直接查詢到這個檔案對應主庫檔案，使得庫和檔案之間的關係模糊不清。

The preferred, modern syntax is to use a URI string that points directly to the
library file, just like you use in other directives. If you have some library,
`my_library.dart`, that contains:

推薦的現代語法是使用 URI 字串直接指向庫檔案。
首選的現代語法是使用直接指向庫檔案的URI字串，URI 的使用和其他指令中一樣。
如果你有一些函式庫，`my_library.dart`，其中包含：

<?code-excerpt "my_library.dart"?>
{% prettify dart tag=pre+code %}
library my_library;

part 'some/other/file.dart';
{% endprettify %}

Then the part file should look like:

從庫中拆分的檔案應該如下所示：

{:.good}
<?code-excerpt "some/other/file.dart"?>
{% prettify dart tag=pre+code %}
part of '../../my_library.dart';
{% endprettify %}

And not:

而不是：

{:.bad}
<?code-excerpt "some/other/file_2.dart"?>
{% prettify dart tag=pre+code %}
part of my_library;
{% endprettify %}

### DON'T import libraries that are inside the `src` directory of another package.

### **不要** 匯入 package 中 `src` 目錄下的函式庫。

{% include linter-rule-mention.md rule="implementation_imports" %}

The `src` directory under `lib` [is specified][package guide] to contain
libraries private to the package's own implementation. The way package
maintainers version their package takes this convention into account. They are
free to make sweeping changes to code under `src` without it being a breaking
change to the package.

`lib` 下的 `src` 目錄 [被指定][package guide] 為 package 自己實現的私有庫。
基於包維護者對版本的考慮，package 使用了這種約定。
在不破壞 package 的情況下，維護者可以自由地對 `src` 目錄下的程式碼進行修改。

[package guide]: /tools/pub/package-layout

That means that if you import some other package's private library, a minor,
theoretically non-breaking point release of that package could break your code.

這意味著，你如果匯入了其中的私有庫，
按理論來講，一個不破壞 package 的次版本就會影響到你的程式碼。

### DON'T allow an import path to reach into or out of `lib`.

{% include linter-rule-mention.md rule="avoid_relative_lib_imports" %}

A `package:` import lets you access
a library inside a package's `lib` directory
without having to worry about where the package is stored on your computer.
For this to work, you cannot have imports that require the `lib`
to be in some location on disk relative to other files.
In other words, a relative import path in a file inside `lib`
can't reach out and access a file outside of the `lib` directory,
and a library outside of `lib` can't use a relative path
to reach into the `lib` directory.
Doing either leads to confusing errors and broken programs.

For example, say your directory structure looks like this:

```text
my_package
└─ lib
   └─ api.dart
   test
   └─ api_test.dart
```

And say `api_test.dart` imports `api.dart` in two ways:

{:.bad}
{% prettify dart tag=pre+code %}
import 'package:my_package/api.dart';
import '../lib/api.dart';
{% endprettify %}

Dart thinks those are imports of two completely unrelated libraries.
To avoid confusing Dart and yourself, follow these two rules:

* Don't use `/lib/` in import paths.
* Don't use `../` to escape the `lib` directory.

Instead, when you need to reach into a package's `lib` directory
(even from the same package's `test` directory
or any other top-level directory),
use a `package:` import.

{:.good}
{% prettify dart tag=pre+code %}
import 'package:my_package/api.dart';
{% endprettify %}

A package should never reach *out* of its `lib` directory and
import libraries from other places in the package.


### PREFER relative import paths.

{% include linter-rule-mention.md rule="prefer_relative_imports" %}

Whenever the previous rule doesn't come into play, follow this one.
When an import does *not* reach across `lib`, prefer using relative imports.
They're shorter.
For example, say your directory structure looks like this:

比如，下面是你的 package 目錄結構：

```text
my_package
└─ lib
   ├─ src
   │  └─ stuff.dart
   │  └─ utils.dart
   └─ api.dart
   test
   │─ api_test.dart
   └─ test_utils.dart
```

Here is how the various libraries should import each other:

**lib/api.dart:**

如果 `api.dart` 想匯入 `utils.dart` ，應該這樣使用：

{:.good}
{% prettify dart tag=pre+code %}
import 'src/stuff.dart';
import 'src/utils.dart';
{% endprettify %}

**lib/src/utils.dart:**

{:.good}
{% prettify dart tag=pre+code %}
import '../api.dart';
import 'stuff.dart';
{% endprettify %}

**test/api_test.dart:**

{:.good}
{% prettify dart tag=pre+code %}
import 'package:my_package/api.dart'; // Don't reach into 'lib'.

import 'test_utils.dart'; // Relative within 'test' is fine.
{% endprettify %}


## Null


### DON'T explicitly initialize variables to `null`.

{% include linter-rule-mention.md rule="avoid_init_to_null" %}

If a variable has a non-nullable type, Dart reports a compile error if you try
to use it before it has been definitely initialized. If the variable is
nullable, then it is implicitly initialized to `null` for you. There's no
concept of "uninitialized memory" in Dart and no need to explicitly initialize a
variable to `null` to be "safe".

{:.good}
<?code-excerpt "usage_good.dart (no-null-init)"?>
{% prettify dart tag=pre+code %}
Item? bestDeal(List<Item> cart) {
  Item? bestItem;

  for (final item in cart) {
    if (bestItem == null || item.price < bestItem.price) {
      bestItem = item;
    }
  }

  return bestItem;
}
{% endprettify %}

{:.bad}
<?code-excerpt "usage_bad.dart (no-null-init)" replace="/ = null/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
Item? bestDeal(List<Item> cart) {
  Item? bestItem[! = null!];

  for (final item in cart) {
    if (bestItem == null || item.price < bestItem.price) {
      bestItem = item;
    }
  }

  return bestItem;
}
{% endprettify %}


### DON'T use an explicit default value of `null`.

{% include linter-rule-mention.md rule="avoid_init_to_null" %}

If you make a nullable parameter optional but don't give it a default value, the
language implicitly uses `null` as the default, so there's no need to write it.

{:.good}
<?code-excerpt "usage_good.dart (default-value-null)"?>
{% prettify dart tag=pre+code %}
void error([String? message]) {
  stderr.write(message ?? '\n');
}
{% endprettify %}

{:.bad}
<?code-excerpt "usage_bad.dart (default-value-null)"?>
{% prettify dart tag=pre+code %}
void error([String? message = null]) {
  stderr.write(message ?? '\n');
}
{% endprettify %}


### PREFER using `??` to convert `null` to a boolean value.

This rule applies when an expression can evaluate to `true`, `false`, or `null`,
and you need to pass the result to something that expects a non-nullable boolean
value. A common case is using the result of a null-aware method call as a
condition. You can "convert" `null` to `true` or `false` using `==`, but we
recommend using `??`:

{:.good}
<?code-excerpt "usage_good.dart (convert-null-aware)"?>
{% prettify dart tag=pre+code %}
// If you want null to be false:
if (optionalThing?.isEnabled ?? false) {
  print('Have enabled thing.');
}

// If you want null to be true:
if (optionalThing?.isEnabled ?? true) {
  print('Have enabled thing or nothing.');
}
{% endprettify %}

{:.bad}
<?code-excerpt "usage_bad.dart (convert-null-equals)"?>
{% prettify dart tag=pre+code %}
// If you want null to be false:
if (optionalThing?.isEnabled == true) {
  print('Have enabled thing.');
}

// If you want null to be true:
if (optionalThing?.isEnabled != false) {
  print('Have enabled thing or nothing.');
}
{% endprettify %}

Both operations produce the same result and do the right thing, but `??` is
preferred for three main reasons:

*   The `??` operator signals that the code has something to do with `null`.

*   The `== true` looks like a common mistake where the equality operator is
    redundant and can be removed. That's true when the boolean expression on the
    left will not produce `null`, but not when it can.

*   The `?? false` and `?? true` clearly show what value will be used when the
    expression is `null`. With `== true`, you have to think through the boolean
    logic to realize that means that a `null` gets converted to *false*.

**Exception:** Using a null-aware operator on a variable inside a condition
doesn't promote the variable to a non-nullable type. If you want the variable
to be promoted inside the body of the `if` statement, it might be better to use
an explicit `!= null` check instead of `?.` followed by `??`:

{:.good}
<?code-excerpt "usage_good.dart (null-aware-promote)"?>
{% prettify dart tag=pre+code %}
int measureMessage(String? message) {
  if (message != null && message.isNotEmpty) {
    // message is promoted to String.
    return message.length;
  }

  return 0;
}
{% endprettify %}

{:.bad}
<?code-excerpt "usage_bad.dart (null-aware-promote)"?>
{% prettify dart tag=pre+code %}
int measureMessage(String? message) {
  if (message?.isNotEmpty ?? false) {
    // message is not promoted to String.
    return message!.length;
  }

  return 0;
}
{% endprettify %}


### AVOID `late` variables if you need to check whether they are initialized.

Dart offers no way to tell if a `late` variable
has been initialized or assigned to.
If you access it, it either immediately runs the initializer
(if it has one) or throws an exception.
Sometimes you have some state that's lazily initialized
where `late` might be a good fit,
but you also need to be able to *tell* if the initialization has happened yet.

Although you could detect initialization by storing the state in a `late` variable
and having a separate boolean field
that tracks whether the variable has been set,
that's redundant because Dart *internally*
maintains the initialized status of the `late` variable.
Instead, it's usually clearer to make the variable non-`late` and nullable.
Then you can see if the variable has been initialized
by checking for `null`.

Of course, if `null` is a valid initialized value for the variable,
then it probably does make sense to have a separate boolean field.


### CONSIDER assigning a nullable field to a local variable to enable type promotion.

Checking that a nullable variable is not equal to `null` promotes the variable
to a non-nullable type. That lets you access members on the variable and pass it
to functions expecting a non-nullable type. Unfortunately, promotion is only
sound for local variables and parameters, so fields and top-level variables
aren't promoted.

One pattern to work around this is to assign the field's value to a local
variable. Null checks on that variable do promote, so you can safely treat
it as non-nullable.

{:.good}
<?code-excerpt "usage_good.dart (shadow-nullable-field)"?>
{% prettify dart tag=pre+code %}
class UploadException {
  final Response? response;

  UploadException([this.response]);

  @override
  String toString() {
    final response = this.response;
    if (response != null) {
      return 'Could not complete upload to ${response.url} '
          '(error code ${response.errorCode}): ${response.reason}.';
    }

    return 'Could not upload (no response).';
  }
}
{% endprettify %}

Assigning to a local variable can be cleaner and safer than using `!` every
place the field or top-level variable is used:

{:.bad}
<?code-excerpt "usage_bad.dart (shadow-nullable-field)" replace="/!\./[!!!]./g"?>
{% prettify dart tag=pre+code %}
class UploadException {
  final Response? response;

  UploadException([this.response]);

  @override
  String toString() {
    if (response != null) {
      return 'Could not complete upload to ${response[!!!].url} '
          '(error code ${response[!!!].errorCode}): ${response[!!!].reason}.';
    }

    return 'Could not upload (no response).';
  }
}
{% endprettify %}

Be careful when using a local variable. If you need to write back to the field,
make sure that you don't write back to the local variable instead. (Making the
local variable `final` can prevent such mistakes.) Also, if the field might
change while the local is still in scope, then the local might have a stale
value. Sometimes it's best to simply use `!` on the field.


## Strings

## 字串

Here are some best practices to keep in mind when composing strings in Dart.

下面是一些需要記住的，關於在 Dart 中使用字串的最佳實踐。

### DO use adjacent strings to concatenate string literals.

### **要** 使用相鄰字串的方式連線字面量字串。

{% include linter-rule-mention.md rule="prefer_adjacent_string_concatenation" %}

If you have two string literals—not values, but the actual quoted literal
form—you do not need to use `+` to concatenate them. Just like in C and
C++, simply placing them next to each other does it. This is a good way to make
a single long string that doesn't fit on one line.

如果你有兩個字面量字串（不是變數，是放在引號中的字串），
你不需要使用 `+` 來連線它們。
應該像 C 和 C++ 一樣，只需要將它們挨著在一起就可以了。
這種方式非常適合不能放到一行的長字串的建立。

{:.good}
<?code-excerpt "usage_good.dart (adjacent-strings-literals)"?>
{% prettify dart tag=pre+code %}
raiseAlarm('ERROR: Parts of the spaceship are on fire. Other '
    'parts are overrun by martians. Unclear which are which.');
{% endprettify %}

{:.bad}
<?code-excerpt "usage_bad.dart (adjacent-strings-literals)"?>
{% prettify dart tag=pre+code %}
raiseAlarm('ERROR: Parts of the spaceship are on fire. Other ' +
    'parts are overrun by martians. Unclear which are which.');
{% endprettify %}

### PREFER using interpolation to compose strings and values.

### **推薦** 使用插值的形式來組合字串和值。

{% include linter-rule-mention.md rule="prefer_interpolation_to_compose_strings" %}

If you're coming from other languages, you're used to using long chains of `+`
to build a string out of literals and other values. That does work in Dart, but
it's almost always cleaner and shorter to use interpolation:

如果你之前使用過其他語言，你一定習慣使用大量 `+` 將字面量字串以及字串變數連結建構字串。
這種方式在 Dart 中同樣有效，但是通常情況下使用插值會更清晰簡短：

{:.good}
<?code-excerpt "usage_good.dart (string-interpolation)"?>
{% prettify dart tag=pre+code %}
'Hello, $name! You are ${year - birth} years old.';
{% endprettify %}

{:.bad}
<?code-excerpt "usage_bad.dart (string-interpolation)"?>
{% prettify dart tag=pre+code %}
'Hello, ' + name + '! You are ' + (year - birth).toString() + ' y...';
{% endprettify %}

Note that this guideline applies to combining *multiple* literals and values.
It's fine to use `.toString()` when converting only a single object to a string.

### AVOID using curly braces in interpolation when not needed.

### **避免** 在字串插值中使用不必要的大括號。

{% include linter-rule-mention.md rule="unnecessary_brace_in_string_interps" %}

If you're interpolating a simple identifier not immediately followed by more
alphanumeric text, the `{}` should be omitted.

如果要插入是一個簡單的識別符號，並且後面沒有緊跟隨在其他字母文字，則應省略 `{}` 。

{:.good}
<?code-excerpt "usage_good.dart (string-interpolation-avoid-curly)"?>
{% prettify dart tag=pre+code %}
var greeting = 'Hi, $name! I love your ${decade}s costume.';
{% endprettify %}

{:.bad}
<?code-excerpt "usage_bad.dart (string-interpolation-avoid-curly)"?>
{% prettify dart tag=pre+code %}
var greeting = 'Hi, ${name}! I love your ${decade}s costume.';
{% endprettify %}

## Collections

## 集合

Out of the box, Dart supports four collection types: lists, maps, queues, and sets.
The following best practices apply to collections.

Dart 集合中原生支援了四種類型：list， map， queue， 和 set。
下面是應用於集合的最佳實踐。

### DO use collection literals when possible.

### **要** 儘可能的使用集合字面量。

{% include linter-rule-mention.md rule="prefer_collection_literals" %}

Dart has three core collection types: List, Map, and Set. The Map and Set
classes have unnamed constructors like most classes do. But because these
collections are used so frequently, Dart has nicer built-in syntax for creating
them:

Dart 有三種核心集合型別。List、Map 和 Set，
這些類和大多數類一樣，都有未命名的建構函式，
但由於這些集合使用頻率很高，Dart 有更好的內建語法來建立它們：

{:.good}
<?code-excerpt "usage_good.dart (collection-literals)"?>
{% prettify dart tag=pre+code %}
var points = <Point>[];
var addresses = <String, Address>{};
var counts = <int>{};
{% endprettify %}

{:.bad}
<?code-excerpt "usage_bad.dart (collection-literals)"?>
{% prettify dart tag=pre+code %}
var addresses = Map<String, Address>();
var counts = Set<int>();
{% endprettify %}

Note that this guideline doesn't apply to the *named* constructors for those
classes. `List.from()`, `Map.fromIterable()`, and friends all have their uses.
(The List class also has an unnamed constructor, but it is prohibited in null
safe Dart.)

Collection literals are particularly powerful in Dart
because they give you access to the [spread operator][spread]
for including the contents of other collections,
and [`if` and `for`][control] for performing control flow while
building the contents:

[spread]: /guides/language/language-tour#spread-operator
[control]: /guides/language/language-tour#collection-operators

{:.good}
<?code-excerpt "usage_good.dart (spread-etc)"?>
{% prettify dart tag=pre+code %}
var arguments = [
  ...options,
  command,
  ...?modeFlags,
  for (var path in filePaths)
    if (path.endsWith('.dart')) path.replaceAll('.dart', '.js')
];
{% endprettify %}

{:.bad}
<?code-excerpt "usage_bad.dart (spread-etc)"?>
{% prettify dart tag=pre+code %}
var arguments = <String>[];
arguments.addAll(options);
arguments.add(command);
if (modeFlags != null) arguments.addAll(modeFlags);
arguments.addAll(filePaths
    .where((path) => path.endsWith('.dart'))
    .map((path) => path.replaceAll('.dart', '.js')));
{% endprettify %}


注意，對於集合類別的 *命名* 建構函式則不適用上面的規則。
`List.from()`、 `Map.fromIterable()` 都有其使用場景。 
如果需要一個固定長度的結合，使用 ``List()`` 來建立一個固定長度的 list 也是合理的。

### DON'T use `.length` to see if a collection is empty.

### **不要** 使用 `.length` 來判斷一個集合是否為空。

{% include linter-rule-mention.md rule1="prefer_is_empty" rule2="prefer_is_not_empty" %}

The [Iterable][] contract does not require that a collection know its length or
be able to provide it in constant time. Calling `.length` just to see if the
collection contains *anything* can be painfully slow.

[Iterable][] 合約並不要求集合知道其長度，也沒要求在遍歷的時候其長度不能改變。
透過呼叫 `.length`  來判斷集合是否包含內容是非常低效的。

[iterable]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Iterable-class.html

Instead, there are faster and more readable getters: `.isEmpty` and
`.isNotEmpty`. Use the one that doesn't require you to negate the result.

相反，Dart 提供了更加高效率和易用的 getter 函式：`.isEmpty` 和`.isNotEmpty`。
使用這些函式並不需要對結果再次取非。

{:.good}
<?code-excerpt "usage_good.dart (dont-use-length)"?>
{% prettify dart tag=pre+code %}
if (lunchBox.isEmpty) return 'so hungry...';
if (words.isNotEmpty) return words.join(' ');
{% endprettify %}

{:.bad}
<?code-excerpt "usage_bad.dart (dont-use-length)"?>
{% prettify dart tag=pre+code %}
if (lunchBox.length == 0) return 'so hungry...';
if (!words.isEmpty) return words.join(' ');
{% endprettify %}

### AVOID using `Iterable.forEach()` with a function literal.

### **避免** 在 `Iterable.forEach()` 中使用字面量函式。

{% include linter-rule-mention.md rule="avoid_function_literals_in_foreach_calls" %}

`forEach()` functions are widely used in JavaScript because the built in
`for-in` loop doesn't do what you usually want. In Dart, if you want to iterate
over a sequence, the idiomatic way to do that is using a loop.

`forEach()` 函式在 JavaScript 中被廣泛使用，
這因為內建的 `for-in` 迴圈通常不能達到你想要的效果。
在Dart中，如果要對序列進行迭代，慣用的方式是使用迴圈。

{:.good}
<?code-excerpt "usage_good.dart (avoid-forEach)"?>
{% prettify dart tag=pre+code %}
for (final person in people) {
  ...
}
{% endprettify %}

{:.bad}
<?code-excerpt "usage_bad.dart (avoid-forEach)"?>
{% prettify dart tag=pre+code %}
people.forEach((person) {
  ...
});
{% endprettify %}

Note that this guideline specifically says "function *literal*". If you want to
invoke some *already existing* function on each element, `forEach()` is fine.

例外情況是，如果要執行的操作是呼叫一些已存在的並且將每個元素作為引數的函式，
在這種情況下，`forEach()` 是很方便的。

{:.good}
<?code-excerpt "usage_good.dart (forEach-over-func)"?>
{% prettify dart tag=pre+code %}
people.forEach(print);
{% endprettify %}

Also note that it's always OK to use `Map.forEach()`. Maps aren't iterable, so
this guideline doesn't apply.

您可以呼叫 `Map.forEach()`。Map 是不可迭代的，所以該準則對它無效。

### DON'T use `List.from()` unless you intend to change the type of the result.

### **不要** 使用 `List.from()` 除非想修改結果的型別。

Given an Iterable, there are two obvious ways to produce a new List that
contains the same elements:

給定一個可迭代的物件，有兩種常見方式來產生一個包含相同元素的 list：

<?code-excerpt "../../test/effective_dart_test.dart (list-from-1)"?>
{% prettify dart tag=pre+code %}
var copy1 = iterable.toList();
var copy2 = List.from(iterable);
{% endprettify %}

The obvious difference is that the first one is shorter. The *important*
difference is that the first one preserves the type argument of the original
object:

明顯的區別是前一個更短。
更*重要*的區別在於第一個保留了原始物件的型別引數：

{:.good}
<?code-excerpt "../../test/effective_dart_test.dart (list-from-good)"?>
{% prettify dart tag=pre+code %}
// Creates a List<int>:
var iterable = [1, 2, 3];

// Prints "List<int>":
print(iterable.toList().runtimeType);
{% endprettify %}

{:.bad}
<?code-excerpt "../../test/effective_dart_test.dart (list-from-bad)"?>
{% prettify dart tag=pre+code %}
// Creates a List<int>:
var iterable = [1, 2, 3];

// Prints "List<dynamic>":
print(List.from(iterable).runtimeType);
{% endprettify %}

If you *want* to change the type, then calling `List.from()` is useful:

如果你*想要*改變型別，那麼可以呼叫 `List.from()` ：

{:.good}
<?code-excerpt "../../test/effective_dart_test.dart (list-from-3)"?>
{% prettify dart tag=pre+code %}
var numbers = [1, 2.3, 4]; // List<num>.
numbers.removeAt(1); // Now it only contains integers.
var ints = List<int>.from(numbers);
{% endprettify %}

But if your goal is just to copy the iterable and preserve its original type, or
you don't care about the type, then use `toList()`.

但是如果你的目的只是複製可迭代物件並且保留元素原始型別，
或者並不在乎型別，那麼請使用 `toList()`。


### DO use `whereType()` to filter a collection by type.

### **要** 使用 `whereType()` 按型別過濾集合。

{% include linter-rule-mention.md rule="prefer_iterable_wheretype" %}

Let's say you have a list containing a mixture of objects, and you want to get
just the integers out of it. You could use `where()` like this:

假設你有一個 list 裡面包含了多種型別的物件，
但是你指向從它裡面獲取整型型別的資料。
那麼你可以像下面這樣使用 `where()` ：

{:.bad}
<?code-excerpt "usage_bad.dart (where-type)"?>
{% prettify dart tag=pre+code %}
var objects = [1, 'a', 2, 'b', 3];
var ints = objects.where((e) => e is int);
{% endprettify %}

This is verbose, but, worse, it returns an iterable whose type probably isn't
what you want. In the example here, it returns an `Iterable<Object>` even though
you likely want an `Iterable<int>` since that's the type you're filtering it to.

這個很羅嗦，但是更糟糕的是，它返回的可迭代物件型別可能並不是你想要的。
在上面的例子中，雖然你想得到一個 `Iterable<int>`，然而它返回了一個 `Iterable<Object>`，
這是因為，這是你過濾後得到的型別。

Sometimes you see code that "corrects" the above error by adding `cast()`:

有時候你會看到透過新增 `cast()` 來“修正”上面的錯誤：

{:.bad}
<?code-excerpt "usage_bad.dart (where-type-2)"?>
{% prettify dart tag=pre+code %}
var objects = [1, 'a', 2, 'b', 3];
var ints = objects.where((e) => e is int).cast<int>();
{% endprettify %}

That's verbose and causes two wrappers to be created, with two layers of
indirection and redundant runtime checking. Fortunately, the core library has
the [`whereType()`][where-type] method for this exact use case:

程式碼冗長，並導致建立了兩個包裝器，獲取元素物件要間接透過兩層，並進行兩次多餘的執行時期檢查。
幸運的是，對於這個使用案例，核心函式庫提供了 [`whereType()`][where-type] 方法：

[where-type]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Iterable/whereType.html

{:.good}
<?code-excerpt "../../test/effective_dart_test.dart (whereType)"?>
{% prettify dart tag=pre+code %}
var objects = [1, 'a', 2, 'b', 3];
var ints = objects.whereType<int>();
{% endprettify %}

Using `whereType()` is concise, produces an [Iterable][] of the desired type,
and has no unnecessary levels of wrapping.

使用 `whereType()` 簡潔，
產生所需的 [Iterable][]（可迭代）型別，
並且沒有不必要的層級包裝。


### DON'T use `cast()` when a nearby operation will do.

### **不要** 使用 `cast()`，如果有更合適的方法。

Often when you're dealing with an iterable or stream, you perform several
transformations on it. At the end, you want to produce an object with a certain
type argument. Instead of tacking on a call to `cast()`, see if one of the
existing transformations can change the type.

通常，當處理可迭代物件或 stream 時，
你可以對其執行多次轉換。
最後，產生所希望的具有特定型別引數的物件。
嘗試檢視是否有已有的轉換方法來改變型別，而不是去掉用 `cast()` 。
而不是呼叫 `cast()`，看看是否有一個現有的轉換可以改變型別。

If you're already calling `toList()`, replace that with a call to
[`List<T>.from()`][list-from] where `T` is the type of resulting list you want.

如果你已經使用了 `toList()` ，那麼請使用 [`List<T>.from()`][list-from] 替換，
這裡的 `T` 是你想要的返回值的型別。

[list-from]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/List/List.from.html

{:.good}
<?code-excerpt "usage_good.dart (cast-list)"?>
{% prettify dart tag=pre+code %}
var stuff = <dynamic>[1, 2];
var ints = List<int>.from(stuff);
{% endprettify %}

{:.bad}
<?code-excerpt "usage_bad.dart (cast-list)"?>
{% prettify dart tag=pre+code %}
var stuff = <dynamic>[1, 2];
var ints = stuff.toList().cast<int>();
{% endprettify %}

If you are calling `map()`, give it an explicit type argument so that it
produces an iterable of the desired type. Type inference often picks the correct
type for you based on the function you pass to `map()`, but sometimes you need
to be explicit.

如果你正在呼叫 `map()` ，給它一個顯式的型別引數，
這樣它就能產生一個所需型別的可迭代物件。
型別推斷通常根據傳遞給 `map()` 的函式選擇出正確的型別，
但有的時候需要明確指明。

{:.good}
<?code-excerpt "usage_good.dart (cast-map)" replace="/\(n as int\)/n/g"?>
{% prettify dart tag=pre+code %}
var stuff = <dynamic>[1, 2];
var reciprocals = stuff.map<double>((n) => 1 / n);
{% endprettify %}

{:.bad}
<?code-excerpt "usage_bad.dart (cast-map)" replace="/\(n as int\)/n/g"?>
{% prettify dart tag=pre+code %}
var stuff = <dynamic>[1, 2];
var reciprocals = stuff.map((n) => 1 / n).cast<double>();
{% endprettify %}


### AVOID using `cast()`.

### **避免** 使用 `cast()` 。

This is the softer generalization of the previous rule. Sometimes there is no
nearby operation you can use to fix the type of some object. Even then, when
possible avoid using `cast()` to "change" a collection's type.

這是對先前規則的一個寬鬆的定義。
有些時候，並沒有合適的方式來修改物件型別，即便如此，
也應該儘可能的避免使用 `cast()` 來“改變”集合中元素的型別。

Prefer any of these options instead:

推薦使用下面的方式來替代：

*   **Create it with the right type.** Change the code where the collection is
    first created so that it has the right type.

    **用恰當的型別建立集合。** 修改集合被首次建立時的程式碼，
    為集合提供有一個恰當的型別。

*   **Cast the elements on access.** If you immediately iterate over the
    collection, cast each element inside the iteration.

    **在存取元素時進行 cast 操作。** 如果要立即對集合進行迭代，
    在迭代內部 cast 每個元素。

*   **Eagerly cast using `List.from()`.** If you'll eventually access most of
    the elements in the collection, and you don't need the object to be backed
    by the original live object, convert it using `List.from()`.

    **逼不得已進行 cast，請使用 `List.from()` 。** 
    如果最終你會使用到集合中的大部分元素，並且不需要物件還原到原始的物件型別，
    使用 `List.from()` 來轉換它。

*   The `cast()` method returns a lazy collection that checks the element type
    on *every operation*. If you perform only a few operations on only a few
    elements, that laziness can be good. But in many cases, the overhead of lazy
    validation and of wrapping outweighs the benefits.

    `cast()` 方法返回一個惰性集合 (lazy collection) ，*每個操作*都會對元素進行檢查。
    如果只對少數元素執行少量操作，那麼這種惰性方式就非常合適。
    但在許多情況下，惰性驗證和包裹 (wrapping) 所產生的開銷已經超過了它們所帶來的好處。

Here is an example of **creating it with the right type:**

下面是 **用恰當的型別建立集合** 的範例：

{:.good}
<?code-excerpt "usage_good.dart (cast-at-create)"?>
{% prettify dart tag=pre+code %}
List<int> singletonList(int value) {
  var list = <int>[];
  list.add(value);
  return list;
}
{% endprettify %}

{:.bad}
<?code-excerpt "usage_bad.dart (cast-at-create)"?>
{% prettify dart tag=pre+code %}
List<int> singletonList(int value) {
  var list = []; // List<dynamic>.
  list.add(value);
  return list.cast<int>();
}
{% endprettify %}

Here is **casting each element on access:**

下面是 **在存取元素時進行 cast 操作** 的範例：

{:.good}
<?code-excerpt "usage_good.dart (cast-iterate)" replace="/\(n as int\)/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
void printEvens(List<Object> objects) {
  // We happen to know the list only contains ints.
  for (final n in objects) {
    if ([!(n as int)!].isEven) print(n);
  }
}
{% endprettify %}

{:.bad}
<?code-excerpt "usage_bad.dart (cast-iterate)"?>
{% prettify dart tag=pre+code %}
void printEvens(List<Object> objects) {
  // We happen to know the list only contains ints.
  for (final n in objects.cast<int>()) {
    if (n.isEven) print(n);
  }
}
{% endprettify %}

Here is **casting eagerly using `List.from()`:**

下面是 **使用 `List.from()` 進行 cast 操作** 的範例：

{:.good}
<?code-excerpt "usage_good.dart (cast-from)"?>
{% prettify dart tag=pre+code %}
int median(List<Object> objects) {
  // We happen to know the list only contains ints.
  var ints = List<int>.from(objects);
  ints.sort();
  return ints[ints.length ~/ 2];
}
{% endprettify %}

{:.bad}
<?code-excerpt "usage_bad.dart (cast-from)"?>
{% prettify dart tag=pre+code %}
int median(List<Object> objects) {
  // We happen to know the list only contains ints.
  var ints = objects.cast<int>();
  ints.sort();
  return ints[ints.length ~/ 2];
}
{% endprettify %}

These alternatives don't always work, of course, and sometimes `cast()` is the
right answer. But consider that method a little risky and undesirable—it
can be slow and may fail at runtime if you aren't careful.

當然，這些替代方案並不總能解決問題，顯然，這時候就應該選擇 `cast()` 方式了。
但是考慮到這種方式的風險和缺點——如果使用不當，可能會導致執行緩慢和執行失敗。


## Functions

## 函式

In Dart, even functions are objects. Here are some best practices
involving functions.

在 Dart 中，就連函式也是物件。以下是一些涉及函式的最佳實踐。


### DO use a function declaration to bind a function to a name.

### **要** 使用函式宣告的方式為函式繫結名稱。

{% include linter-rule-mention.md rule="prefer_function_declarations_over_variables" %}

Modern languages have realized how useful local nested functions and closures
are. It's common to have a function defined inside another one. In many cases,
this function is used as a callback immediately and doesn't need a name. A
function expression is great for that.

現代語言已經意識到本地巢狀(Nesting)函式和閉套件的益處。
在一個函式中定義另一個函式非常常見。
在許多情況下，這些函式被立即執行並返回結果，而且不需要名字。
這種情況下非常適合使用函式表示式來實現。

But, if you do need to give it a name, use a function declaration statement
instead of binding a lambda to a variable.

但是，如果你確實需要給方法一個名字，請使用方法定義而不是把
lambda 賦值給一個變數。

{:.good}
<?code-excerpt "usage_good.dart (func-decl)"?>
{% prettify dart tag=pre+code %}
void main() {
  void localFunction() {
    ...
  }
}
{% endprettify %}

{:.bad}
<?code-excerpt "usage_bad.dart (func-decl)"?>
{% prettify dart tag=pre+code %}
void main() {
  var localFunction = () {
    ...
  };
}
{% endprettify %}

### DON'T create a lambda when a tear-off will do.

### **不要** 使用 lambda 表示式來替代 tear-off。

{% include linter-rule-mention.md rule="unnecessary_lambdas" %}

When you refer to a function, method, or named constructor but omit the
parentheses, Dart creates a _tear-off_—a closure that takes the same
parameters as the function and invokes the underlying function when you call it.
If all you need is a closure that invokes a named function with the same
parameters as the closure accepts, don't manually wrap the call in a lambda.

如果你參考了一個函式、方法或命名構造，但省略了括號，Dart 會嘗試
**tear-off**&mdash;&mdash;在呼叫時使用同樣的引數對對應的方法建立閉套件。
如果你需要的僅僅是一個參考，請不要利用 lambda 手動包裝。

如果你有一個方法，這個方法呼叫了引數相同的另一個方法。
那麼，你不需要人為將這個方法包裝到一個 lambda 表示式中。

{:.good}
<?code-excerpt "usage_good.dart (use-tear-off)"?>
{% prettify dart tag=pre+code %}
var charCodes = [68, 97, 114, 116];
var buffer = StringBuffer();

// Function:
charCodes.forEach(print);

// Method:
charCodes.forEach(buffer.write);

// Named constructor:
var strings = charCodes.map(String.fromCharCode);

// Unnamed constructor:
var buffers = charCodes.map(StringBuffer.new);
{% endprettify %}

{:.bad}
<?code-excerpt "usage_bad.dart (use-tear-off)"?>
{% prettify dart tag=pre+code %}
var charCodes = [68, 97, 114, 116];
var buffer = StringBuffer();

// Function:
charCodes.forEach((code) {
  print(code);
});

// Method:
charCodes.forEach((code) {
  buffer.write(code);
});

// Named constructor:
var strings = charCodes.map((code) => String.fromCharCode(code));

// Unnamed constructor:
var buffers = charCodes.map((code) => StringBuffer(code));
{% endprettify %}

### DO use `=` to separate a named parameter from its default value.

### **要** 使用 `=` 來分隔引數名和引數預設值。

{% include linter-rule-mention.md rule="prefer_equal_for_default_values" %}

For legacy reasons, Dart allows both `:` and `=` as the default value separator
for named parameters. For consistency with optional positional parameters, use
`=`.

由於遺留原因，Dart 同時支援 `:` 和 `=` 作為引數名和預設值的分隔符。
為了與可選的位置引數保持一致，請使用 `=` 。

{:.good}
<?code-excerpt "usage_good.dart (default-separator)"?>
{% prettify dart tag=pre+code %}
void insert(Object item, {int at = 0}) { ... }
{% endprettify %}

{:.bad}
<?code-excerpt "usage_bad.dart (default-separator)"?>
{% prettify dart tag=pre+code %}
void insert(Object item, {int at: 0}) { ... }
{% endprettify %}

## Variables

## 變數

The following best practices describe how to best use variables in Dart.

### DO follow a consistent rule for `var` and `final` on local variables.

Most local variables shouldn't have type annotations and should be declared
using just `var` or `final`. There are two rules in wide use for when to use one
or the other:

*   Use `final` for local variables that are not reassigned and `var` for those
    that are.

*   Use `var` for all local variables, even ones that aren't reassigned. Never use
    `final` for locals. (Using `final` for fields and top-level variables is
    still encouraged, of course.)

Either rule is acceptable, but pick *one* and apply it consistently throughout
your code. That way when a reader sees `var`, they know whether it means that
the variable is assigned later in the function.


### AVOID storing what you can calculate.

### **避免** 儲存可計算的結果。

When designing a class, you often want to expose multiple views into the same
underlying state. Often you see code that calculates all of those views in the
constructor and then stores them:

在設計類別的時候，你常常希望暴露底層狀態的多個表現屬性。
常常你會發現在類別的建構函式中計算這些屬性，然後儲存起來：

{:.bad}
<?code-excerpt "usage_bad.dart (calc-vs-store1)"?>
{% prettify dart tag=pre+code %}
class Circle {
  double radius;
  double area;
  double circumference;

  Circle(double radius)
      : radius = radius,
        area = pi * radius * radius,
        circumference = pi * 2.0 * radius;
}
{% endprettify %}

This code has two things wrong with it. First, it's likely wasting memory. The
area and circumference, strictly speaking, are *caches*. They are stored
calculations that we could recalculate from other data we already have. They are
trading increased memory for reduced CPU usage. Do we know we have a performance
problem that merits that trade-off?

上面的程式碼有兩個不妥之處。首先，這樣浪費了記憶體。
嚴格來說面積和周長是*快取*資料。
他們儲存的結果可以透過已知的資料計算出來。
他們減少了 CPU 消耗卻增加了記憶體消耗。
我們還沒有權衡，到底存不存在效能問題？

Worse, the code is *wrong*. The problem with caches is *invalidation*—how
do you know when the cache is out of date and needs to be recalculated? Here, we
never do, even though `radius` is mutable. You can assign a different value and
the `area` and `circumference` will retain their previous, now incorrect values.

更糟糕的是，程式碼是錯誤的。
問題在於快取是無效的 —— 你如何知道快取何時會過期並且需要重新計算？
即便半徑是可變的，在這裡我們也永遠不會這樣做。
你可以賦一個不同的值，但面積和周長還是以前的值，現在的值是不正確的。

To correctly handle cache invalidation, we would need to do this:

為了正確處理快取失效，我們需要這樣做：

{:.bad}
<?code-excerpt "usage_bad.dart (calc-vs-store2)"?>
{% prettify dart tag=pre+code %}
class Circle {
  double _radius;
  double get radius => _radius;
  set radius(double value) {
    _radius = value;
    _recalculate();
  }

  double _area = 0.0;
  double get area => _area;

  double _circumference = 0.0;
  double get circumference => _circumference;

  Circle(this._radius) {
    _recalculate();
  }

  void _recalculate() {
    _area = pi * _radius * _radius;
    _circumference = pi * 2.0 * _radius;
  }
}
{% endprettify %}

That's an awful lot of code to write, maintain, debug, and read. Instead, your
first implementation should be:

這需要編寫、維護、除錯以及閱讀更多的程式碼。
如果你一開始這樣寫程式碼：

{:.good}
<?code-excerpt "usage_good.dart (calc-vs-store)"?>
{% prettify dart tag=pre+code %}
class Circle {
  double radius;

  Circle(this.radius);

  double get area => pi * radius * radius;
  double get circumference => pi * 2.0 * radius;
}
{% endprettify %}

This code is shorter, uses less memory, and is less error-prone. It stores the
minimal amount of data needed to represent the circle. There are no fields to
get out of sync because there is only a single source of truth.

上面的程式碼更加簡潔、使用更少的記憶體、減少出錯的可能性。
它儘可能少的儲存了表示圓所需要的資料。
這裡沒有欄位需要同步，因為這裡只有一個有效資料源。

In some cases, you may need to cache the result of a slow calculation, but only
do that after you know you have a performance problem, do it carefully, and
leave a comment explaining the optimization.

在某些情況下，當計算結果比較費時的時候可能需要快取，
但是隻應該在你只有你有這樣的效能問題的時候再去處理，
處理時要仔細，並留下掛關於最佳化的註釋。


## Members

## 成員

In Dart, objects have members which can be functions (methods) or data (instance
variables). The following best practices apply to an object's members.

在 Dart 中，物件成員可以是函式（方法）或資料（例項變數）。
下面是關於物件成員的最佳實踐。

### DON'T wrap a field in a getter and setter unnecessarily.

### **不要** 為欄位建立不必要的 getter 和 setter 方法。

{% include linter-rule-mention.md rule="unnecessary_getters_setters" %}

In Java and C#, it's common to hide all fields behind getters and setters (or
properties in C#), even if the implementation just forwards to the field. That
way, if you ever need to do more work in those members, you can without needing
to touch the call sites. This is because calling a getter method is different
than accessing a field in Java, and accessing a property isn't binary-compatible
with accessing a raw field in C#.

在 Java 和 C# 中，通常情況下會將所有的欄位隱藏到 getter 和 setter 方法中（在 C# 中被稱為屬性），
即使實現中僅僅是指向這些欄位。在這種方式下，即使你在這些成員上做多少的事情，你也不需要直接存取它們。
這是因為，在 Java 中，呼叫 getter 方法和直接存取欄位是不同的。
在 C# 中，存取屬性與存取欄位不是二進位制相容的。

Dart doesn't have this limitation. Fields and getters/setters are completely
indistinguishable. You can expose a field in a class and later wrap it in a
getter and setter without having to touch any code that uses that field.

Dart 不存在這個限制。欄位和 getter/setter 是完全無法區分的。
你可以在類中公開一個欄位，然後將其包裝在 getter 和 setter 中，
而不會影響任何使用該欄位的程式碼。

{:.good}
<?code-excerpt "usage_good.dart (dont-wrap-field)"?>
{% prettify dart tag=pre+code %}
class Box {
  Object? contents;
}
{% endprettify %}

{:.bad}
<?code-excerpt "usage_bad.dart (dont-wrap-field)"?>
{% prettify dart tag=pre+code %}
class Box {
  Object? _contents;
  Object? get contents => _contents;
  set contents(Object? value) {
    _contents = value;
  }
}
{% endprettify %}

### PREFER using a `final` field to make a read-only property.

### **推薦** 使用 `final` 關鍵字來建立唯讀屬性。

{% include linter-rule-mention.md rule="unnecessary_getters_setters" %}

If you have a field that outside code should be able to see but not assign to, a
simple solution that works in many cases is to simply mark it `final`.

如果一個變數對於外部程式碼來說只能讀取不能修改，
最簡單的做法就是使用 `final` 關鍵字來標記這個變數。

{:.good}
<?code-excerpt "usage_good.dart (final)"?>
{% prettify dart tag=pre+code %}
class Box {
  final contents = [];
}
{% endprettify %}

{:.bad}
<?code-excerpt "usage_bad.dart (final)"?>
{% prettify dart tag=pre+code %}
class Box {
  Object? _contents;
  Object? get contents => _contents;
}
{% endprettify %}

Of course, if you need to internally assign to the field outside of the
constructor, you may need to do the "private field, public getter" pattern, but
don't reach for that until you need to.

當然，如果你需要構造一個內部可以賦值，外部可以存取的欄位，
你可以需要這種“私有成員變數，公開存取函式”的模式，
但是，如非必要，請不要使用這種模式。


### CONSIDER using `=>` for simple members.

### **考慮** 對簡單成員使用 `=>` 。

{% include linter-rule-mention.md rule="prefer_expression_function_bodies" %}

In addition to using `=>` for function expressions, Dart also lets you define
members with it. That style is a good fit for simple members that just calculate
and return a value.

除了使用 `=>` 可以用作函式表示式以外，
Dart 還允許使用它來定義成員。
這種風格非常適合，僅進行計算並返回結果的簡單成員。

{:.good}
<?code-excerpt "usage_good.dart (use-arrow)"?>
{% prettify dart tag=pre+code %}
double get area => (right - left) * (bottom - top);

String capitalize(String name) =>
    '${name[0].toUpperCase()}${name.substring(1)}';
{% endprettify %}

People *writing* code seem to love `=>`, but it's very easy to abuse it and end
up with code that's hard to *read*. If your declaration is more than a couple of
lines or contains deeply nested expressions—cascades and conditional
operators are common offenders—do yourself and everyone who has to read
your code a favor and use a block body and some statements.

*編寫*程式碼的人似乎很喜歡 `=>` 語法，但是它很容易被濫用，最後導致程式碼不容易被*閱讀*。
如果你有很多行宣告或包含深層的巢狀(Nesting)表示式（級聯和條件運算子就是常見的罪魁禍首），
你以及其他人有誰會願意讀這樣的程式碼！
你應該換做使用程式碼塊和一些陳述式來實現。

{:.good}
<?code-excerpt "usage_good.dart (arrow-long)"?>
{% prettify dart tag=pre+code %}
Treasure? openChest(Chest chest, Point where) {
  if (_opened.containsKey(chest)) return null;

  var treasure = Treasure(where);
  treasure.addAll(chest.contents);
  _opened[chest] = treasure;
  return treasure;
}
{% endprettify %}

{:.bad}
<?code-excerpt "usage_bad.dart (arrow-long)"?>
{% prettify dart tag=pre+code %}
Treasure? openChest(Chest chest, Point where) => _opened.containsKey(chest)
    ? null
    : _opened[chest] = (Treasure(where)..addAll(chest.contents));
{% endprettify %}

You can also use `=>` on members that don't return a value. This is idiomatic
when a setter is small and has a corresponding getter that uses `=>`.

您還可以對不返回值的成員使用 `=>` 。 
這裡有個慣例，就是當 setter 和 getter 都比較簡單的時候使用 `=>` 。

{:.good}
<?code-excerpt "usage_good.dart (arrow-setter)"?>
{% prettify dart tag=pre+code %}
num get x => center.x;
set x(num value) => center = Point(value, center.y);
{% endprettify %}


### DON'T use `this.` except to redirect to a named constructor or to avoid shadowing. {#dont-use-this-when-not-needed-to-avoid-shadowing}

### **不要** 使用 `this.`，在重新導向命名函式和避免衝突的情況下除外。

{% include linter-rule-mention.md rule="unnecessary_this" %}

JavaScript requires an explicit `this.` to refer to members on the object whose
method is currently being executed, but Dart—like C++, Java, and
C#—doesn't have that limitation.

JavaScript 需要使用 `this.` 來參考物件的成員變數，
但是 Dart&mdash;和 C++, Java, 以及C#&mdash;沒有這種限制。

There are only two times you need to use `this.`. One is when a local variable
with the same name shadows the member you want to access:

只有當局部變數和成員變數名字一樣的時候，你才需要使用 `this.` 來存取成員變數。
只有兩種情況需要使用 `this.`，
其中一種情況是要存取的區域變數和成員變數命名一樣的時候：

{:.bad}
<?code-excerpt "usage_bad.dart (this-dot)"?>
{% prettify dart tag=pre+code %}
class Box {
  Object? value;

  void clear() {
    this.update(null);
  }

  void update(Object? value) {
    this.value = value;
  }
}
{% endprettify %}

{:.good}
<?code-excerpt "usage_good.dart (this-dot)"?>
{% prettify dart tag=pre+code %}
class Box {
  Object? value;

  void clear() {
    update(null);
  }

  void update(Object? value) {
    this.value = value;
  }
}
{% endprettify %}

The other time to use `this.` is when redirecting to a named constructor:

另一種使用 `this.` 的情況是在重新導向到一個命名函式的時候：

{:.bad}
<?code-excerpt "usage_bad.dart (this-dot-constructor)"?>
{% prettify dart tag=pre+code %}
class ShadeOfGray {
  final int brightness;

  ShadeOfGray(int val) : brightness = val;

  ShadeOfGray.black() : this(0);

  // This won't parse or compile!
  // ShadeOfGray.alsoBlack() : black();
}
{% endprettify %}

{:.good}
<?code-excerpt "usage_good.dart (this-dot-constructor)"?>
{% prettify dart tag=pre+code %}
class ShadeOfGray {
  final int brightness;

  ShadeOfGray(int val) : brightness = val;

  ShadeOfGray.black() : this(0);

  // But now it will!
  ShadeOfGray.alsoBlack() : this.black();
}
{% endprettify %}

Note that constructor parameters never shadow fields in constructor initializer
lists:

注意，建構函式初始化列表中的欄位有永遠不會與建構函式引數列表引數產生衝突。

{:.good}
<?code-excerpt "usage_good.dart (param-dont-shadow-field-ctr-init)"?>
{% prettify dart tag=pre+code %}
class Box extends BaseBox {
  Object? value;

  Box(Object? value)
      : value = value,
        super(value);
}
{% endprettify %}

This looks surprising, but works like you want. Fortunately, code like this is
relatively rare thanks to initializing formals and super initializers.

這看起來很令人驚訝，但是實際結果是你想要的。
幸運的是，由於初始化規則的特殊性，上面的程式碼很少見到。


### DO initialize fields at their declaration when possible.

### **要** 儘可能的在定義變數的時候初始化變數值。

If a field doesn't depend on any constructor parameters, it can and should be
initialized at its declaration. It takes less code and avoids duplication when
the class has multiple constructors.

{:.bad}
<?code-excerpt "usage_bad.dart (field-init-at-decl)"?>
{% prettify dart tag=pre+code %}
class ProfileMark {
  final String name;
  final DateTime start;

  ProfileMark(this.name) : start = DateTime.now();
  ProfileMark.unnamed()
      : name = '',
        start = DateTime.now();
}
{% endprettify %}

{:.good}
<?code-excerpt "usage_good.dart (field-init-at-decl)"?>
{% prettify dart tag=pre+code %}
class ProfileMark {
  final String name;
  final DateTime start = DateTime.now();

  ProfileMark(this.name);
  ProfileMark.unnamed() : name = '';
}
{% endprettify %}

Some fields can't be initialized at their declarations because they need to reference
`this`—to use other fields or call methods, for example. However, if the
field is marked `late`, then the initializer *can* access `this`.

Of course, if a field depends on constructor parameters, or is initialized
differently by different constructors, then this guideline does not apply.

當然，對於變數取值依賴建構函式引數的情況以及不同的建構函式取值也不一樣的情況，
則不適合本條規則。


## Constructors

## 建構函式

The following best practices apply to declaring constructors for a class.

下面對於類別的建構函式的最佳實踐。

### DO use initializing formals when possible.

### **要** 儘可能的使用初始化形式。

{% include linter-rule-mention.md rule="prefer_initializing_formals" %}

Many fields are initialized directly from a constructor parameter, like:

許多欄位直接使用建構函式引數來初始化，如：

{:.bad}
<?code-excerpt "usage_bad.dart (field-init-as-param)"?>
{% prettify dart tag=pre+code %}
class Point {
  double x, y;
  Point(double x, double y)
      : x = x,
        y = y;
}
{% endprettify %}

We've got to type `x` _four_ times here to define a field. We can do better:

為了初始化一個欄位，我們需要反覆寫下 `x` **四**次。
使用下面的方式會更好：

{:.good}
<?code-excerpt "usage_good.dart (field-init-as-param)"?>
{% prettify dart tag=pre+code %}
class Point {
  double x, y;
  Point(this.x, this.y);
}
{% endprettify %}

This `this.` syntax before a constructor parameter is called an "initializing
formal". You can't always take advantage of it. Sometimes you want to have a
named parameter whose name doesn't match the name of the field you are
initializing. But when you *can* use initializing formals, you *should*.

### DON'T use `late` when a constructor initializer list will do.

Sound null safety requires Dart to ensure that a non-nullable field is
initialized before it can be read. Since fields can be read inside the
constructor body, this means you get an error if you don't initialize a
non-nullable field before the body runs.

You can make this error go away by marking the field `late`. That turns the
compile-time error into a *runtime* error if you access the field before it is
initialized. That's what you need in some cases, but often the right fix is to
initialize the field in the constructor initializer list:

如果建構函式引數使用 `this.` 的方式來初始化欄位，
這時引數的型別被認為和欄位型別相同。

{:.good}
<?code-excerpt "usage_good.dart (late-init-list)"?>
{% prettify dart tag=pre+code %}
class Point {
  double x, y;
  Point.polar(double theta, double radius)
      : x = cos(theta) * radius,
        y = sin(theta) * radius;
}
{% endprettify %}

{:.bad}
<?code-excerpt "usage_bad.dart (late-init-list)"?>
{% prettify dart tag=pre+code %}
class Point {
  late double x, y;
  Point.polar(double theta, double radius) {
    x = cos(theta) * radius;
    y = sin(theta) * radius;
  }
}
{% endprettify %}


The initializer list gives you access to constructor parameters and lets you
initialize fields before they can be read. So, if it's possible to use an initializer list,
that's better than making the field `late` and losing some static safety and
performance.


### DO use `;` instead of `{}` for empty constructor bodies.

### **要** 用 `;` 來替代空的建構函式體 `{}`。

{% include linter-rule-mention.md rule="empty_constructor_bodies" %}

In Dart, a constructor with an empty body can be terminated with just a
semicolon. (In fact, it's required for const constructors.)

在 Dart 中，沒有具體函式體的建構函式可以使用分號結尾。
（事實上，這是不可變建構函式的要求。）

{:.good}
<?code-excerpt "usage_good.dart (semicolon-for-empty-body)"?>
{% prettify dart tag=pre+code %}
class Point {
  double x, y;
  Point(this.x, this.y);
}
{% endprettify %}

{:.bad}
<?code-excerpt "usage_bad.dart (semicolon-for-empty-body)"?>
{% prettify dart tag=pre+code %}
class Point {
  double x, y;
  Point(this.x, this.y) {}
}
{% endprettify %}

### DON'T use `new`.

### **不要** 使用 `new`。

{% include linter-rule-mention.md rule="unnecessary_new" %}

Dart 2 makes the `new` keyword optional. Even in Dart 1, its meaning was never
clear because factory constructors mean a `new` invocation may still not
actually return a new object.

Dart 2 `new` 關鍵字成為可選項。
即使在Dart 1中，其含義也從未明確過，
因為在工廠建構函式中，呼叫 `new` 可能並不意味著一定會返回一個新物件。

The language still permits `new` in order to make migration less painful, but
consider it deprecated and remove it from your code.

為了減少程式碼遷移時的痛苦， Dart 語言仍允許使用 `new` 關鍵字，
但請考在你的程式碼中棄用和刪除 `new`。

{:.good}
<?code-excerpt "usage_good.dart (no-new)"?>
{% prettify dart tag=pre+code %}
Widget build(BuildContext context) {
  return Row(
    children: [
      RaisedButton(
        child: Text('Increment'),
      ),
      Text('Click!'),
    ],
  );
}
{% endprettify %}

{:.bad}
<?code-excerpt "usage_bad.dart (no-new)" replace="/new/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
Widget build(BuildContext context) {
  return [!new!] Row(
    children: [
      [!new!] RaisedButton(
        child: [!new!] Text('Increment'),
      ),
      [!new!] Text('Click!'),
    ],
  );
}
{% endprettify %}


### DON'T use `const` redundantly.

### **不要** 冗餘地使用 `const` 。

{% include linter-rule-mention.md rule="unnecessary_const" %}

In contexts where an expression *must* be constant, the `const` keyword is
implicit, doesn't need to be written, and shouldn't. Those contexts are any
expression inside:

在表示式一定是常量的上下文中，`const` 關鍵字是隱含的，不需要寫，也不應該。
這裡包括：

* A const collection literal.

  一個字面量常量集合。

* A const constructor call

  呼叫一個常量建構函式。

* A metadata annotation.

  元資料註解。

* The initializer for a const variable declaration.

  一個常量宣告的初始化方法。

* A switch case expression—the part right after `case` before the `:`, not
  the body of the case.

  switch case 表示式—— `case` 和 `:` 中間的部分，不是 case 執行體。


(Default values are not included in this list because future versions of Dart
may support non-const default values.)

（預設值並不包含在這個列表中，因為在 Dart 將來的版本中可能會在支援非常量的預設值。）

Basically, any place where it would be an error to write `new` instead of
`const`, Dart 2 allows you to omit the `const`.

基本上，任何地方用 `new` 替代 `const` 的寫法都是錯的，
因為 Dart 2 中允許省略 `const` 。

{:.good}
<?code-excerpt "usage_good.dart (no-const)"?>
{% prettify dart tag=pre+code %}
const primaryColors = [
  Color('red', [255, 0, 0]),
  Color('green', [0, 255, 0]),
  Color('blue', [0, 0, 255]),
];
{% endprettify %}

{:.bad}
<?code-excerpt "usage_bad.dart (no-const)" replace="/ (const)/ [!$1!]/g"?>
{% prettify dart tag=pre+code %}
const primaryColors = [!const!] [
  [!const!] Color('red', [!const!] [255, 0, 0]),
  [!const!] Color('green', [!const!] [0, 255, 0]),
  [!const!] Color('blue', [!const!] [0, 0, 255]),
];
{% endprettify %}

## Error handling

## 錯誤處理

Dart uses exceptions when an error occurs in your program. The following
best practices apply to catching and throwing exceptions.

Dart 使用例外來表示程式執行錯誤。
下面是關於如何捕獲和丟擲例外的最佳實踐。

### AVOID catches without `on` clauses.

### **避免** 使用沒有 `on` 陳述式的 catch。

{% include linter-rule-mention.md rule="avoid_catches_without_on_clauses" %}

A catch clause with no `on` qualifier catches *anything* thrown by the code in
the try block. [Pokémon exception handling][pokemon] is very likely not what you
want. Does your code correctly handle [StackOverflowError][] or
[OutOfMemoryError][]? If you incorrectly pass the wrong argument to a method in
that try block do you want to have your debugger point you to the mistake or
would you rather that helpful [ArgumentError][] get swallowed? Do you want any
`assert()` statements inside that code to effectively vanish since you're
catching the thrown [AssertionError][]s?

沒有 `on` 限定的 catch 陳述式會捕獲 try 程式碼塊中丟擲的*任何*例外。
[Pokémon exception handling][pokemon] 可能並不是你想要的。
你的程式碼是否正確的處理 [StackOverflowError][] 或者 [OutOfMemoryError][] 例外？
如果你使用錯誤的引數呼叫函式，你是期望偵錯程式定位出你的錯誤使用情況還是，
把這個有用的 [ArgumentError][] 給吞噬了？
由於你捕獲了 [AssertionError][] 例外，
導致所有 try 塊內的 `assert()` 陳述式都失效了，這是你需要的結果嗎？

The answer is probably "no", in which case you should filter the types you
catch. In most cases, you should have an `on` clause that limits you to the
kinds of runtime failures you are aware of and are correctly handling.

答案和可能是 "no"，在這種情況下，您應該過濾掉捕獲的型別。
在大多數情況下，您應該有一個 `on` 子句，
這樣它能夠捕獲程式在執行時你所關注的限定型別的例外並進行恰當處理。

In rare cases, you may wish to catch any runtime error. This is usually in
framework or low-level code that tries to insulate arbitrary application code
from causing problems. Even here, it is usually better to catch [Exception][]
than to catch all types. Exception is the base class for all *runtime* errors
and excludes errors that indicate *programmatic* bugs in the code.


### DON'T discard errors from catches without `on` clauses.

### **不要** 丟棄沒有使用 `on` 陳述式捕獲的例外。

If you really do feel you need to catch *everything* that can be thrown from a
region of code, *do something* with what you catch. Log it, display it to the
user or rethrow it, but do not silently discard it.

如果你真的期望捕獲一段程式碼內的 *所有* 例外，
請*在捕獲例外的地方做些事情*。 記錄下來並顯示給使用者，
或者重新丟擲 (rethrow) 例外資訊，記得不要默默的丟棄該例外資訊。


### DO throw objects that implement `Error` only for programmatic errors.

### **要** 只在代表程式設計錯誤的情況下才丟擲實現了 `Error` 的例外。

The [Error][] class is the base class for *programmatic* errors. When an object
of that type or one of its subinterfaces like [ArgumentError][] is thrown, it
means there is a *bug* in your code. When your API wants to report to a caller
that it is being used incorrectly throwing an Error sends that signal clearly.

[Error][] 類是所有 *編碼* 錯誤的基底類別。當一個該型別或者其子類別型，
例如 [ArgumentError][] 物件被丟擲了，這意味著是你程式碼中的一個 *bug*。
當你的 API 想要告訴呼叫者使用錯誤的時候可以丟擲一個 Error 來表明你的意圖。


Conversely, if the exception is some kind of runtime failure that doesn't
indicate a bug in the code, then throwing an Error is misleading. Instead, throw
one of the core Exception classes or some other type.

同樣的，如果一個例外表示為執行時例外而不是程式碼 bug， 則丟擲 Error 則會誤導呼叫者。
應該丟擲核心定義的 Exception 類或者其他型別。


### DON'T explicitly catch `Error` or types that implement it.

### **不要** 顯示的捕獲 `Error` 或者其子類別。

{% include linter-rule-mention.md rule="avoid_catching_errors" %}

This follows from the above. Since an Error indicates a bug in your code, it
should unwind the entire callstack, halt the program, and print a stack trace so
you can locate and fix the bug.

本條銜接上一條的內容。既然 Error 表示程式碼中的 bug，
應該展開整個呼叫堆疊，暫停程式並列印堆疊追蹤，以便找到錯誤並修復。

Catching errors of these types breaks that process and masks the bug. Instead of
*adding* error-handling code to deal with this exception after the fact, go back
and fix the code that is causing it to be thrown in the first place.

捕獲這類錯誤打破了處理流程並且程式碼中有 bug。
不要在這裡使用錯誤處理程式碼，而是需要到導致該錯誤出現的地方修復你的程式碼。


### DO use `rethrow` to rethrow a caught exception.

### **要** 使用 `rethrow` 來重新丟擲捕獲的例外。

{% include linter-rule-mention.md rule="use_rethrow_when_possible" %}

If you decide to rethrow an exception, prefer using the `rethrow` statement
instead of throwing the same exception object using `throw`.
`rethrow` preserves the original stack trace of the exception. `throw` on the
other hand resets the stack trace to the last thrown position.

如果你想重新丟擲一個例外，推薦使用 `rethrow` 陳述式。
`rethrow` 保留了原來的例外堆疊資訊。 
而 `throw` 會把例外堆疊資訊重置為最後丟擲的位置。

{:.bad}
<?code-excerpt "usage_bad.dart (rethrow)"?>
{% prettify dart tag=pre+code %}
try {
  somethingRisky();
} catch (e) {
  if (!canHandle(e)) throw e;
  handle(e);
}
{% endprettify %}

{:.good}
<?code-excerpt "usage_good.dart (rethrow)" replace="/rethrow/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
try {
  somethingRisky();
} catch (e) {
  if (!canHandle(e)) [!rethrow!];
  handle(e);
}
{% endprettify %}


## Asynchrony

## 非同步

Dart has several language features to support asynchronous programming.
The following best practices apply to asynchronous coding.

Dart 具有幾個語言特性來支援非同步程式設計。
下面是針對非同步程式設計的最佳實踐。

### PREFER async/await over using raw futures.

### **推薦** 使用 async/await 而不是直接使用底層的特性。

Asynchronous code is notoriously hard to read and debug, even when using a nice
abstraction like futures. The `async`/`await` syntax improves readability and
lets you use all of the Dart control flow structures within your async code.

顯式的非同步程式碼是非常難以閱讀和除錯的，
即使使用很好的抽象（比如 future）也是如此。
這就是為何 Dart 提供了 `async`/`await`。
這樣可以顯著的提高程式碼的可讀性並且讓你可以在非同步程式碼中使用語言提供的所有流程控制陳述式。

{:.good}
<?code-excerpt "usage_good.dart (async-await)" replace="/async|await/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
Future<int> countActivePlayers(String teamName) [!async!] {
  try {
    var team = [!await!] downloadTeam(teamName);
    if (team == null) return 0;

    var players = [!await!] team.roster;
    return players.where((player) => player.isActive).length;
  } catch (e) {
    log.error(e);
    return 0;
  }
}
{% endprettify %}

{:.bad}
<?code-excerpt "usage_bad.dart (async-await)"?>
{% prettify dart tag=pre+code %}
Future<int> countActivePlayers(String teamName) {
  return downloadTeam(teamName).then((team) {
    if (team == null) return Future.value(0);

    return team.roster.then((players) {
      return players.where((player) => player.isActive).length;
    });
  }).catchError((e) {
    log.error(e);
    return 0;
  });
}
{% endprettify %}

### DON'T use `async` when it has no useful effect.

### **不要** 在沒有有用效果的情況下使用 `async` 。

It's easy to get in the habit of using `async` on any function that does
anything related to asynchrony. But in some cases, it's extraneous. If you can
omit the `async` without changing the behavior of the function, do so.

當成為習慣之後，你可能會在所有和非同步相關的函式使用 `async`。但是在有些情況下，
如果可以忽略 `async`  而不改變方法的行為，則應該這麼做：

{:.good}
<?code-excerpt "usage_good.dart (unnecessary-async)"?>
{% prettify dart tag=pre+code %}
Future<int> fastestBranch(Future<int> left, Future<int> right) {
  return Future.any([left, right]);
}
{% endprettify %}

{:.bad}
<?code-excerpt "usage_bad.dart (unnecessary-async)"?>
{% prettify dart tag=pre+code %}
Future<int> fastestBranch(Future<int> left, Future<int> right) async {
  return Future.any([left, right]);
}
{% endprettify %}

Cases where `async` *is* useful include:

下面這些情況 `async` 是有用的：

* You are using `await`. (This is the obvious one.)

  你使用了 `await`。 (這是一個很明顯的例子。)

* You are returning an error asynchronously. `async` and then `throw` is shorter
  than `return Future.error(...)`.

  你在非同步的丟擲一個例外。 `async` 然後 `throw` 比 `return new Future.error(...)` 要簡短很多。

* You are returning a value and you want it implicitly wrapped in a future.
  `async` is shorter than `Future.value(...)`.

  你在返回一個值，但是你希望他顯式的使用 Future。`async` 比 `Future.value(...)` 要簡短很多。

{:.good}
<?code-excerpt "usage_good.dart (async)"?>
{% prettify dart tag=pre+code %}
Future<void> usesAwait(Future<String> later) async {
  print(await later);
}

Future<void> asyncError() async {
  throw 'Error!';
}

Future<String> asyncValue() async => 'value';
{% endprettify %}

### CONSIDER using higher-order methods to transform a stream.

### **考慮** 使用高階函式來轉換事件流 (stream) 。

This parallels the above suggestion on iterables. Streams support many of the
same methods and also handle things like transmitting errors, closing, etc.
correctly.

### AVOID using Completer directly.

### **避免** 直接使用 Completer。

Many people new to asynchronous programming want to write code that produces a
future. The constructors in Future don't seem to fit their need so they
eventually find the Completer class and use that.

很多非同步程式設計的新手想要編寫產生一個 future 的程式碼。
而 Future 的建構函式看起來並不滿足他們的要求，
然後他們就發現 Completer 類並使用它：

{:.bad}
<?code-excerpt "usage_bad.dart (avoid-completer)"?>
{% prettify dart tag=pre+code %}
Future<bool> fileContainsBear(String path) {
  var completer = Completer<bool>();

  File(path).readAsString().then((contents) {
    completer.complete(contents.contains('bear'));
  });

  return completer.future;
}
{% endprettify %}

Completer is needed for two kinds of low-level code: new asynchronous
primitives, and interfacing with asynchronous code that doesn't use futures.
Most other code should use async/await or [`Future.then()`][then], because
they're clearer and make error handling easier.

Completer 是用於兩種底層程式碼的：
新的非同步原子操作和整合沒有使用 Future 的非同步程式碼。
大部分的程式碼都應該使用 async/await 或者 [`Future.then()`][then]，
這樣程式碼更加清晰並且例外處理更加容易。

[then]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-async/Future/then.html

{:.good}
<?code-excerpt "usage_good.dart (avoid-completer)"?>
{% prettify dart tag=pre+code %}
Future<bool> fileContainsBear(String path) {
  return File(path).readAsString().then((contents) {
    return contents.contains('bear');
  });
}
{% endprettify %}

{:.good}
<?code-excerpt "usage_good.dart (avoid-completer-alt)"?>
{% prettify dart tag=pre+code %}
Future<bool> fileContainsBear(String path) async {
  var contents = await File(path).readAsString();
  return contents.contains('bear');
}
{% endprettify %}


### DO test for `Future<T>` when disambiguating a `FutureOr<T>` whose type argument could be `Object`.

### **要** 使用 `Future<T>` 對 `FutureOr<T>` 引數進行測試，以消除引數可能是 `Object` 型別的歧義。

Before you can do anything useful with a `FutureOr<T>`, you typically need to do
an `is` check to see if you have a `Future<T>` or a bare `T`. If the type
argument is some specific type as in `FutureOr<int>`, it doesn't matter which
test you use, `is int` or `is Future<int>`. Either works because those two types
are disjoint.

在使用 `FutureOr<T>` 執行任何有用的操作之前，
通常需要做 `is` 檢查，來確定你擁有的是 `Future<T>` 還是一個空的 `T`。
如果型別引數是某個特定型別，如 `FutureOr <int>`，
使用 `is int` 或 `is Future<int>` 那種測試都可以。
兩者都有效，因為這兩種型別是不相交的。

However, if the value type is `Object` or a type parameter that could possibly
be instantiated with `Object`, then the two branches overlap. `Future<Object>`
itself implements `Object`, so `is Object` or `is T` where `T` is some type
parameter that could be instantiated with `Object` returns true even when the
object is a future. Instead, explicitly test for the `Future` case:

但是，如果值的型別是 `Object` 或者可能使用 `Object` 例項化的型別引數，這時要分兩種情況。
`Future<Object>` 本身繼承 `Object` ，使用 `is Object` 或 `is T` ，
其中 `T` 表示引數的型別，該引數可能是 `Object` 的例項，
在這種情況下，即使是 future 物件也會返回 true 。
相反，下面是確切測試 `Future` 的例子：

{:.good}
<?code-excerpt "usage_good.dart (test-future-or)"?>
{% prettify dart tag=pre+code %}
Future<T> logValue<T>(FutureOr<T> value) async {
  if (value is Future<T>) {
    var result = await value;
    print(result);
    return result;
  } else {
    print(value);
    return value;
  }
}
{% endprettify %}

{:.bad}
<?code-excerpt "usage_bad.dart (test-future-or)"?>
{% prettify dart tag=pre+code %}
Future<T> logValue<T>(FutureOr<T> value) async {
  if (value is T) {
    print(value);
    return value;
  } else {
    var result = await value;
    print(result);
    return result;
  }
}
{% endprettify %}

In the bad example, if you pass it a `Future<Object>`, it incorrectly treats it
like a bare, synchronous value.

在錯誤的範例中，如果給它傳一個 `Future<Object>` ，
它會錯誤地將其視為一個空的同步物件值。

[pokemon]: https://blog.codinghorror.com/new-programming-jargon/
[Error]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Error-class.html
[StackOverflowError]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/StackOverflowError-class.html
[OutOfMemoryError]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/OutOfMemoryError-class.html
[ArgumentError]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/ArgumentError-class.html
[AssertionError]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/AssertionError-class.html
[Exception]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Exception-class.html
