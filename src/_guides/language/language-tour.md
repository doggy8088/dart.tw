---
title: A tour of the Dart language
title: Dart 開發語言概覽
description: A tour of all the major Dart language features.
description: Dart 開發語言的主要特性概覽。
short-title: Language tour
js: [{url: 'https://dartpad.cn/inject_embed.dart.js', defer: true}]
---
<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /(^|\n) *\/\/\s+ignore:[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore:[^\n]+\n/$1\n/g; / *\/\/\s+ignore:[^\n]+//g; /([A-Z]\w*)\d\b/$1/g"?>

This page shows you how to use each major Dart feature, from
variables and operators to classes and libraries, with the assumption
that you already know how to program in another language.
For a briefer, less complete introduction to the language, see the
[language samples page](/samples).

本文將從變數和運算子開始到類和庫的使用來向你介紹 Dart 程式語言的主要功能，
這裡假設你已經有使用其它語言進行程式設計的經驗。

To learn more about Dart's core libraries, see the
[library tour](/guides/libraries/library-tour).
Whenever you want more details about a language feature,
consult the [Dart language specification][].

你可以透過檢視 [Dart 函式庫概覽](/guides/libraries/library-tour)
學習更多關於 Dart 核心函式庫的知識。
若還想了解更多有關語言功能的詳細內容，
請參閱 [Dart 程式設計語言規範][Dart language specification]。

{{site.alert.note}}

  You can play with most of Dart's language features using DartPad
  ([learn more](/tools/dartpad)).
  **<a href="{{site.dartpad}}" target="_blank" rel="noopener">Open
  DartPad.</a>**

  你可以透過 DartPad 體驗 Dart 的大部分語言功能 ([瞭解更多](/tools/dartpad))，
  **<a href="{{site.dartpad}}" target="_blank" rel="noopener">開啟 DartPad。</a>**

  This page uses embedded DartPads to display some of the examples.

  本頁面內嵌了一些 DartPads 做例子展示，

  {% include dartpads-embedded-troubleshooting.md %}

{{site.alert.end}}

{% comment %}
[TODO #2950: Look for null, ?, !, late, optional, Map, List, Set.
(Anything else?)
Look for dynamic. Look for code that isn't auto-included (no code-excerpt.)]
{% endcomment %}

## A basic Dart program

## 一個簡單的 Dart 程式

The following code uses many of Dart’s most basic features:

下面的應用程式程式碼用到了很多 Dart 的基本功能：

<?code-excerpt "misc/test/language_tour/basic_test.dart"?>
```dart
// Define a function.
void printInteger(int aNumber) {
  print('The number is $aNumber.'); // Print to console.
}

// This is where the app starts executing.
void main() {
  var number = 42; // Declare and initialize a variable.
  printInteger(number); // Call a function.
}
```

Here’s what this program uses that applies to all (or almost all) Dart
apps:

下面是上述應用程式中使用到的程式碼片段，這些程式碼片段適用於所有（或幾乎所有）的 Dart 應用：

<code>// <em>This is a comment.</em> </code>

<code>// <em>註釋。</em> </code>

:   A single-line comment.
    Dart also supports multi-line and document comments.
    For details, see [Comments](#comments).

    以雙斜槓開頭的一行陳述式稱為單行註釋。Dart 同樣支援多行註釋和文件註釋。查閱[註釋](#comments)獲取更多相關資訊。

`void`

:   A special type that indicates a value that's never used.
    Functions like `printInteger()` and `main()` that don't explicitly return a value
    have the `void` return type.

    一種特殊的型別，表示一個值永遠不會被使用。
    類似於 `main()` 和 `printInteger()` 的函式，
    以 `void` 宣告的函式返回型別，並不會返回值。

`int`

:   Another type, indicating an integer.
    Some additional [built-in types](#built-in-types)
    are `String`, `List`, and `bool`.

    另一種資料型別，表示一個整型數字。
    Dart 中一些其他的[內建型別](#built-in-types)包括 `String`、`List` 和 `bool`。

`42`

:   A number literal. Number literals are a kind of compile-time constant.

    表示一個數字字面量。數字字面量是一種編譯時常量。

`print()`

:   A handy way to display output.

    一種便利的將資訊輸出顯示的方式。

`'...'` (或 `"..."`)

:   A string literal.

    表示字串字面量。

<code>$<em>variableName</em></code> (或 <code>${<em>expression</em>}</code>)

:   String interpolation: including a variable or expression’s string
    equivalent inside of a string literal. For more information, see
    [Strings](#strings).

    表示字串插值：字串字面量中包含的變數或表示式。查閱[字串](#strings)獲取更多相關資訊。

`main()`

:   The special, *required*, top-level function where app execution
    starts. For more information, see
    [The main() function](#the-main-function).

    一個特殊且 **必須的** 最上層函式，Dart 應用程式總是會從該函式開始執行。查閱 [main() 函式](#the-main-function) 獲取更多相關資訊。

`var`

:   A way to declare a variable without specifying its type.
    The type of this variable (`int`)
    is determined by its initial value (`42`).

    用於定義變數，透過這種方式定義變數不需要指定變數型別。
    這類變數的型別 (`int`) 由它的初始值決定 (`42`)。

{{site.alert.note}}

  This site's code follows the conventions in the
  [Dart style guide](/guides/language/effective-dart/style).

  本站的程式碼遵循
  [Dart 風格指南](/guides/language/effective-dart/style) 中的約定。

{{site.alert.end}}


## Important concepts

## 重要概念

As you learn about the Dart language, keep these facts and concepts in
mind:

當你在學習 Dart 語言時, 應該牢記以下幾點：

-   Everything you can place in a variable is an *object*, and every
    object is an instance of a *class*. Even numbers, functions, and
    `null` are objects.
    With the exception of `null` (if you enable [sound null safety][ns]),
    all objects inherit from the [`Object`][] class.

    {{site.alert.version-note}}
      [Null safety][ns] was introduced in Dart 2.12.
      Using null safety requires a [language version][] of at least 2.12.
    {{site.alert.end}}

    所有變數參考的都是 **物件**，每個物件都是一個 **類** 的例項。
    數字、函式以及 `null` 都是物件。
    除去 `null` 以外（如果你開啟了 [空安全][ns]）,
    所有的類都繼承於 [`Object`][] 類別。

-   Although Dart is strongly typed, type annotations are optional
    because Dart can infer types. In the code above, `number`
    is inferred to be of type `int`.

    儘管 Dart 是強型別語言，但是在宣告變數時指定型別是可選的，因為 Dart 可以進行型別推斷。
    在上述程式碼中，變數 `number` 的型別被推斷為 `int` 型別。

-   If you enable [null safety][ns],
    variables can’t contain `null` unless you say they can.
    You can make a variable nullable by
    putting a question mark (`?`) at the end of its type.
    For example, a variable of type `int?` might be an integer,
    or it might be `null`.
    If you _know_ that an expression never evaluates to `null`
    but Dart disagrees,
    you can add `!` to assert that it isn't null
    (and to throw an exception if it is).
    An example: `int x = nullableButNotNullInt!`

    如果你開啟了 [空安全][ns]，變數在未宣告為可空型別時不能為 `null`。
    你可以透過在型別後加上問號 (`?`) 將型別宣告為可空。
    例如，`int?` 型別的變數可以是整形數字或 `null`。
    如果你 **明確知道** 一個表示式不會為空，但 Dart 不這麼認為時，
    你可以在表示式後新增 `!` 來斷言表示式不為空（為空時將丟擲例外）。
    例如：`int x = nullableButNotNullInt!`

-   When you want to explicitly say
    that any type is allowed, use the type `Object?`
    (if you've [enabled null safety][ns-enable]), `Object`,
    or—if you must defer type checking until runtime—the
    [special type `dynamic`][ObjectVsDynamic].

    如果你想要明確地宣告允許任意類別型，使用 `Object?`（如果你 [開啟了空安全][ns-enable]）、
    `Object` 或者 [特殊型別 `dynamic`][ObjectVsDynamic] 將檢查延遲到執行時進行。

-   Dart supports generic types, like `List<int>` (a list of integers)
    or `List<Object>` (a list of objects of any type).

    Dart 支援泛型，比如 `List<int>`（表示一組由 int 物件組成的列表）或
    `List<Object>`（表示一組由任何型別物件組成的列表）。

-   Dart supports top-level functions (such as `main()`), as well as
    functions tied to a class or object (*static* and *instance
    methods*, respectively). You can also create functions within
    functions (*nested* or *local functions*).

    Dart 支援最上層函式（例如 `main` 方法），
    同時還支援定義屬於類或物件的函式（即 *靜態* 和 *例項方法*）。
    你還可以在函式中定義函式（*巢狀(Nesting)* 或 *區域性函式*）。

-   Similarly, Dart supports top-level *variables*, as well as variables
    tied to a class or object (static and instance variables). Instance
    variables are sometimes known as *fields* or *properties*.

    Dart 支援最上層 **變數**，以及定義屬於類或物件的變數（靜態和例項變數）。
    例項變數有時稱之為域或屬性。

-   Unlike Java, Dart doesn’t have the keywords `public`, `protected`,
    and `private`. If an identifier starts with an underscore (`_`), it’s
    private to its library. For details, see
    [Libraries and visibility](#libraries-and-visibility).

    Dart 沒有類似於 Java 那樣的 `public`、`protected` 和 `private` 成員存取限定符。
    如果一個識別符號以下劃線 (`_`) 開頭則表示該識別符號在庫內是私有的。可以查閱 [庫和可見性](#libraries-and-visibility) 獲取更多相關資訊。

-   *Identifiers* can start with a letter or underscore (`_`), followed by any
    combination of those characters plus digits.

    *識別符號* 可以以字母或者下劃線 (`_`) 開頭，其後可跟字元和數字的組合。

-   Dart has both *expressions* (which have runtime values) and
    *statements* (which don't).
    For example, the [conditional expression](#conditional-expressions)
    `condition ? expr1 : expr2` has a value of `expr1` or `expr2`.
    Compare that to an [if-else statement](#if-and-else), which has no value.
    A statement often contains one or more expressions,
    but an expression can't directly contain a statement.

    Dart 中 **表示式** 和 **陳述式** 是有區別的，表示式有值而陳述式沒有。比如[條件表示式](#conditional-expressions) `expression condition ? expr1 : expr2` 中含有值 `expr1` 或 `expr2`。與 [if-else 分支陳述式](#if-and-else)相比，`if-else` 分支陳述式則沒有值。一個陳述式通常包含一個或多個表示式，但是一個表示式不能只包含一個陳述式。

-   Dart tools can report two kinds of problems: _warnings_ and _errors_.
    Warnings are just indications that your code might not work, but
    they don’t prevent your program from executing. Errors can be either
    compile-time or run-time. A compile-time error prevents the code
    from executing at all; a run-time error results in an
    [exception](#exceptions) being raised while the code executes.

    Dart 工具可以顯示 **警告** 和 **錯誤** 兩種型別的問題。
    警告表明程式碼可能有問題但不會阻止其執行。
    錯誤分為編譯時錯誤和執行時錯誤；
    編譯時錯誤程式碼無法執行；
    執行時錯誤會在程式碼執行時導致 [例外](#exceptions)。

## Keywords

## 關鍵字

The following table lists the words that the Dart language treats specially.

下面的表格中列出了 Dart 語言所使用的關鍵字。

{% assign ckw = '&nbsp;<sup title="contextual keyword" alt="contextual keyword">1</sup>' %}
{% assign bii = '&nbsp;<sup title="built-in-identifier" alt="built-in-identifier">2</sup>' %}
{% assign lrw = '&nbsp;<sup title="limited reserved word" alt="limited reserved word">3</sup>' %}
<div class="table-wrapper" markdown="1">
| [abstract][]{{bii}}   | [else][]              | [import][]{{bii}}     | [show][]{{ckw}}   |
| [as][]{{bii}}         | [enum][]              | [in][]                | [static][]{{bii}} |
| [assert][]            | [export][]{{bii}}     | [interface][]{{bii}}  | [super][]         |
| [async][]{{ckw}}      | [extends][]           | [is][]                | [switch][]        |
| [await][]{{lrw}}      | [extension][]{{bii}}  | [late][]{{bii}}       | [sync][]{{ckw}}   |
| [break][]             | [external][]{{bii}}   | [library][]{{bii}}    | [this][]          |
| [case][]              | [factory][]{{bii}}    | [mixin][]{{bii}}      | [throw][]         |
| [catch][]             | [false][]             | [new][]               | [true][]          |
| [class][]             | [final][]             | [null][]              | [try][]           |
| [const][]             | [finally][]           | [on][]{{ckw}}         | [typedef][]{{bii}}|
| [continue][]          | [for][]               | [operator][]{{bii}}   | [var][]           |
| [covariant][]{{bii}}  | [Function][]{{bii}}   | [part][]{{bii}}       | [void][]          |
| [default][]           | [get][]{{bii}}        | [required][]{{bii}}   | [while][]         |
| [deferred][]{{bii}}   | [hide][]{{ckw}}       | [rethrow][]           | [with][]          |
| [do][]                | [if][]                | [return][]            | [yield][]{{lrw}}  |
| [dynamic][]{{bii}}    | [implements][]{{bii}} | [set][]{{bii}}        |                   |
{:.table .table-striped .nowrap}
</div>

[abstract]: #abstract-classes
[as]: #type-test-operators
[assert]: #assert
[async]: #asynchrony-support
[await]: #asynchrony-support
[break]: #break-and-continue
[case]: #switch-and-case
[catch]: #catch
[class]: #instance-variables
[const]: #final-and-const
{% comment %}
  [TODO #2950: Make sure that points to a place that talks about const constructors,
  as well as const literals and variables.]
{% endcomment %}
[continue]: #break-and-continue
[covariant]: /guides/language/sound-problems#the-covariant-keyword
[default]: #switch-and-case
[deferred]: #lazily-loading-a-library
[do]: #while-and-do-while
[dynamic]: #important-concepts
[else]: #if-and-else
[enum]: #enumerated-types
[export]: /guides/libraries/create-library-packages
[extends]: #extending-a-class
[extension]: #extension-methods
[external]: https://spec.dart.dev/DartLangSpecDraft.pdf#External%20Functions
[factory]: #factory-constructors
[false]: #booleans
[final]: #final-and-const
[finally]: #finally
[for]: #for-loops
[Function]: #functions
[get]: #getters-and-setters
[hide]: #importing-only-part-of-a-library
[if]: #if-and-else
[implements]: #implicit-interfaces
[import]: #using-libraries
[in]: #for-loops
[interface]: #implicit-interfaces
[is]: #type-test-operators
[late]: #late-variables
[library]: #libraries-and-visibility
[mixin]: #adding-features-to-a-class-mixins
[new]: #using-constructors
[null]: #default-value
[on]: #catch
[operator]: #_operators
[part]: /guides/libraries/create-library-packages#organizing-a-library-package
[required]: #named-parameters
[rethrow]: #catch
[return]: #functions
[set]: #getters-and-setters
[show]: #importing-only-part-of-a-library
[static]: #class-variables-and-methods
[super]: #extending-a-class
[switch]: #switch-and-case
[sync]: #generators
[this]: #constructors
[throw]: #throw
[true]: #booleans
[try]: #catch
[typedef]: #typedefs
[var]: #variables
[void]: #built-in-types
{% comment %}
  TODO #2950: Add coverage of void to the language tour.
{% endcomment %}
[with]: #adding-features-to-a-class-mixins
[while]: #while-and-do-while
[yield]: #generators

Avoid using these words as identifiers.
However, if necessary, the keywords marked with superscripts can be identifiers:

應該避免使用這些單詞作為識別符號。但是，帶有上標的單詞可以在必要的情況下作為識別符號：

* Words with the superscript **1** are **contextual keywords**,
  which have meaning only in specific places.
  They're valid identifiers everywhere.

  帶有上標 **1** 的關鍵字為 **上下文關鍵字**，只有在特定的場景才有意義，它們可以在任何地方作為有效的識別符號。

* Words with the superscript **2** are **built-in identifiers**.
  These keywords are valid identifiers in most places,
  but they can't be used as class or type names, or as import prefixes.

  帶有上標 **2** 的關鍵字為 **內建識別符號**，這些關鍵字在大多數時候都可以作為有效的識別符號，但是它們不能用作類別名稱或者型別名或者作為匯入字首使用。

* Words with the superscript **3** are limited reserved words related to
  [asynchrony support](#asynchrony-support).
  You can't use `await` or `yield` as an identifier
  in any function body marked with `async`, `async*`, or `sync*`.

  帶有上標 **3** 的關鍵字為 Dart 1.0 釋出後用於 [支援非同步](#asynchrony-support) 相關內容。不能在由關鍵字 `async`、`async*` 或 `sync*` 標識的方法體中使用 `await` 或 `yield` 作為識別符號。

All other words in the table are **reserved words**,
which can't be identifiers.

其它沒有上標的關鍵字為 **保留字**，均不能用作識別符號。

## Variables

## 變數

Here’s an example of creating a variable and initializing it:

下面的範例程式碼將建立一個變數並將其初始化：

<?code-excerpt "misc/lib/language_tour/variables.dart (var-decl)"?>
```dart
var name = 'Bob';
```

Variables store references. The variable called `name` contains a
reference to a `String` object with a value of “Bob”.

變數僅儲存物件的參考。這裡名為 `name` 的變數儲存了一個 `String` 型別物件的參考，“Bob” 則是該物件的值。

The type of the `name` variable is inferred to be `String`,
but you can change that type by specifying it.
If an object isn't restricted to a single type,
specify the `Object` type (or `dynamic` if necessary).

`name` 變數的型別被推斷為 `String`，但是你可以為其指定型別。
如果一個物件的參考不侷限於單一的型別，可以將其指定為 `Object`（或 `dynamic`）型別。

<?code-excerpt "misc/lib/language_tour/variables.dart (type-decl)"?>
```dart
Object name = 'Bob';
```

Another option is to explicitly declare the type that would be inferred:

除此之外你也可以指定型別：

<?code-excerpt "misc/lib/language_tour/variables.dart (static-types)"?>
```dart
String name = 'Bob';
```

{{site.alert.note}}

  This page follows the
  [style guide recommendation](/guides/language/effective-dart/design#types)
  of using `var`, rather than type annotations, for local variables.

  本文遵循
  [風格建議指南](/guides/language/effective-dart/design#types) 中的建議，
  透過 `var` 宣告區域變數而非使用指定的型別。

{{site.alert.end}}


### Default value

### 預設值

Uninitialized variables that have a nullable type
have an initial value of `null`.
(If you haven't opted into [null safety][ns],
then every variable has a nullable type.)
Even variables with numeric types are initially null,
because numbers—like everything else in Dart—are objects.

在 Dart 中，未初始化以及可空型別的變數擁有一個預設的初始值 `null`。
（如果你未遷移至 [空安全][ns]，所有變數都為可空型別。）
即便數字也是如此，因為在 Dart 中一切皆為物件，數字也不例外。

<?code-excerpt "misc/test/language_tour/variables_test.dart (var-null-init)"?>
```dart
int? lineCount;
assert(lineCount == null);
```

{{site.alert.note}}

  Production code ignores the `assert()` call. During development, on the other
  hand, <code>assert(<em>condition</em>)</code> throws an exception if
  _condition_ is false. For details, see [Assert](#assert).

  `assert()` 的呼叫將會在生產環境的程式碼中被忽略掉。
  在開發過程中，<code>assert(<em>condition</em>)</code>
  將會在 **條件判斷** 為 false 時丟擲一個例外。
  詳情請查閱 [Assert](#assert)。

{{site.alert.end}}

If you enable null safety, then you must initialize the values
of non-nullable variables before you use them:

若你啟用了空安全，你必須在使用變數前初始化它的值。

<?code-excerpt "misc/lib/language_tour/variables.dart (var-ns-init)"?>
```dart
int lineCount = 0;
```

You don't have to initialize a local variable where it's declared,
but you do need to assign it a value before it's used.
For example, the following code is valid because
Dart can detect that `lineCount` is non-null by the time
it's passed to `print()`:

你並不需要在宣告變數時初始化，只需在第一次用到這個變數前初始化即可。
例如，下面的程式碼是正確的，因為 Dart 可以在 `lineCount` 被傳遞到 `print()` 時檢測它是否為空:

<?code-excerpt "misc/lib/language_tour/variables.dart (var-ns-flow)"?>
```dart
int lineCount;

if (weLikeToCount) {
  lineCount = countLines();
} else {
  lineCount = 0;
}

print(lineCount);
```

Top-level and class variables are lazily initialized;
the initialization code runs
the first time the variable is used.

最上層變數以及類變數是延遲初始化的，
即檢查變數的初始化會在它第一次被使用的時候完成。

### Late variables

### 延遲初始化變數

Dart 2.12 added the `late` modifier, which has two use cases:

Dart 2.12 新增了 `late` 修飾符，這個修飾符可以在以下情況中使用：

* Declaring a non-nullable variable that's initialized after its declaration.
  
  宣告一個非空變數，但不在宣告時初始化。

* Lazily initializing a variable.

   延遲初始化一個變數。

Often Dart's control flow analysis can detect when a non-nullable variable
is set to a non-null value before it's used,
but sometimes analysis fails.
Two common cases are top-level variables and instance variables:
Dart often can't determine whether they're set,
so it doesn't try.

通常 Dart 的語義分析會在一個已宣告為非空的變數被使用前檢查它是否已經被賦值，
但有時這個分析會失敗。
例如：在檢查最上層變數和例項變數時，分析通常無法判斷它們是否已經被初始化，因此不會進行分析。

If you're sure that a variable is set before it's used,
but Dart disagrees,
you can fix the error by marking the variable as `late`:

如果你確定這個變數在使用前就已經被宣告，但 Dart 判斷失誤的話，
你可以在宣告變數的時候使用 `late` 修飾來解決這個問題。

<?code-excerpt "misc/lib/language_tour/variables.dart (var-late-top-level)" replace="/late/[!$&!]/g"?>
```dart
[!late!] String description;

void main() {
  description = 'Feijoada!';
  print(description);
}
```

{{site.alert.warn}}

  If you fail to initialize a `late` variable,
  a runtime error occurs when the variable is used.
  
  若 `late` 標記的變數在使用前沒有初始化，
  在變數被使用時會丟擲執行時例外。
  
{{site.alert.end}}

When you mark a variable as `late` but initialize it at its declaration,
then the initializer runs the first time the variable is used.
This lazy initialization is handy in a couple of cases:

如果一個 `late` 修飾的變數在宣告時就指定了初始化方法，
那麼它實際的初始化過程會發生在第一次被使用的時候。
這樣的延遲初始化在以下場景中會帶來便利：

* The variable might not be needed,
  and initializing it is costly.
  
  Dart 認為這個變數可能在後文中沒被使用，而且初始化時將產生較大的代價。
  
* You're initializing an instance variable,
  and its initializer needs access to `this`.
  
  你正在初始化一個例項變數，它的初始化方法需要呼叫 `this`。

In the following example,
if the `temperature` variable is never used,
then the expensive `readThermometer()` function is never called:

在下面這個例子中，如果 `temperature` 變數從未被使用的話，
那麼 `readThermometer()` 將永遠不會被呼叫：

<?code-excerpt "misc/lib/language_tour/variables.dart (var-late-lazy)" replace="/late/[!$&!]/g"?>
```dart
// This is the program's only call to readThermometer().
[!late!] String temperature = readThermometer(); // Lazily initialized.
```

### Final and const

### Final 和 Const

If you never intend to change a variable, use `final` or `const`, either
instead of `var` or in addition to a type. A final variable can be set
only once; a const variable is a compile-time constant. (Const variables
are implicitly final.)

如果你不想更改一個變數，可以使用關鍵字 `final` 或者 `const` 修飾變數，
這兩個關鍵字可以替代 `var` 關鍵字或者加在一個具體的型別前。
一個 final 變數只可以被賦值一次；
一個 const 變數是一個編譯時常量 (const 變數同時也是 final 的)。


{{site.alert.note}}

  [Instance variables](#instance-variables) can be `final` but not `const`.

  [例項變數](#instance-variables) 可以是 `final` 的但不可以是 `const`，

{{site.alert.end}}

Here's an example of creating and setting a `final` variable:

下面的範例中我們建立並設定兩個 `final` 變數：

<?code-excerpt "misc/lib/language_tour/variables.dart (final)"?>
```dart
final name = 'Bob'; // Without a type annotation
final String nickname = 'Bobby';
```

You can't change the value of a `final` variable:

你不能修改一個 final 變數的值：

{:.fails-sa}
<?code-excerpt "misc/lib/language_tour/variables.dart (cant-assign-to-final)"?>
```dart
name = 'Alice'; // Error: a final variable can only be set once.
```

Use `const` for variables that you want to be **compile-time constants**. If
the const variable is at the class level, mark it `static const`.
Where you declare the variable, set the value to a compile-time constant
such as a number or string literal, a const
variable, or the result of an arithmetic operation on constant numbers:

使用關鍵字 `const` 修飾變量表示該變數為 **編譯時常量**。
如果使用 const 修飾類中的變數，則必須加上 static 關鍵字，
即 `static const`（譯者注：順序不能顛倒）。
在宣告 const 變數時可以直接為其賦值，也可以使用其它的 const 變數為其賦值：

<?code-excerpt "misc/lib/language_tour/variables.dart (const)"?>
```dart
const bar = 1000000; // Unit of pressure (dynes/cm2)
const double atm = 1.01325 * bar; // Standard atmosphere
```

The `const` keyword isn't just for declaring constant variables.
You can also use it to create constant _values_,
as well as to declare constructors that _create_ constant values.
Any variable can have a constant value.

`const` 關鍵字不僅僅可以用來定義常量，
還可以用來建立 **常量值**，該常量值可以賦予給任何變數。
你也可以將建構函式宣告為 const 的，這種型別的建構函式建立的物件是不可改變的。

<?code-excerpt "misc/lib/language_tour/variables.dart (const-vs-final)"?>
```dart
var foo = const [];
final bar = const [];
const baz = []; // Equivalent to `const []`
```

You can omit `const` from the initializing expression of a `const` declaration,
like for `baz` above. For details, see [DON’T use const redundantly][].

如果使用初始化表示式為常量賦值可以省略掉關鍵字 `const`，比如上面的常量 `baz` 的賦值就省略掉了 `const`。
詳情請查閱 [不要冗餘地使用 `const`][DON’T use const redundantly]。

You can change the value of a non-final, non-const variable,
even if it used to have a `const` value:

沒有使用 final 或 const 修飾的變數的值是可以被更改的，
即使這些變數之前參考過 `const` 的值。

<?code-excerpt "misc/lib/language_tour/variables.dart (reassign-to-non-final)"?>
```dart
foo = [1, 2, 3]; // Was const []
```

You can't change the value of a `const` variable:

常量的值不可以被修改：

{:.fails-sa}
<?code-excerpt "misc/lib/language_tour/variables.dart (cant-assign-to-const)"?>
```dart
baz = [42]; // Error: Constant variables can't be assigned a value.
```

You can define constants that use
[type checks and casts](#type-test-operators) (`is` and `as`),
[collection `if`](#collection-operators),
and [spread operators](#spread-operator) (`...` and `...?`):

你可以在常量中使用 [型別檢查和強制型別轉換](#type-test-operators) (`is` 和 `as`)、
[集合中的 `if`](#collection-operators) 以及
[展開運運算元](#spread-operator) (`...` 和 `...?`)：

<?code-excerpt "misc/lib/language_tour/variables.dart (const-dart-25)"?>
```dart
const Object i = 3; // Where i is a const Object with an int value...
const list = [i as int]; // Use a typecast.
const map = {if (i is int) i: 'int'}; // Use is and collection if.
const set = {if (list is List<int>) ...list}; // ...and a spread.
```

{{site.alert.note}}
  Although a `final` object cannot be modified,
  its fields can be changed.
  In comparison, a `const` object and its fields
  cannot be changed: they're _immutable_.
{{site.alert.end}}

For more information on using `const` to create constant values, see
[Lists](#lists), [Maps](#maps), and [Classes](#classes).

可以查閱 [Lists](#lists)、[Maps](#maps) 和 [Classes](#classes)
獲取更多關於使用 `const` 建立常量值的資訊。

## Built-in types

## 內建型別

The Dart language has special support for the following:

Dart 語言支援下列內容：

- [Numbers](#numbers) (`int`, `double`)
- [Strings](#strings) (`String`)
- [Booleans](#booleans) (`bool`)
- [Lists](#lists) (`List`, also known as *arrays*)

  [Lists](#lists) (也被稱為 *arrays*)

- [Sets](#sets) (`Set`)
- [Maps](#maps) (`Map`)
- [Runes](#characters) (`Runes`; often replaced by the `characters` API)

  [Runes](#characters) (常用於在 `Characters` API 中進行字元替換)

- [Symbols](#symbols) (`Symbol`)
- The value `null` (`Null`)

This support includes the ability to create objects using literals.
For example, `'this is a string'` is a string literal,
and `true` is a boolean literal.

使用字面量來建立物件也受到支援。
例如 `'This is a string'` 是一個字串字面量，`true` 是一個布林字面量。

Because every variable in Dart refers to an object—an instance of a
*class*—you can usually use *constructors* to initialize variables. Some
of the built-in types have their own constructors. For example, you can
use the `Map()` constructor to create a map.

由於 Dart 中每個變數參考都指向一個物件（一個 **類** 的例項），
通常也可以使用 **構造器** 來初始化變數。一些內建的型別有它們自己的構造器。
例如你可以使用 `Map()` 來建立一個 map 物件。

Some other types also have special roles in the Dart language:

* `Object`: The superclass of all Dart classes except `Null`.
* `Enum`: The superclass of all enums.
* `Future` and `Stream`: Used in [asynchrony support](#asynchrony-support).
* `Iterable`: Used in [for-in loops][iteration] and
  in synchronous [generator functions](#generator).
* `Never`: Indicates that an expression can never
  successfully finish evaluating.
  Most often used for functions that always throw an exception.
* `dynamic`: Indicates that you want to disable static checking.
  Usually you should use `Object` or `Object?` instead.
* `void`: Indicates that a value is never used.
  Often used as a return type.

{% comment %}
[TODO: move/add for-in coverage to language tour?]
{% endcomment %}

The `Object`, `Object?`, `Null`, and `Never` classes
have special roles in the class hierarchy,
as described in the [top-and-bottom][] section of
[Understanding null safety][].

{% comment %}
If we decide to cover `dynamic` more,
here's a nice example that illustrates what dynamic does:
  dynamic a = 2;
  String b = a; // No problem! Until runtime, when you get an uncaught error.

  Object c = 2;
  String d = c;  // Problem!
{% endcomment %}

### Numbers

Dart numbers come in two flavors:

Dart 支援兩種 Number 型別：

[`int`][]

:   Integer values no larger than 64 bits,
    [depending on the platform][dart-numbers].
    On native platforms, values can be from
    -2<sup>63</sup> to 2<sup>63</sup> - 1.
    On the web, integer values are represented as JavaScript numbers
    (64-bit floating-point values with no fractional part)
    and can be from -2<sup>53</sup> to 2<sup>53</sup> - 1.

    整數值；長度不超過 64 位，具體取值範圍 [依賴於不同的平台][dart-numbers]。
    在 DartVM 上其取值位於 -2<sup>63</sup> 至 2<sup>63</sup> - 1 之間。
    在 Web 上，整型數值代表著 JavaScript 的數字（64 位無小數浮點型），
    其允許的取值範圍在 -2<sup>53</sup> 至 2<sup>53</sup> - 1 之間。

[`double`][]

:   64-bit (double-precision) floating-point numbers, as specified by
    the IEEE 754 standard.

    64 位的雙精度浮點數字，且符合 IEEE 754 標準。

Both `int` and `double` are subtypes of [`num`][].
The num type includes basic operators such as +, -, /, and \*,
and is also where you’ll find `abs()`,` ceil()`,
and `floor()`, among other methods.
(Bitwise operators, such as \>\>, are defined in the `int` class.)
If num and its subtypes don’t have what you’re looking for, the
[dart:math][] library might.

`int` 和 `double` 都是 [`num`][] 的子類別。
num 中定義了一些基本的運算子比如 +、-、\*、/ 等，
還定義了 `abs()`、`ceil()` 和 `floor()` 等方法
（位運算子，比如 \>\> 定義在 int 中）。
如果 num 及其子類別不滿足你的要求，
可以檢視 [dart:math][] 庫中的 API。

Integers are numbers without a decimal point. Here are some examples of
defining integer literals:

整數是不帶小數點的數字，
下面是一些定義整數字面量的例子：

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (integer-literals)"?>
```dart
var x = 1;
var hex = 0xDEADBEEF;
```

If a number includes a decimal, it is a double. Here are some examples
of defining double literals:

如果一個數字包含了小數點，那麼它就是浮點型的。
下面是一些定義浮點數字面量的例子：

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (double-literals)"?>
```dart
var y = 1.1;
var exponents = 1.42e5;
```

You can also declare a variable as a num. If you do this, the variable
can have both integer and double values.

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (declare-num)"?>
```dart
num x = 1; // x can have both int and double values
x += 2.5;
```

Integer literals are automatically converted to doubles when necessary:

整型字面量將會在必要的時候自動轉換成浮點數字面量：

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (int-to-double)"?>
```dart
double z = 1; // Equivalent to double z = 1.0.
```

{{site.alert.version-note}}

  Before Dart 2.1, it was an error to use an integer literal in a double
  context.

  在 Dart 2.1 之前，在浮點數上下文中使用整數字面量是錯誤的。

{{site.alert.end}}

Here’s how you turn a string into a number, or vice versa:

下面是字串和數字之間轉換的方式：

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (number-conversion)"?>
```dart
// String -> int
var one = int.parse('1');
assert(one == 1);

// String -> double
var onePointOne = double.parse('1.1');
assert(onePointOne == 1.1);

// int -> String
String oneAsString = 1.toString();
assert(oneAsString == '1');

// double -> String
String piAsString = 3.14159.toStringAsFixed(2);
assert(piAsString == '3.14');
```

The `int` type specifies the traditional bitwise shift (`<<`, `>>`, `>>>`),
complement (`~`), AND (`&`), OR (`|`), and XOR (`^`) operators,
which are useful for manipulating and masking flags in bit fields.
For example:

整型支援傳統的位移操作，比如移位（`<<`、`>>` 和 `>>>`）、
補碼 (`~`)、按位與 (`&`)、按位或 (`|`) 以及按位異或 (`^`)，例如：

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (bit-shifting)"?>
```dart
assert((3 << 1) == 6); // 0011 << 1 == 0110
assert((3 | 4) == 7); // 0011 | 0100 == 0111
assert((3 & 4) == 0); // 0011 & 0100 == 0000
```

For more examples, see the
[bitwise and shift operator](#bitwise-and-shift-operators) section.

更多範例請檢視 [移位運運算元](#bitwise-and-shift-operators) 小節。

Literal numbers are compile-time constants.
Many arithmetic expressions are also compile-time constants,
as long as their operands are
compile-time constants that evaluate to numbers.

數字字面量為編譯時常量。很多算術表示式只要其運算元是常量，
則表示式結果也是編譯時常量。

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (const-num)"?>
```dart
const msPerSecond = 1000;
const secondsUntilRetry = 5;
const msUntilRetry = secondsUntilRetry * msPerSecond;
```

For more information, see [Numbers in Dart][dart-numbers].

更多內容，請檢視 [Dart 中的數字][dart-numbers]。

[dart-numbers]: /guides/language/numbers

### Strings

A Dart string (`String` object) holds a sequence of UTF-16 code units.
You can use either
single or double quotes to create a string:

Dart 字串 （`String` 物件）包含了 UTF-16 編碼的字元序列。
可以使用單引號或者雙引號來建立字串：

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (quoting)"?>
```dart
var s1 = 'Single quotes work well for string literals.';
var s2 = "Double quotes work just as well.";
var s3 = 'It\'s easy to escape the string delimiter.';
var s4 = "It's even easier to use the other delimiter.";
```

<!--skip-->
```dart
// 程式碼中文解釋
var s1 = '使用單引號建立字串字面量。';
var s2 = "雙引號也可以用於建立字串字面量。";
var s3 = '使用單引號建立字串時可以使用斜槓來轉義那些與單引號衝突的字串：\'。';
var s4 = "而在雙引號中則不需要使用轉義與單引號衝突的字串：'";
```

You can put the value of an expression inside a string by using
`${`*`expression`*`}`. If the expression is an identifier, you can skip
the {}. To get the string corresponding to an object, Dart calls the
object’s `toString()` method.

在字串中，請以 `${`*`表示式`*`}` 的形式使用表示式，
如果表示式是一個識別符號，可以省略掉 {}。
如果表示式的結果為一個物件，則 Dart 會呼叫該物件的 `toString` 方法來獲取一個字串。

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (string-interpolation)"?>
```dart
var s = 'string interpolation';

assert('Dart has $s, which is very handy.' ==
    'Dart has string interpolation, '
        'which is very handy.');
assert('That deserves all caps. '
        '${s.toUpperCase()} is very handy!' ==
    'That deserves all caps. '
        'STRING INTERPOLATION is very handy!');
```

<!--skip-->
```dart
// 程式碼中文解釋
var s = '字串插值';

assert('Dart 有$s，使用起來非常方便。' == 'Dart 有字串插值，使用起來非常方便。');
assert('使用${s.substring(3,5)}表示式也非常方便' == '使用插值表示式也非常方便。');
```

{{site.alert.note}}

  The `==` operator tests whether two objects are equivalent. Two
  strings are equivalent if they contain the same sequence of code
  units.

  `==` 運算子負責判斷兩個物件是否等同，
  比如，如果兩個字串包含一樣的字元編碼序列，
  則表示他們是等同的。

{{site.alert.end}}

You can concatenate strings using adjacent string literals or the `+`
operator:

你可以使用 `+` 運算子或並列放置多個字串來連線字串：

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (adjacent-string-literals)"?>
```dart
var s1 = 'String '
    'concatenation'
    " works even over line breaks.";
assert(s1 ==
    'String concatenation works even over '
        'line breaks.');

var s2 = 'The + operator ' + 'works, as well.';
assert(s2 == 'The + operator works, as well.');
```

<!--skip-->
```dart
// 程式碼中文解釋
var s1 = '可以拼接'
    '字串'
    "即便它們不在同一行。";
assert(s1 == '可以拼接字串即便它們不在同一行。');

var s2 = '使用加號 + 運算子' + '也可以達到相同的效果。';
assert(s2 == '使用加號 + 運算子也可以達到相同的效果。');
```

Another way to create a multi-line string: use a triple quote with
either single or double quotation marks:

使用三個單引號或者三個雙引號也能建立多行字串：

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (triple-quotes)"?>
```dart
var s1 = '''
You can create
multi-line strings like this one.
''';

var s2 = """This is also a
multi-line string.""";
```

<!--skip-->
```dart
// 程式碼中文解釋
var s1 = '''
你可以像這樣建立
多行字串。
''';

var s2 = """這也是一個
多行字串。""";
```

You can create a “raw” string by prefixing it with `r`:

在字串前加上 `r` 作為字首建立 “raw” 字串
（即不會被做任何處理（比如轉義）的字串）：

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (raw-strings)"?>
```dart
var s = r'In a raw string, not even \n gets special treatment.';
```

<!--skip-->
```dart
// 程式碼中文解釋
var s = r'在 raw 字串中，跳脫字元串 \n 會直接輸出 “\n” 而不是轉義為換行。';
```

See [Runes and grapheme clusters](#characters) for details on how
to express Unicode characters in a string.

你可以查閱 [Runes 與 grapheme clusters](#characters) 獲取更多關於如何在字串中表示 Unicode 字元的資訊。

Literal strings are compile-time constants,
as long as any interpolated expression is a compile-time constant
that evaluates to null or a numeric, string, or boolean value.

字串字面量是一個編譯時常量，
只要是編譯時常量 (null、數字、字串、布林)
都可以作為字串字面量的插值表示式：

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (string-literals)"?>
```dart
// These work in a const string.
const aConstNum = 0;
const aConstBool = true;
const aConstString = 'a constant string';

// These do NOT work in a const string.
var aNum = 0;
var aBool = true;
var aString = 'a string';
const aConstList = [1, 2, 3];

const validConstString = '$aConstNum $aConstBool $aConstString';
// const invalidConstString = '$aNum $aBool $aString $aConstList';
```

For more information on using strings, see
[Strings and regular expressions](/guides/libraries/library-tour#strings-and-regular-expressions).

可以查閱
[字串和正則表示式](/guides/libraries/library-tour#strings-and-regular-expressions)
獲取更多關於如何使用字串的資訊。

### Booleans

### 布林型別

To represent boolean values, Dart has a type named `bool`. Only two
objects have type bool: the boolean literals `true` and `false`,
which are both compile-time constants.

Dart 使用 `bool` 關鍵字表示布林型別，
布林型別只有兩個物件 `true` 和 `false`，兩者都是編譯時常量。

Dart's type safety means that you can't use code like
<code>if (<em>nonbooleanValue</em>)</code> or
<code>assert (<em>nonbooleanValue</em>)</code>.
Instead, explicitly check for values, like this:

Dart 的型別安全不允許你使用類似 <code>if (<em>nonbooleanValue</em>)</code>
或者 <code>assert (<em>nonbooleanValue</em>)</code> 這樣的程式碼檢查布林值。
相反，你應該總是顯示地檢查布林值，比如像下面的程式碼這樣：

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (no-truthy)"?>
```dart
// Check for an empty string.
var fullName = '';
assert(fullName.isEmpty);

// Check for zero.
var hitPoints = 0;
assert(hitPoints <= 0);

// Check for null.
var unicorn;
assert(unicorn == null);

// Check for NaN.
var iMeantToDoThis = 0 / 0;
assert(iMeantToDoThis.isNaN);
```

### Lists

Perhaps the most common collection in nearly every programming language
is the *array*, or ordered group of objects. In Dart, arrays are
[`List`][] objects, so most people just call them *lists*.

陣列 (**Array**) 是幾乎所有程式語言中最常見的集合型別，
在 Dart 中陣列由 [`List`][] 物件表示。
通常稱之為 **List**。

Dart list literals are denoted by
a comma separated list of expressions or values,
enclosed in square brackets (`[]`).
Here's a simple Dart list:

Dart 中的列表字面量是由逗號分隔的一串表示式或值並以方括號 (`[]`) 包裹而組成的。
下面是一個 Dart List 的範例：

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (list-literal)"?>
```dart
var list = [1, 2, 3];
```

{{site.alert.note}}

  Dart infers that `list` has type `List<int>`. If you try to add non-integer
  objects to this list, the analyzer or runtime raises an error. For more
  information, read about
  [type inference.](/guides/language/type-system#type-inference)

  這裡 Dart 推斷出 `list` 的型別為 `List<int>`，
  如果往該陣列中新增一個非 int 型別的物件則會報錯。
  你可以閱讀
  [型別推斷](/guides/language/type-system#type-inference) 獲取更多相關資訊。

{{site.alert.end}}

<a name="trailing-comma"></a>
You can add a comma after the last item in a Dart collection literal.
This _trailing comma_ doesn't affect the collection,
but it can help prevent copy-paste errors.

<a name="trailing-comma"></a>
你可以在 Dart 的集合型別的最後一個專案後新增逗號。
這個尾隨逗號並不會影響集合，但它能有效避免「複製貼上」的錯誤。

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (trailing-commas)"?>
```dart
var list = [
  'Car',
  'Boat',
  'Plane',
];
```

Lists use zero-based indexing, where 0 is the index of the first value
and `list.length - 1` is the index of the last value. 
You can get a list’s length using the `.length` property
and access a list's values using the subscript operator (`[]`):

List 的下標索引從 0 開始，第一個元素的下標為 0，
最後一個元素的下標為 `list.length - 1`。
你可以像 JavaScript 中的用法那樣獲取 Dart 中 List 的長度以及元素：

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (list-indexing)"?>
```dart
var list = [1, 2, 3];
assert(list.length == 3);
assert(list[1] == 2);

list[1] = 1;
assert(list[1] == 1);
```

To create a list that's a compile-time constant,
add `const` before the list literal:

在 List 字面量前新增 `const` 關鍵字
會建立一個編譯時常量：

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (const-list)"?>
```dart
var constantList = const [1, 2, 3];
// constantList[1] = 1; // This line will cause an error.
```

<a id="spread-operator"> </a>
Dart supports the **spread operator** (`...`) and the
**null-aware spread operator** (`...?`),
which provide a concise way to insert multiple values into a collection.

<a id="spread-operator"> </a>
Dart 在 2.3 引入了 **擴充運運算元**（`...`）和 **空感知擴充運運算元**（`...?`），
它們提供了一種將多個元素插入集合的簡潔方法。

For example, you can use the spread operator (`...`) to insert
all the values of a list into another list:

例如，你可以使用擴充運運算元（`...`）
將一個 List 中的所有元素插入到另一個 List 中：

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (list-spread)"?>
```dart
var list = [1, 2, 3];
var list2 = [0, ...list];
assert(list2.length == 4);
```

If the expression to the right of the spread operator might be null,
you can avoid exceptions by using a null-aware spread operator (`...?`):

如果擴充運運算元右邊可能為 null ，
你可以使用 null-aware 擴充運運算元（`...?`）來避免產生例外：

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (list-null-spread)"?>
```dart
var list2 = [0, ...?list];
assert(list2.length == 1);
```

For more details and examples of using the spread operator, see the
[spread operator proposal.][spread proposal]

可以查閱[擴充運運算元建議][spread proposal]獲取更多關於如何使用擴充運運算元的資訊。

<a id="collection-operators"> </a>
Dart also offers **collection if** and **collection for**,
which you can use to build collections using conditionals (`if`)
and repetition (`for`).

<a id="collection-operators"> </a>
Dart 還同時引入了 **集合中的 if** 和 **集合中的 for** 操作，
在建構集合時，可以使用條件判斷 (`if`) 和迴圈 (`for`)。

Here's an example of using **collection if**
to create a list with three or four items in it:

下面範例是使用 **集合中的 if** 來建立一個 List 的範例，
它可能包含 3 個或 4 個元素：

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (list-if)"?>
```dart
var nav = ['Home', 'Furniture', 'Plants', if (promoActive) 'Outlet'];
```

Here's an example of using **collection for**
to manipulate the items of a list before
adding them to another list:

下面是使用 **集合中的 for** 將列表中的元素修改後新增到另一個列表中的範例：

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (list-for)"?>
```dart
var listOfInts = [1, 2, 3];
var listOfStrings = ['#0', for (var i in listOfInts) '#$i'];
assert(listOfStrings[1] == '#1');
```

For more details and examples of using collection `if` and `for`, see the
[control flow collections proposal.][collections proposal]

你可以查閱 [集合中使用控制流建議][collections proposal]
獲取更多關於在集合中使用 `if` 和 `for` 的細節內容和範例。

[collections proposal]: https://github.com/dart-lang/language/blob/master/accepted/2.3/control-flow-collections/feature-specification.md

[spread proposal]: https://github.com/dart-lang/language/blob/master/accepted/2.3/spread-collections/feature-specification.md

The List type has many handy methods for manipulating lists. For more
information about lists, see [Generics](#generics) and
[Collections](/guides/libraries/library-tour#collections).

List 類中有許多用於操作 List 的便捷方法，
你可以查閱 [泛型](#generics) 和 [集合](/guides/libraries/library-tour#collections)
獲取更多與之相關的資訊。

### Sets

A set in Dart is an unordered collection of unique items.
Dart support for sets is provided by set literals and the
[`Set`][] type.

在 Dart 中，set 是一組特定元素的無序集合。
Dart 支援的集合由集合的字面量和 [`Set`] 類提供。

{{site.alert.version-note}}

  Although the Set _type_ has always been a core part of Dart, set _literals_
  were introduced in Dart 2.2.

  儘管 Set **型別(type)** 一直都是 Dart 的一項核心功能，
  但是 Set **字面量(literals)** 是在 Dart 2.2 中才加入的。

{{site.alert.end}}

Here is a simple Dart set, created using a set literal:

下面是使用 Set 字面量來建立一個 Set 集合的方法：

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (set-literal)"?>
```dart
var halogens = {'fluorine', 'chlorine', 'bromine', 'iodine', 'astatine'};
```

{{site.alert.note}}

  Dart infers that `halogens` has the type `Set<String>`. If you try to add the
  wrong type of value to the set, the analyzer or runtime raises an error. For
  more information, read about
  [type inference.](/guides/language/type-system#type-inference)

  Dart 推斷 `halogens` 變數是一個 `Set<String>` 型別的集合，
  如果往該 Set 中新增型別不正確的物件則會報錯。
  你可以查閱
  [型別推斷](/guides/language/type-system#type-inference) 獲取更多與之相關的內容。

{{site.alert.end}}

To create an empty set, use `{}` preceded by a type argument,
or assign `{}` to a variable of type `Set`:

可以使用在 `{}` 前加上型別引數的方式建立一個空的 Set，
或者將 `{}` 賦值給一個 Set 型別的變數：

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (set-vs-map)"?>
```dart
var names = <String>{};
// Set<String> names = {}; // This works, too.
// var names = {}; // Creates a map, not a set.
```

{{site.alert.info}}

  **Set or map?** The syntax for map literals is similar to that for set
  literals. Because map literals came first, `{}` defaults to the `Map` type. If
  you forget the type annotation on `{}` or the variable it's assigned to, then
  Dart creates an object of type `Map<dynamic, dynamic>`.

  **Set 還是 map?** Map 字面量語法相似於 Set 字面量語法。
  因為先有的 Map 字面量語法，所以 `{}` 預設是 `Map` 型別。
  如果忘記在 `{}` 上註釋型別或賦值到一個未宣告型別的變數上，
  那麼 Dart 會建立一個類別型為 `Map<dynamic, dynamic>` 的物件。

{{site.alert.end}}

Add items to an existing set using the `add()` or `addAll()` methods:

使用 `add()` 方法或 `addAll()` 方法向已存在的 Set 中新增專案：

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (set-add-items)"?>
```dart
var elements = <String>{};
elements.add('fluorine');
elements.addAll(halogens);
```

Use `.length` to get the number of items in the set:

使用 `.length` 可以獲取 Set 中元素的數量：

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (set-length)"?>
```dart
var elements = <String>{};
elements.add('fluorine');
elements.addAll(halogens);
assert(elements.length == 5);
```

To create a set that's a compile-time constant,
add `const` before the set literal:

可以在 Set 變數前新增 `const` 關鍵字建立一個 Set 編譯時常量：

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (const-set)"?>
```dart
final constantSet = const {
  'fluorine',
  'chlorine',
  'bromine',
  'iodine',
  'astatine',
};
// constantSet.add('helium'); // This line will cause an error.
```

Sets support spread operators (`...` and `...?`)
and collection `if` and `for`,
just like lists do.
For more information, see the
[list spread operator](#spread-operator) and
[list collection operator](#collection-operators) discussions.

從 Dart 2.3 開始，Set 可以像 List 一樣支援使用擴充運運算元（`...` 和 `...?`）
以及 Collection `if` 和 `for` 操作。
你可以查閱 [List 擴充運運算元](#spread-operator) 和
[List 集合運運算元](#collection-operators) 獲取更多相關資訊。

For more information about sets, see
[Generics](#generics) and
[Sets](/guides/libraries/library-tour#sets).

你也可以查閱 [泛型](#generics) 以及
[Set](/guides/libraries/library-tour#sets) 獲取更多相關資訊。

### Maps

In general, a map is an object that associates keys and values. Both
keys and values can be any type of object. Each *key* occurs only once,
but you can use the same *value* multiple times. Dart support for maps
is provided by map literals and the [`Map`][] type.

通常來說，Map 是用來關聯 keys 和 values 的物件。其中鍵和值都可以是任何型別的物件。
每個 *鍵* 只能出現一次但是 *值* 可以重複出現多次。
Dart 中 Map 提供了 Map 字面量以及 [`Map`][] 型別兩種形式的 Map。

Here are a couple of simple Dart maps, created using map literals:

下面是一對使用 Map 字面量建立 Map 的例子：

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (map-literal)"?>
```dart
var gifts = {
  // Key:    Value
  'first': 'partridge',
  'second': 'turtledoves',
  'fifth': 'golden rings'
};

var nobleGases = {
  2: 'helium',
  10: 'neon',
  18: 'argon',
};
```

{{site.alert.note}}

  Dart infers that `gifts` has the type `Map<String, String>` and `nobleGases`
  has the type `Map<int, String>`. If you try to add the wrong type of value to
  either map, the analyzer or runtime raises an error. For more information,
  read about [type inference.](/guides/language/type-system#type-inference)

  Dart 將 `gifts` 變數的型別推斷為 `Map<String, String>`，
  而將 `nobleGases` 的型別推斷為 `Map<int, String>`。
  如果你向這兩個 Map 物件中新增不正確的型別值，將導致執行時例外。
  你可以閱讀
  [型別推斷](/guides/language/type-system#type-inference) 獲取更多相關資訊。

{{site.alert.end}}

You can create the same objects using a Map constructor:

你也可以使用 Map 的構造器建立 Map：

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (map-constructor)"?>
```dart
var gifts = Map<String, String>();
gifts['first'] = 'partridge';
gifts['second'] = 'turtledoves';
gifts['fifth'] = 'golden rings';

var nobleGases = Map<int, String>();
nobleGases[2] = 'helium';
nobleGases[10] = 'neon';
nobleGases[18] = 'argon';
```

{{site.alert.note}}
  If you come from a language like C# or Java, you might expect to see `new Map()` 
  instead of just `Map()`. In Dart, the `new` keyword is optional.
  For details, see [Using constructors](#using-constructors).

  如果你之前是使用的 C# 或 Java 這樣的語言，也許你想使用 `new Map()` 構造 Map 物件。但是在 Dart 中，`new` 關鍵詞是可選的。(譯者注：且不被建議使用)
  你可以查閱 [建構函式的使用](#using-constructors) 獲取更多相關資訊。

{{site.alert.end}}

Add a new key-value pair to an existing map
using the subscript assignment operator (`[]=`):

向現有的 Map 中新增鍵值對與 JavaScript 的操作類似：

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (map-add-item)"?>
```dart
var gifts = {'first': 'partridge'};
gifts['fourth'] = 'calling birds'; // Add a key-value pair
```

Retrieve a value from a map using the subscript operator (`[]`):

從一個 Map 中獲取一個值的操作也與 JavaScript 類似：

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (map-retrieve-item)"?>
```dart
var gifts = {'first': 'partridge'};
assert(gifts['first'] == 'partridge');
```

If you look for a key that isn’t in a map, you get `null` in return:

如果檢索的 Key 不存在於 Map 中則會返回一個 null：

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (map-missing-key)"?>
```dart
var gifts = {'first': 'partridge'};
assert(gifts['fifth'] == null);
```

Use `.length` to get the number of key-value pairs in the map:

使用 `.length` 可以獲取 Map 中鍵值對的數量：

<?code-excerpt "misc/test/language_tour/built_in_types_test.dart (map-length)"?>
```dart
var gifts = {'first': 'partridge'};
gifts['fourth'] = 'calling birds';
assert(gifts.length == 2);
```

To create a map that's a compile-time constant,
add `const` before the map literal:

在一個 Map 字面量前新增 `const` 關鍵字可以建立一個 Map 編譯時常量：

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (const-map)"?>
```dart
final constantMap = const {
  2: 'helium',
  10: 'neon',
  18: 'argon',
};

// constantMap[2] = 'Helium'; // This line will cause an error.
```

Maps support spread operators (`...` and `...?`)
and collection `if` and `for`, just like lists do.
For details and examples, see the
[spread operator proposal][spread proposal] and the
[control flow collections proposal.][collections proposal]

Map 可以像 List 一樣支援使用擴充運運算元（`...` 和 `...?`）
以及集合的 if 和 for 操作。
你可以查閱 [List 擴充運運算元](#spread-operator)
和 [List 集合運運算元](#collection-operators) 獲取更多相關資訊。

For more information about maps, see the
[generics](#generics) section and
the library tour's coverage of
the [`Maps` API](/guides/libraries/library-tour#maps).

你也可以查閱 [泛型](#generics) 以及
[`Maps` API](/guides/libraries/library-tour#maps) 獲取更多相關資訊。

<a id="characters"></a>
### Runes and grapheme clusters

### Runes 與 grapheme clusters

In Dart, [runes][] expose the Unicode code points of a string.
You can use the [characters package][]
to view or manipulate user-perceived characters,
also known as
[Unicode (extended) grapheme clusters.][grapheme clusters]

在 Dart 中，[runes][] 公開了字串的 Unicode 碼位。
使用 [characters 包][characters package] 來存取
或者操作使用者感知的字元，也被稱為
[Unicode (擴充) grapheme clusters][grapheme clusters]。

Unicode defines a unique numeric value for each letter, digit,
and symbol used in all of the world's writing systems.
Because a Dart string is a sequence of UTF-16 code units,
expressing Unicode code points within a string requires
special syntax.

Unicode 編碼為每一個字母、數字和符號都定義了一個唯一的數值。
因為 Dart 中的字串是一個 UTF-16 的字元序列，
所以如果想要表示 32 位的 Unicode 數值則需要一種特殊的語法。

The usual way to express a Unicode code point is
`\uXXXX`, where XXXX is a 4-digit hexadecimal value.
For example, the heart character (♥) is `\u2665`.
To specify more or less than 4 hex digits,
place the value in curly brackets.
For example, the laughing emoji (😆) is `\u{1f606}`.

表示 Unicode 字元的常見方式是使用 `\uXXXX`，
其中 XXXX 是一個四位數的 16 進位制數字。
例如心形字元（♥）的 Unicode 為 `\u2665`。
對於不是四位數的 16 進位制數字，
需要使用大括號將其括起來。
例如大笑的 emoji 表情（😆）的 Unicode 為 `\u{1f600}`。

If you need to read or write individual Unicode characters,
use the `characters` getter defined on String
by the characters package.
The returned [`Characters`][] object is the string as
a sequence of grapheme clusters.
Here's an example of using the characters API:

如果你需要讀寫單個 Unicode 字元，可以使用 characters 套件中定義
的 `characters` getter。
它將返回 [`Characters`][] 物件作為一系列 grapheme clusters
的字串。下面是使用 characters API 的範例：

<?code-excerpt "misc/lib/language_tour/characters.dart"?>
```dart
import 'package:characters/characters.dart';

void main() {
  var hi = 'Hi 🇩🇰';
  print(hi);
  print('The end of the string: ${hi.substring(hi.length - 1)}');
  print('The last character: ${hi.characters.last}');
}
```

The output, depending on your environment, looks something like this:

輸出取決於你的環境，大致類似於：

```terminal
$ dart run bin/main.dart
Hi 🇩🇰
The end of the string: ???
The last character: 🇩🇰
```

For details on using the characters package to manipulate strings,
see the [example][characters example] and [API reference][characters API]
for the characters package.

有關使用 characters 包操作字串的詳細資訊，請參閱用於 characters 套件的[範例][characters example]
和 [API 參考][characters API]。

### Symbols

A [`Symbol`][] object
represents an operator or identifier declared in a Dart program. You
might never need to use symbols, but they're invaluable for APIs that
refer to identifiers by name, because minification changes identifier
names but not identifier symbols.

[`Symbol`][] 表示 Dart 中宣告的運運算元或者識別符號。
你幾乎不會需要 Symbol，但是它們對於那些透過名稱參考識別符號的 API 很有用，
因為程式碼壓縮後，儘管識別符號的名稱會改變，但是它們的 Symbol 會保持不變。

To get the symbol for an identifier, use a symbol literal, which is just
`#` followed by the identifier:

可以使用在識別符號前加 `#` 字首來獲取 Symbol：

```nocode
#radix
#bar
```

{% comment %}
The code from the following excerpt isn't actually what is being shown in the page

<?code-excerpt "misc/lib/language_tour/built_in_types.dart (symbols)"?>
```dart
// MOVE TO library tour?

void main() {
  print(Function.apply(int.parse, ['11']));
  print(Function.apply(int.parse, ['11'], {#radix: 16}));
  print(Function.apply(int.parse, ['11a'], {#onError: handleError}));
  print(Function.apply(
      int.parse, ['11a'], {#radix: 16, #onError: handleError}));
}

int handleError(String source) {
  return 0;
}
```
{% endcomment %}

Symbol literals are compile-time constants.

Symbol 字面量是編譯時常量。

## Functions

## 函式

Dart is a true object-oriented language, so even functions are objects
and have a type, [Function.][Function API reference]
This means that functions can be assigned to variables or passed as arguments
to other functions. You can also call an instance of a Dart class as if
it were a function. For details, see [Callable classes](#callable-classes).

Dart 是一種真正面向物件的語言，所以即便函式也是物件並且型別為
[Function][Function API reference]，這意味著函式
可以被賦值給變數或者作為其它函式的引數。
你也可以像呼叫函式一樣呼叫 Dart 類別的例項。
詳情請查閱 [可呼叫的類](#callable-classes)。

Here’s an example of implementing a function:

下面是定義一個函式的例子：

<?code-excerpt "misc/lib/language_tour/functions.dart (function)"?>
```dart
bool isNoble(int atomicNumber) {
  return _nobleGases[atomicNumber] != null;
}
```

Although Effective Dart recommends
[type annotations for public APIs](/guides/language/effective-dart/design#do-type-annotate-fields-and-top-level-variables-if-the-type-isnt-obvious),
the function still works if you omit the types:

雖然高效 Dart 指南建議在
[公開的 API 上定義返回型別](/guides/language/effective-dart/design#do-type-annotate-fields-and-top-level-variables-if-the-type-isnt-obvious)，
不過即便不定義，該函式也依然有效：

<?code-excerpt "misc/lib/language_tour/functions.dart (function-omitting-types)"?>
```dart
isNoble(atomicNumber) {
  return _nobleGases[atomicNumber] != null;
}
```

For functions that contain just one expression, you can use a shorthand
syntax:

如果函式體內只包含一個表示式，你可以使用簡寫語法：

<?code-excerpt "misc/lib/language_tour/functions.dart (function-shorthand)"?>
```dart
bool isNoble(int atomicNumber) => _nobleGases[atomicNumber] != null;
```

The <code>=> <em>expr</em></code> syntax is a shorthand for
<code>{ return <em>expr</em>; }</code>. The `=>` notation
is sometimes referred to as _arrow_ syntax.

語法 <code>=> <em>表示式</em></code>
是 <code>{ return <em>表示式</em>; }</code> 的簡寫，
`=>` 有時也稱之為 **箭頭** 函式。

{{site.alert.note}}

  Only an *expression*—not a *statement*—can appear between the arrow (=\>) and
  the semicolon (;). For example, you can’t put an [if statement](#if-and-else)
  there, but you can use a [conditional expression](#conditional-expressions).

  在 =\> 與 ; 之間的只能是 *表示式* 而非 *陳述式*。
  比如你不能將一個 [if陳述式](#if-and-else) 放在其中，
  但是可以放置 [條件表示式](#conditional-expressions)。

{{site.alert.end}}

### Parameters

### 引數

A function can have any number of *required positional* parameters. These can be
followed either by *named* parameters or by *optional positional* parameters
(but not both).

函式可以有兩種形式的引數：**必要引數** 和 **可選引數**。
必要引數定義在引數列表前面，可選引數則定義在必要引數後面。
可選引數可以是 **命名的** 或 **位置的**。

{{site.alert.note}}

  Some APIs—notably [Flutter][] widget constructors—use only named
  parameters, even for parameters that are mandatory. See the next section for
  details.

  某些 API（特別是 [Flutter][] 控制項的構造器）只使用命名引數，
  即便引數是強制性的。可以查閱下一節獲取更多資訊。

{{site.alert.end}}

You can use [trailing commas][] when you pass arguments to a function
or when you define function parameters.

向函式傳入引數或者定義函式引數時，可以使用 [尾逗號][trailing commas]。

#### Named parameters

#### 命名引數

Named parameters are optional
unless they're explicitly marked as `required`.

命名引數預設為可選引數，除非他們被特別標記為 `required`。

When defining a function, use
<code>{<em>param1</em>, <em>param2</em>, …}</code>
to specify named parameters.
If you don't provide a default value
or mark a named parameter as `required`,
their types must be nullable
as their default value will be `null`:

定義函式時，使用
<code>{<em>引數1</em>, <em>引數2</em>, …}</code>
來指定命名引數：
如果你沒有提供一個預設值，
也沒有使用 `required` 標記的話，
那麼它一定可空的型別，
因為他們的預設值會是 `null`：

<?code-excerpt "misc/lib/language_tour/functions.dart (specify-named-parameters)"?>
```dart
/// Sets the [bold] and [hidden] flags ...
void enableFlags({bool? bold, bool? hidden}) {...}
```

When calling a function, 
you can specify named arguments using
<code><em>paramName</em>: <em>value</em></code>. 
For example:

當呼叫函式時，你可以使用 <code><em>引數名</em>: <em>引數值</em></code>
指定一個命名引數的值。例如：

<?code-excerpt "misc/lib/language_tour/functions.dart (use-named-parameters)"?>
```dart
enableFlags(bold: true, hidden: false);
```

<a id="default-parameters"></a>
To define a default value for a named parameter besides `null`,
use `=` to specify a default value.
The specified value must be a compile-time constant.
For example:

<a id="default-parameters"></a>
你可以使用 `=` 來為一個命名引數指定除了 `null` 以外的預設值。
指定的預設值必須要為編譯時的常量，例如：

<?code-excerpt "misc/lib/language_tour/functions.dart (named-parameter-default-values)"?>
```dart
/// Sets the [bold] and [hidden] flags ...
void enableFlags({bool bold = false, bool hidden = false}) {...}

// bold will be true; hidden will be false.
enableFlags(bold: true);
```

If you instead want a named parameter to be mandatory,
requiring callers to provide a value for the parameter,
annotate them with `required`:

如果你希望一個命名引數是強制需要使用的，呼叫者需要提供它的值，
則你可以使用 `required` 進行宣告：

<?code-excerpt "misc/lib/language_tour/functions.dart (required-named-parameters)" replace="/required/[!$&!]/g"?>
```dart
const Scrollbar({super.key, [!required!] Widget child});
```

If someone tries to create a `Scrollbar`
without specifying the `child` argument,
then the analyzer reports an issue.

當你建立一個不帶 `child` 引數的 `Scrollbar` 時，
分析器就會報告這裡出了問題。

{{site.alert.note}}

  A parameter marked as `required`
  can still be nullable:

  一個標記了 `required` 的引數
  仍然是可空的型別:

  <?code-excerpt "misc/lib/language_tour/functions.dart (required-named-parameters-nullable)" replace="/Widget\?/[!$&!]/g; /ScrollbarTwo/Scrollbar/g;"?>
  ```dart
  const Scrollbar({super.key, required [!Widget?!] child});
  ```
{{site.alert.end}}

You might want to place positional arguments first,
but Dart doesn't require it.
Dart allows named arguments to be placed anywhere in the
argument list when it suits your API:

儘管將位置引數放在最前面通常比較合理，但你也可以將命名引數放在引數列表的任意位置，
讓整個呼叫的方式看起來更適合你的 API：

<?code-excerpt "misc/lib/language_tour/functions.dart (named-arguments-anywhere)"?>
```dart
repeat(times: 2, () {
  ...
});
```

#### Optional positional parameters

#### 可選的位置引數

Wrapping a set of function parameters in `[]`
marks them as optional positional parameters.
If you don't provide a default value,
their types must be nullable
as their default value will be `null`:

使用 `[]` 將一系列引數包裹起來，即可將其標記為位置引數，
因為它們的預設值是 `null`，所以如果你沒有提供預設值的話，
它們的型別必須得是允許為空 (nullable) 的型別。

<?code-excerpt "misc/test/language_tour/functions_test.dart (optional-positional-parameters)"?>
```dart
String say(String from, String msg, [String? device]) {
  var result = '$from says $msg';
  if (device != null) {
    result = '$result with a $device';
  }
  return result;
}
```

Here’s an example of calling this function
without the optional parameter:

下面是不使用可選引數呼叫上述函式的範例

<?code-excerpt "misc/test/language_tour/functions_test.dart (call-without-optional-param)"?>
```dart
assert(say('Bob', 'Howdy') == 'Bob says Howdy');
```

And here’s an example of calling this function with the third parameter:

下面是使用可選引數呼叫上述函式的範例：

<?code-excerpt "misc/test/language_tour/functions_test.dart (call-with-optional-param)"?>
```dart
assert(say('Bob', 'Howdy', 'smoke signal') ==
    'Bob says Howdy with a smoke signal');
```

To define a default value for an optional positional parameter besides `null`,
use `=` to specify a default value.
The specified value must be a compile-time constant.
For example:

你可以使用 `=` 來為一個位置可選引數指定除了 `null` 以外的預設值。
指定的預設值必須要為編譯時的常量，例如：

<?code-excerpt "misc/test/language_tour/functions_test.dart (optional-positional-param-default)"?>
```dart
String say(String from, String msg, [String device = 'carrier pigeon']) {
  var result = '$from says $msg with a $device';
  return result;
}

assert(say('Bob', 'Howdy') == 'Bob says Howdy with a carrier pigeon');
```

### The main() function

### main() 函式

Every app must have a top-level `main()` function, which serves as the
entrypoint to the app. The `main()` function returns `void` and has an
optional `List<String>` parameter for arguments.

每個 Dart 程式都必須有一個 `main()` 最上層函式作為程式的入口，
`main()` 函式返回值為 `void` 並且有一個 `List<String>` 型別的可選引數。

Here's a simple `main()` function:

下面是一個簡單 `main()` 函式：

<?code-excerpt "misc/test/samples_test.dart (hello-world)"?>
```dart
void main() {
  print('Hello, World!');
}
```

Here's an example of the `main()` function for a command-line app that
takes arguments:

下面是使用命令列存取帶引數的 `main()` 函式範例：

<?code-excerpt "misc/test/language_tour/functions_test.dart (main-args)"?>
```dart
// Run the app like this: dart args.dart 1 test
void main(List<String> arguments) {
  print(arguments);

  assert(arguments.length == 2);
  assert(int.parse(arguments[0]) == 1);
  assert(arguments[1] == 'test');
}
```

You can use the [args library]({{site.pub-pkg}}/args) to
define and parse command-line arguments.

你可以透過使用 [引數庫]({{site.pub}}/packages/args) 來定義和解析命令列引數。

### Functions as first-class objects

### 函式是一級物件

You can pass a function as a parameter to another function. For example:

可以將函式作為引數傳遞給另一個函式。例如：

<?code-excerpt "misc/lib/language_tour/functions.dart (function-as-param)"?>
```dart
void printElement(int element) {
  print(element);
}

var list = [1, 2, 3];

// Pass printElement as a parameter.
list.forEach(printElement);
```

You can also assign a function to a variable, such as:

你也可以將函式賦值給一個變數，比如：

<?code-excerpt "misc/test/language_tour/functions_test.dart (function-as-var)"?>
```dart
var loudify = (msg) => '!!! ${msg.toUpperCase()} !!!';
assert(loudify('hello') == '!!! HELLO !!!');
```

This example uses an anonymous function.
More about those in the next section.

該範例中使用了匿名函式。下一節會有更多與其相關的介紹。

### Anonymous functions

### 匿名函式

Most functions are named, such as `main()` or `printElement()`.
You can also create a nameless function
called an _anonymous function_, or sometimes a _lambda_ or _closure_.
You might assign an anonymous function to a variable so that,
for example, you can add or remove it from a collection.

大多數方法都是有名字的，比如 `main()` 或 `printElement()`。
你可以建立一個沒有名字的方法，稱之為 **匿名函式**、
**Lambda 表示式** 或 **Closure 閉包**。
你可以將匿名方法賦值給一個變數然後使用它，
比如將該變數新增到集合或從中刪除。

An anonymous function looks similar
to a named function—zero or more parameters, separated by commas
and optional type annotations, between parentheses.

匿名方法看起來與命名方法類似，在括號之間可以定義引數，引數之間用逗號分割。

The code block that follows contains the function's body:

後面大括號中的內容則為函式體：

<code>
([[<em>型別</em>] <em>引數</em>[, …]]) { <br>
&nbsp;&nbsp;<em>函式體</em>; <br>
}; <br>
</code>

The following example defines an anonymous function
with an untyped parameter, `item`,
and passes it to the `map` function.
The function, invoked for each item in the list,
converts each string to uppercase.
Then in the anonymous function passed to `forEach`,
each converted string is printed out alongside its length.

下面程式碼定義了只有一個引數 `item` 且沒有引數型別的匿名方法。
List 中的每個元素都會呼叫這個函式，列印元素位置和值的字串：

<?code-excerpt "misc/test/language_tour/functions_test.dart (anonymous-function)"?>
```dart
const list = ['apples', 'bananas', 'oranges'];
list.map((item) {
  return item.toUpperCase();
}).forEach((item) {
  print('$item: ${item.length}');
});
```

Click **Run** to execute the code.

點選 **Run** 按鈕執行程式碼。

<?code-excerpt "misc/test/language_tour/functions_test.dart (anonymous-function-main)"?>
```dart:run-dartpad:height-400px:ga_id-anonymous_functions
void main() {
  const list = ['apples', 'bananas', 'oranges'];
  list.map((item) {
    return item.toUpperCase();
  }).forEach((item) {
    print('$item: ${item.length}');
  });
}
```

If the function contains only a single expression or return statement,
you can shorten it using arrow notation. 
Paste the following line into DartPad and click **Run**
to verify that it is functionally equivalent.

如果函式體內只有一行返回陳述式，你可以使用胖箭頭縮寫法。
貼上下面程式碼到 DartPad 中並點選執行按鈕，驗證兩個函式是否一致。

<?code-excerpt "misc/test/language_tour/functions_test.dart (anon-func)"?>
```dart
list
    .map((item) => item.toUpperCase())
    .forEach((item) => print('$item: ${item.length}'));
```


### Lexical scope

### 詞法作用域

Dart is a lexically scoped language, which means that the scope of
variables is determined statically, simply by the layout of the code.
You can “follow the curly braces outwards” to see if a variable is in
scope.

Dart 是詞法有作用域語言，變數的作用域在寫程式碼的時候就確定了，
大括號內定義的變數只能在大括號記憶體取，與 Java 類似。

Here is an example of nested functions with variables at each scope
level:

下面是一個巢狀(Nesting)函式中變數在多個作用域中的範例：

<?code-excerpt "misc/test/language_tour/functions_test.dart (nested-functions)"?>
```dart
bool topLevel = true;

void main() {
  var insideMain = true;

  void myFunction() {
    var insideFunction = true;

    void nestedFunction() {
      var insideNestedFunction = true;

      assert(topLevel);
      assert(insideMain);
      assert(insideFunction);
      assert(insideNestedFunction);
    }
  }
}
```

Notice how `nestedFunction()` can use variables from every level, all
the way up to the top level.

注意 `nestedFunction()` 函式可以存取包括最上層變數在內的所有的變數。

### Lexical closures

### 詞法閉包

A *closure* is a function object that has access to variables in its
lexical scope, even when the function is used outside of its original
scope.

**閉包** 即一個函式物件，即使函式物件的呼叫在它原始作用域之外，
依然能夠存取在它詞法作用域內的變數。

Functions can close over variables defined in surrounding scopes. In the
following example, `makeAdder()` captures the variable `addBy`. Wherever the
returned function goes, it remembers `addBy`.

函式可以封閉定義到它作用域內的變數。接下來的範例中，
函式 `makeAdder()` 捕獲了變數 `addBy`。
無論函式在什麼時候返回，它都可以使用捕獲的 `addBy` 變數。

<?code-excerpt "misc/test/language_tour/functions_test.dart (function-closure)"?>
```dart
/// Returns a function that adds [addBy] to the
/// function's argument.
Function makeAdder(int addBy) {
  return (int i) => addBy + i;
}

void main() {
  // Create a function that adds 2.
  var add2 = makeAdder(2);

  // Create a function that adds 4.
  var add4 = makeAdder(4);

  assert(add2(3) == 5);
  assert(add4(3) == 7);
}
```


### Testing functions for equality

### 測試函式是否相等

Here's an example of testing top-level functions, static methods, and
instance methods for equality:

下面是最上層函式、靜態方法和例項方法相等性的測試範例：

<?code-excerpt "misc/lib/language_tour/function_equality.dart"?>
```dart
void foo() {} // A top-level function

class A {
  static void bar() {} // A static method
  void baz() {} // An instance method
}

void main() {
  Function x;

  // Comparing top-level functions.
  x = foo;
  assert(foo == x);

  // Comparing static methods.
  x = A.bar;
  assert(A.bar == x);

  // Comparing instance methods.
  var v = A(); // Instance #1 of A
  var w = A(); // Instance #2 of A
  var y = w;
  x = w.baz;

  // These closures refer to the same instance (#2),
  // so they're equal.
  assert(y.baz == x);

  // These closures refer to different instances,
  // so they're unequal.
  assert(v.baz != w.baz);
}
```


### Return values

### 返回值

All functions return a value. If no return value is specified, the
statement `return null;` is implicitly appended to the function body.

所有的函式都有返回值。沒有顯示返回陳述式的函式最後一行預設為執行 `return null;`。

<?code-excerpt "misc/test/language_tour/functions_test.dart (implicit-return-null)"?>
```dart
foo() {}

assert(foo() == null);
```


## Operators

## 運算子

Dart supports the operators shown in the following table.
The table shows Dart's operator associativity 
and [operator precedence](#operator-precedence-example) from highest to lowest,
which are an **approximation** of Dart's operator relationships.
You can implement many of these [operators as class members](#_operators).

Dart 支援下表所示的運運算元，它也體現了 Dart
運算子的關聯性和 [優先順序](#operator-precedence-example) 的從高到低的順序。
這也是 Dart 運算子關係的近似值。
你可以將這些運算子實現為 [一個類別的成員](#_operators)。

|-----------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------|
| Description                             | Operator                                                                                                                                                                                          | Associativity |
|-----------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------|
| unary postfix                           | <code><em>expr</em>++</code>    <code><em>expr</em>--</code>    `()`    `[]`    `?[]`    `.`    `?.`    `!`                                                                                       | None          |
| unary prefix                            | <code>-<em>expr</em></code>    <code>!<em>expr</em></code>    <code>~<em>expr</em></code>    <code>++<em>expr</em></code>    <code>--<em>expr</em></code>      <code>await <em>expr</em></code>    | None          |
| multiplicative                          | `*`    `/`    `%`    `~/`                                                                                                                                                                         | Left          |
| additive                                | `+`    `-`                                                                                                                                                                                        | Left          |
| shift                                   | `<<`    `>>`    `>>>`                                                                                                                                                                             | Left          |
| bitwise AND                             | `&`                                                                                                                                                                                               | Left          |
| bitwise XOR                             | `^`                                                                                                                                                                                               | Left          |
| bitwise OR                              | `|`                                                                                                                                                                                               | Left          |
| relational&nbsp;and&nbsp;type&nbsp;test | `>=`    `>`    `<=`    `<`    `as`    `is`    `is!`                                                                                                                                               | None          |
| equality                                | `==`    `!=`                                                                                                                                                                                      | None          |
| logical AND                             | `&&`                                                                                                                                                                                              | Left          |
| logical OR                              | `||`                                                                                                                                                                                              | Left          |
| if null                                 | `??`                                                                                                                                                                                              | Left          |
| conditional                             | <code><em>expr1</em> ? <em>expr2</em> : <em>expr3</em></code>                                                                                                                                     | Right         |
| cascade                                 | `..` &nbsp;&nbsp; `?..`                                                                                                                                                                           | Right         |
| assignment                              | `=`    `*=`    `/=`    `+=`    `-=`    `&=`    `^=`    <em>etc.</em>                                                                                                                              | Right         |
{:.table .table-striped}

{{site.alert.warning}}

  The previous table should only be used as a helpful guide.
  The notion of operator precedence and associativity
  is an approximation of the truth found in the language grammar.
  You can find the authoritative behavior of Dart's operator relationships
  in the grammar defined in the [Dart language specification][].

  上述的表格只是作為一個比較實用的指南，
  運運算元的優先順序和關聯性概念是從程式語言的語法中整理發現的。
  你可以在 [Dart 程式設計語言規範][Dart language specification]
  文件中找到更為權威的解釋。

{{site.alert.end}}

{% comment %}
|--------------------------+------------------------------------------------|
| 描述                     | 運算子                                         |
|--------------------------|------------------------------------------------|
| 一元后綴                 | <code><em>表示式</em>++</code>   <code><em>表示式</em>--</code> `()` `[]` `.` `?.` `!` |
| 一元字首      | <code>-<em>表示式</em></code>   <code>!<em>表示式</em></code>   <code>~<em>表示式</em></code>   <code>++<em>表示式</em></code>   <code>--<em>表示式</em></code>  |
| 乘除法        | `*`   `/`   `%`    `~/`                                     |
| 加減法        | `+`   `-`                                                    |
| 位運算        | `<<`   `>>`   `>>>`                                         |
| 二進位制與      | `&`                                                           |
| 二進位制異或     | `^`                                                           |
| 二進位制或      | `|`                                                           |
| 關係和型別測試 | `>=`   `>`   `<=`   `<`   `as`   `is`   `is!`           |
| 相等判斷      | `==`   `!=`                                                 |
| 邏輯與        | `&&`                                                          |
| 邏輯或        | `||`                                                          |
| 空判斷        | `??`                                                          |
| 條件表示式     | <code><em>表示式 1</em> ? <em>表示式 2</em> : <em>表示式 3</em></code> |
| 級聯          | `..`   `?..`                                      |
| 賦值          | `=`   `*=`   `/=`   `+=`   `-=`   `&=`   `^=`   <em>等等……</em> |

#TODO
先讓英文原文完成編譯，然後再動 markdown 表格吧，在原始碼中看著太亂了。

{% endcomment %}


When you use operators, you create expressions. Here are some examples
of operator expressions:

一旦你使用了運算子，就建立了表示式。下面是一些運算子表示式的範例：

<?code-excerpt "misc/test/language_tour/operators_test.dart (expressions)" replace="/,//g"?>
```dart
a++
a + b
a = b
a == b
c ? a : b
a is T
```

<a id="operator-precedence-example"></a>
In the [operator table](#operators),
each operator has higher precedence than the operators in the rows
that follow it. For example, the multiplicative operator `%` has higher
precedence than (and thus executes before) the equality operator `==`,
which has higher precedence than the logical AND operator `&&`. That
precedence means that the following two lines of code execute the same
way:

<a id="operator-precedence-example"></a>
在 [運算子表](#operators) 中，運算子的優先順序按先後排列，
即第一行優先順序最高，最後一行優先順序最低，
而同一行中，最左邊的優先順序最高，最右邊的優先順序最低。
例如：`%` 運算子優先順序高於 `==` ，而 `==` 高於 `&&`。
根據優先順序規則，那麼意味著以下兩行程式碼執行的效果相同：

<?code-excerpt "misc/test/language_tour/operators_test.dart (precedence)"?>
```dart
// Parentheses improve readability.
if ((n % i == 0) && (d % i == 0)) ...

// Harder to read, but equivalent.
if (n % i == 0 && d % i == 0) ...
```

{{site.alert.warning}}

  For operators that take two operands, the leftmost operand determines which
  method is used. For example, if you have a `Vector` object and
  a `Point` object, then `aVector + aPoint` uses `Vector` addition (`+`).

  對於有兩個運算元的運算子，左邊的運算元決定了運算子的功能。
  比如對於一個 Vector 物件和一個 Point 物件，
  表示式 `aVector + aPoint` 中所使用的是 Vector 物件中定義的相加運算子 (`+`)。

{{site.alert.end}}


### Arithmetic operators

### 算術運算子

Dart supports the usual arithmetic operators, as shown in the following table.

Dart 支援常用的算術運算子：

|-----------------------------+-------------------------------------------|
| 運算子                       | 描述                                       |
|-----------------------------+-------------------------------------------|
| `+`                         | 加
| `-`                         | 減
| <code>-<em>表示式</em></code>| 一元負, 也可以作為反轉（反轉表示式的符號）
| `*`                         | 乘
| `/`                         | 除
| `~/`                        | 除並取整
| `%`                         | 取模
{:.table .table-striped}

Example:

範例：

<?code-excerpt "misc/test/language_tour/operators_test.dart (arithmetic)"?>
```dart
assert(2 + 3 == 5);
assert(2 - 3 == -1);
assert(2 * 3 == 6);
assert(5 / 2 == 2.5); // Result is a double
assert(5 ~/ 2 == 2); // Result is an int
assert(5 % 2 == 1); // Remainder

assert('5/2 = ${5 ~/ 2} r ${5 % 2}' == '5/2 = 2 r 1');
```

Dart also supports both prefix and postfix increment and decrement
operators.

Dart 還支援自增自減操作。

|-----------------------------+-------------------------------------------|
| Operator                    | Meaning                                   |
|-----------------------------+-------------------------------------------|
| <code>++<em>var</em></code> | <code><em>var</em> = <em>var</em> + 1</code> (表示式的值為 <code><em>var</em> + 1</code>)
| <code><em>var</em>++</code> | <code><em>var</em> = <em>var</em> + 1</code> (表示式的值為 <code><em>var</em></code>)
| <code>--<em>var</em></code> | <code><em>var</em> = <em>var</em> - 1</code> (表示式的值為 <code><em>var</em> - 1</code>)
| <code><em>var</em>--</code> | <code><em>var</em> = <em>var</em> - 1</code> (表示式的值為 <code><em>var</em></code>)
{:.table .table-striped}

Example:

範例：

<?code-excerpt "misc/test/language_tour/operators_test.dart (increment-decrement)"?>
```dart
int a;
int b;

a = 0;
b = ++a; // Increment a before b gets its value.
assert(a == b); // 1 == 1

a = 0;
b = a++; // Increment a AFTER b gets its value.
assert(a != b); // 1 != 0

a = 0;
b = --a; // Decrement a before b gets its value.
assert(a == b); // -1 == -1

a = 0;
b = a--; // Decrement a AFTER b gets its value.
assert(a != b); // -1 != 0
```


### Equality and relational operators

### 關係運算符

The following table lists the meanings of equality and relational operators.

下表列出了關係運算符及含義：

|-----------+-------------------------------------------|
| Operator  | Meaning                                   |
|-----------+-------------------------------------------|
| `==`      |       相等
| `!=`      |       不等
| `>`       |       大於
| `<`       |       小於
| `>=`      |       大於等於
| `<=`      |       小於等於
{:.table .table-striped}

To test whether two objects x and y represent the same thing, use the
`==` operator. (In the rare case where you need to know whether two
objects are the exact same object, use the [identical()][]
function instead.) Here’s how the `==` operator works:

要判斷兩個物件 x 和 y 是否表示相同的事物使用 `==` 即可。
（在極少數情況下，可能需要使用 [identical()][] 函式來確定兩個物件是否完全相同）。
下面是 `==` 運算子的一些規則：

1.  If *x* or *y* is null, return true if both are null, and false if only
    one is null.

    當 **x** 和 **y** 同時為空時返回 true，而只有一個為空時返回 false。

2.  Return the result of invoking the `==` method on *x* with the argument *y*.
    (That’s right, operators such as `==` are methods that
    are invoked on their first operand.
    For details, see [Operators](#_operators).)

    返回對 **x** 呼叫 `==` 方法的結果，引數為 **y**。
    （像 `==` 這樣的運運算元是對左側內容進行呼叫的。
    詳情請查閱 [運運算元](#_operators)。）

Here’s an example of using each of the equality and relational
operators:

下面的程式碼給出了每一種關係運算符的範例：

<?code-excerpt "misc/test/language_tour/operators_test.dart (relational)"?>
```dart
assert(2 == 2);
assert(2 != 3);
assert(3 > 2);
assert(2 < 3);
assert(3 >= 3);
assert(2 <= 3);
```


### Type test operators

### 型別判斷運算子

The `as`, `is`, and `is!` operators are handy for checking types at
runtime.

`as`、`is`、`is!` 運算子是在執行時判斷物件型別的運算子。

|-----------+-------------------------------------------|
| Operator  | Meaning                                   |
|-----------+-------------------------------------------|
| `as`      | Typecast (also used to specify [library prefixes](#specifying-a-library-prefix))
| `as`      | 型別轉換（也用作指定 [庫字首](#specifying-a-library-prefix))）
| `is`      | True if the object has the specified type
| `is`      | 如果物件是指定型別則返回 true
| `is!`     | True if the object doesn't have the specified type
| `is!`     | 如果物件是指定型別則返回 false
{:.table .table-striped}

The result of `obj is T` is true if `obj` implements the interface
specified by `T`. For example, `obj is Object?` is always true.

當且僅當 `obj` 實現了 `T` 的介面，`obj is T` 才是 true。
例如 `obj is Object` 總為 true，因為所有類都是 Object 的子類別。

Use the `as` operator to cast an object to a particular type if and only if
you are sure that the object is of that type. Example:

僅當你確定這個物件是該型別的時候，
你才可以使用 `as` 運運算元可以把物件轉換為特定的型別。例如：

<?code-excerpt "misc/lib/language_tour/classes/employee.dart (emp as Person)"?>
```dart
(employee as Person).firstName = 'Bob';
```

If you aren't sure that the object is of type `T`, then use `is T` to check the
type before using the object.

如果你不確定這個物件型別是不是 `T`，
請在轉型前使用 `is T` 檢查型別。

<?code-excerpt "misc/lib/language_tour/classes/employee.dart (emp is Person)"?>
```dart
if (employee is Person) {
  // Type check
  employee.firstName = 'Bob';
}
```

{{site.alert.note}}

  The code isn’t equivalent. If `employee` is null or not a `Person`, the
  first example throws an exception; the second does nothing.

  上述兩種方式是有區別的：如果 `employee` 為 null 或者不為 `Person` 型別，
  則第一種方式將會丟擲例外，而第二種不會。

{{site.alert.end}}

### Assignment operators

### 賦值運算子

As you’ve already seen, you can assign values using the `=` operator.
To assign only if the assigned-to variable is null,
use the `??=` operator.

可以使用 `=` 來賦值，同時也可以使用 `??=` 來為值為 null 的變數賦值。

<?code-excerpt "misc/test/language_tour/operators_test.dart (assignment)"?>
```dart
// Assign value to a
a = value;
// Assign value to b if b is null; otherwise, b stays the same
b ??= value;
```

Compound assignment operators such as `+=` combine
an operation with an assignment.

像 `+=` 這樣的賦值運算子將算數運算子和賦值運算子組合在了一起。

| `=`  | `*=`  | `%=`  | `>>>=` | `^=`
| `+=` | `/=`  | `<<=` | `&=`   | `|=`
| `-=` | `~/=` | `>>=`
{:.table}

Here’s how compound assignment operators work:

下表解釋了複合運算子的原理：

|-----------+----------------------+-----------------------|
|    場景    |       複合運算        |        等效表示式       |
|-----------+----------------------+-----------------------|
|**假設有運算子 <em>op</em>：** | <code>a <em>op</em>= b</code> | <code>a = a <em>op</em> b</code>
|**範例：**                     |`a += b`                       | `a = a + b`
{:.table}

The following example uses assignment and compound assignment
operators:

下面的例子展示瞭如何使用賦值以及複合賦值運算子：

<?code-excerpt "misc/test/language_tour/operators_test.dart (op-assign)"?>
```dart
var a = 2; // Assign using =
a *= 3; // Assign and multiply: a = a * 3
assert(a == 6);
```


### Logical operators

### 邏輯運算子

You can invert or combine boolean expressions using the logical
operators.

使用邏輯運算子你可以反轉或組合布林表示式。

|-------------------------------+-------------------------------------------|
|             運算子             |                   描述                     |
|-------------------------------+-------------------------------------------|
| <code>!<em>表示式</em></code>  | 對錶達式結果取反（即將 true 變為 false，false 變為 true）
| `||`                          | 邏輯或
| `&&`                          | 邏輯與
{:.table .table-striped}

Here’s an example of using the logical operators:

下面是使用邏輯表示式的範例：

<?code-excerpt "misc/lib/language_tour/operators.dart (op-logical)"?>
```dart
if (!done && (col == 0 || col == 3)) {
  // ...Do something...
}
```


### Bitwise and shift operators

### 按位和移位運算子

You can manipulate the individual bits of numbers in Dart. Usually,
you’d use these bitwise and shift operators with integers.

在 Dart 中，二進位制位運算子可以操作二進位制的某一位，但僅適用於整數。

|------------------------------+-------------------------------------------|
| 運算子                        | 描述                                       |
|------------------------------+-------------------------------------------|
| `&`                          | 按位與
| `|`                          | 按位或
| `^`                          | 按位異或
| <code>~<em>表示式</em></code> | 按位取反（即將 “0” 變為 “1”，“1” 變為 “0”）
| `<<`                         | 位左移
| `>>`                         | 位右移
| `>>>`                        | 無符號右移
{:.table .table-striped}

Here’s an example of using bitwise and shift operators:

下面是使用按位和移位運算子的範例：

<?code-excerpt "misc/test/language_tour/operators_test.dart (op-bitwise)"?>
```dart
final value = 0x22;
final bitmask = 0x0f;

assert((value & bitmask) == 0x02); // AND
assert((value & ~bitmask) == 0x20); // AND NOT
assert((value | bitmask) == 0x2f); // OR
assert((value ^ bitmask) == 0x2d); // XOR
assert((value << 4) == 0x220); // Shift left
assert((value >> 4) == 0x02); // Shift right
assert((value >>> 4) == 0x02); // Unsigned shift right
assert((-value >> 4) == -0x03); // Shift right
assert((-value >>> 4) > 0); // Unsigned shift right
```

{{site.alert.version-note}}

  The `>>>` operator (known as _triple-shift_ or _unsigned shift_)
  requires a [language version][] of at least 2.14.

  `>>>` 運運算元在 2.14 以上的 [Dart 版本][language version] 中可用。

{{site.alert.end}}

### Conditional expressions

### 條件表示式

Dart has two operators that let you concisely evaluate expressions
that might otherwise require [if-else](#if-and-else) statements:

Dart 有兩個特殊的運算子可以用來替代 [if-else](#if-and-else) 陳述式：

<code><em>condition</em> ? <em>expr1</em> : <em>expr2</em></code>
<br> If _condition_ is true, evaluates _expr1_ (and returns its value);
  otherwise, evaluates and returns the value of _expr2_.

<code><em>條件</em> ? <em>表示式 1</em> : <em>表示式 2</em></code>
<br> 如果條件為 true，執行表示式 1並返回執行結果，否則執行表示式 2 並返回執行結果。

<code><em>expr1</em> ?? <em>expr2</em></code>
<br> If _expr1_ is non-null, returns its value;
  otherwise, evaluates and returns the value of _expr2_.

<code><em>表示式 1</em> ?? <em>表示式 2</em></code>
<br>如果表示式 1 為非 null 則返回其值，否則執行表示式 2 並返回其值。

When you need to assign a value
based on a boolean expression,
consider using `?` and `:`.

根據布林表示式確定賦值時，請考慮使用 `?` 和 `:`

<?code-excerpt "misc/lib/language_tour/operators.dart (if-then-else-operator)"?>
```dart
var visibility = isPublic ? 'public' : 'private';
```

If the boolean expression tests for null,
consider using `??`.

如果賦值是根據判定是否為 null 則考慮使用 `??`。

<?code-excerpt "misc/test/language_tour/operators_test.dart (if-null)"?>
```dart
String playerName(String? name) => name ?? 'Guest';
```

The previous example could have been written at least two other ways,
but not as succinctly:

上述範例還可以寫成至少下面兩種不同的形式，只是不夠簡潔：

<?code-excerpt "misc/test/language_tour/operators_test.dart (if-null-alt)"?>
```dart
// Slightly longer version uses ?: operator.
String playerName(String? name) => name != null ? name : 'Guest';

// Very long version uses if-else statement.
String playerName(String? name) {
  if (name != null) {
    return name;
  } else {
    return 'Guest';
  }
}
```

<a id="cascade"></a>
### Cascade notation

### 級聯運算子

Cascades (`..`, `?..`) allow you to make a sequence of operations
on the same object. In addition to accessing instance members,
you can also call instance methods on that same object.
This often saves you the step of creating a temporary variable and
allows you to write more fluid code.

級聯運算子 (`..`, `?..`) 可以讓你在同一個物件上連續呼叫多個物件的變數或方法。

Consider the following code:

比如下面的程式碼：

<?code-excerpt "misc/lib/language_tour/cascades.dart (cascade)"?>
```dart
var paint = Paint()
  ..color = Colors.black
  ..strokeCap = StrokeCap.round
  ..strokeWidth = 5.0;
```

The constructor, `Paint()`,
returns a `Paint` object.
The code that follows the cascade notation operates
on this object, ignoring any values that
might be returned.

The previous example is equivalent to this code:

<?code-excerpt "misc/lib/language_tour/cascades.dart (cascade-expanded)"?>
```dart
var paint = Paint();
paint.color = Colors.black;
paint.strokeCap = StrokeCap.round;
paint.strokeWidth = 5.0;
```

If the object that the cascade operates on can be null,
then use a _null-shorting_ cascade (`?..`) for the first operation.
Starting with `?..` guarantees that none of the cascade operations
are attempted on that null object.

<?code-excerpt "misc/test/language_tour/browser_test.dart (cascade-operator)"?>
```dart
querySelector('#confirm') // Get an object.
  ?..text = 'Confirm' // Use its members.
  ..classes.add('important')
  ..onClick.listen((e) => window.alert('Confirmed!'))
  ..scrollIntoView();
```

{{site.alert.version-note}}

  The `?..` syntax requires a [language version][] of at least 2.12.

  `?..` 執行在 2.12 和以上的 [版本][language version] 中可用。

{{site.alert.end}}

The previous code is equivalent to the following:

上面的程式碼相當於：

<?code-excerpt "misc/test/language_tour/browser_test.dart (cascade-operator-example-expanded)"?>
```dart
var button = querySelector('#confirm');
button?.text = 'Confirm';
button?.classes.add('important');
button?.onClick.listen((e) => window.alert('Confirmed!'));
button?.scrollIntoView();
```

You can also nest cascades. For example:

級聯運算子可以巢狀(Nesting)，例如：

<?code-excerpt "misc/lib/language_tour/operators.dart (nested-cascades)"?>
```dart
final addressBook = (AddressBookBuilder()
      ..name = 'jenny'
      ..email = 'jenny@example.com'
      ..phone = (PhoneNumberBuilder()
            ..number = '415-555-0100'
            ..label = 'home')
          .build())
    .build();
```

Be careful to construct your cascade on a function that returns
an actual object. For example, the following code fails:

在返回物件的函式中謹慎使用級聯運運算元。例如，下面的程式碼是錯誤的：

<?code-excerpt "misc/lib/language_tour/operators.dart (cannot-cascade-on-void)" plaster="none"?>
```dart
var sb = StringBuffer();
sb.write('foo')
  ..write('bar'); // Error: method 'write' isn't defined for 'void'.
```

The `sb.write()` call returns void,
and you can't construct a cascade on `void`.

上述程式碼中的 `sb.write()` 方法返回的是 void，
返回值為 `void` 的方法則不能使用級聯運算子。

{{site.alert.note}}

  Strictly speaking, the "double dot" notation for cascades isn't an operator.
  It's just part of the Dart syntax.

  嚴格來說 `..` 級聯操作並非一個運算子而是 Dart 的特殊語法。

{{site.alert.end}}

### Other operators

### 其他運算子

You've seen most of the remaining operators in other examples:

大多數其它的運算子，已經在其它的範例中使用過：

|----------+-----------------------+-------------------|
|   運算子  | 名字                  | 描述               |
|----------+-----------------------+-------------------|
| `()`     | 使用方法               | 代表呼叫一個方法
| `[]`     | 存取 List             | 存取 List 中特定位置的元素
| `?[]`    | 判空存取 List          | 左側呼叫者不為空時，存取 List 中特定位置的元素
| `.`      | 存取成員               | 成員存取符
| `?.`     | 條件存取成員            | 與上述成員存取符類似，但是左邊的操作物件不能為 null，例如 foo?.bar，如果 foo 為 null 則返回 null ，否則返回 bar
| `!`      | 空斷言運運算元            | 將表示式的型別轉換為其基礎型別，如果轉換失敗會丟擲執行時例外。例如 `foo!.bar`，如果 `foo` 為 null，則丟擲執行時例外
{:.table .table-striped}

For more information about the `.`, `?.`, and `..` operators, see
[Classes](#classes).

更多關於 `.`, `?.` 和  `..` 運算子介紹，請參考[類](#classes).


## Control flow statements

## 流程控制陳述式

You can control the flow of your Dart code using any of the following:

你可以使用下面的陳述式來控制 Dart 程式碼的執行流程：

-   `if` and `else`

    `if` 和 `else`

-   `for` loops

    `for` 迴圈

-   `while` and `do`-`while` loops

    `while` 和 `do`-`while` 迴圈

-   `break` and `continue`

    `break` 和 `continue`

-   `switch` and `case`

    `switch` 和 `case`

-   `assert`

You can also affect the control flow using `try-catch` and `throw`, as
explained in [Exceptions](#exceptions).

使用 `try-catch` 和 `throw` 也能影響控制流，
詳情參考[例外](#exceptions)部分。

### If and else

### If 和 Else

Dart supports `if` statements with optional `else` statements, as the
next sample shows. Also see [conditional expressions](#conditional-expressions).

Dart 支援 `if - else` 陳述式，其中 `else` 是可選的，
比如下面的例子。你也可以參考[條件表示式](#conditional-expressions)。

<?code-excerpt "misc/lib/language_tour/control_flow.dart (if-else)"?>
```dart
if (isRaining()) {
  you.bringRainCoat();
} else if (isSnowing()) {
  you.wearJacket();
} else {
  car.putTopDown();
}
```

The statement conditions must be expressions
that evaluate to boolean values, nothing else.
See [Booleans](#booleans) for more information.

Dart 的 if 陳述式中的條件必須是布林值而不能為其它型別。
詳情請查閱 [布林值](#booleans)。


### For loops

### For 迴圈

You can iterate with the standard `for` loop. For example:

你可以使用標準的 `for` 迴圈進行迭代。例如：

<?code-excerpt "misc/test/language_tour/control_flow_test.dart (for)"?>
```dart
var message = StringBuffer('Dart is fun');
for (var i = 0; i < 5; i++) {
  message.write('!');
}
```

Closures inside of Dart’s `for` loops capture the _value_ of the index,
avoiding a common pitfall found in JavaScript. For example, consider:

在 Dart 語言中，`for` 迴圈中的閉包會自動捕獲迴圈的
**索引值** 以避免 JavaScript 中一些常見的陷阱。假設有如下程式碼：

<?code-excerpt "misc/test/language_tour/control_flow_test.dart (for-and-closures)"?>
```dart
var callbacks = [];
for (var i = 0; i < 2; i++) {
  callbacks.add(() => print(i));
}

for (final c in callbacks) {
  c();
}
```

The output is `0` and then `1`, as expected. In contrast, the example
would print `2` and then `2` in JavaScript.

上述程式碼執行後會輸出 `0` 和 `1`，但是如果在 JavaScript 中執行同樣的程式碼則會輸出兩個 `2`。

If the object that you are iterating over is an Iterable (such as List or Set)
and if you don't need to know the current iteration counter, 
you can use the `for-in` form of [iteration][]:

如果要遍歷的物件是一個可迭代物件（例如 List 或 Set），並且你不需要知道當前的遍歷索引，
則可以使用 `for-in` 方法進行 [遍歷](/guides/libraries/library-tour#iteration)：

<?code-excerpt "misc/lib/language_tour/control_flow.dart (collection)"?>
```dart
for (final candidate in candidates) {
  candidate.interview();
}
```

{{site.alert.tip}}

  To practice using `for-in`, follow the
  [Iterable collections codelab](/codelabs/iterables).
  
  若想練習使用 `for-in`，請參考 [遍歷集合 codelab](/codelabs/iterables)。
  
{{site.alert.end}}

Iterable classes also have a [forEach()][] method as another option:

可迭代物件同時可以使用 [forEach()][] 方法作為另一種選擇：

<?code-excerpt "misc/test/language_tour/control_flow_test.dart (forEach)"?>
```dart
var collection = [1, 2, 3];
collection.forEach(print); // 1 2 3
```


### While and do-while

### While 和 Do-While

A `while` loop evaluates the condition before the loop:

`while` 迴圈會在執行迴圈體前先判斷條件：

<?code-excerpt "misc/lib/language_tour/control_flow.dart (while)"?>
```dart
while (!isDone()) {
  doSomething();
}
```

A `do`-`while` loop evaluates the condition *after* the loop:

`do-while` 迴圈則會 **先執行一遍迴圈體** 再判斷條件：

<?code-excerpt "misc/lib/language_tour/control_flow.dart (do-while)"?>
```dart
do {
  printLine();
} while (!atEndOfPage());
```


### Break and continue

### Break 和 Continue

Use `break` to stop looping:

使用 `break` 可以中斷迴圈：

<?code-excerpt "misc/lib/language_tour/control_flow.dart (while-break)"?>
```dart
while (true) {
  if (shutDownRequested()) break;
  processIncomingRequests();
}
```

Use `continue` to skip to the next loop iteration:

使用 `continue` 可以跳過本次迴圈直接進入下一次迴圈：

<?code-excerpt "misc/lib/language_tour/control_flow.dart (for-continue)"?>
```dart
for (int i = 0; i < candidates.length; i++) {
  var candidate = candidates[i];
  if (candidate.yearsExperience < 5) {
    continue;
  }
  candidate.interview();
}
```

You might write that example differently if you’re using an
[`Iterable`][] such as a list or set:

如果你正在使用諸如 List 或 Set 之類別的 [`Iterable`][] 物件，你可以用以下方式重寫上述例子:

<?code-excerpt "misc/lib/language_tour/control_flow.dart (where)"?>
```dart
candidates
    .where((c) => c.yearsExperience >= 5)
    .forEach((c) => c.interview());
```


### Switch and case

### Switch 和 Case

Switch statements in Dart compare integer, string, or compile-time
constants using `==`. The compared objects must all be instances of the
same class (and not of any of its subtypes), and the class must not
override `==`.
[Enumerated types](#enumerated-types) work well in `switch` statements.

Switch 陳述式在 Dart 中使用 `==` 來比較整數、字串或編譯時常量，
比較的兩個物件必須是同一個型別且不能是子類別並且沒有重寫 `==` 運運算元。
[列舉型別](#enumerated-types)非常適合在 `Switch` 陳述式中使用。

{{site.alert.note}}

  Switch statements in Dart are intended for limited circumstances,
  such as in interpreters or scanners.

  Dart 中的 Switch 陳述式僅適用於有限的情況，比如使用直譯器和掃描器的場景。

{{site.alert.end}}

Each non-empty `case` clause ends with a `break` statement, as a rule.
Other valid ways to end a non-empty `case` clause are a `continue`,
`throw`, or `return` statement.

每一個非空的 `case` 子句都必須有一個 `break` 陳述式，
也可以透過 `continue`、`throw` 或者 `return` 來結束非空 `case` 陳述式。

Use a `default` clause to execute code when no `case` clause matches:

不匹配任何 `case` 陳述式的情況下，會執行 `default` 子句中的程式碼：

<?code-excerpt "misc/lib/language_tour/control_flow.dart (switch)"?>
```dart
var command = 'OPEN';
switch (command) {
  case 'CLOSED':
    executeClosed();
    break;
  case 'PENDING':
    executePending();
    break;
  case 'APPROVED':
    executeApproved();
    break;
  case 'DENIED':
    executeDenied();
    break;
  case 'OPEN':
    executeOpen();
    break;
  default:
    executeUnknown();
}
```

The following example omits the `break` statement in a `case` clause,
thus generating an error:

下面的例子忽略了 `case` 子句的 `break` 陳述式，因此會產生錯誤：

<?code-excerpt "misc/lib/language_tour/control_flow.dart (switch-break-omitted)" plaster="none"?>
```dart
var command = 'OPEN';
switch (command) {
  case 'OPEN':
    executeOpen();
    // ERROR: Missing break

  case 'CLOSED':
    executeClosed();
    break;
}
```

However, Dart does support empty `case` clauses, allowing a form of
fall-through:

但是，Dart 支援空的 `case` 陳述式，允許其以 fall-through 的形式執行。

<?code-excerpt "misc/lib/language_tour/control_flow.dart (switch-empty-case)"?>
```dart
var command = 'CLOSED';
switch (command) {
  case 'CLOSED': // Empty case falls through.
  case 'NOW_CLOSED':
    // Runs for both CLOSED and NOW_CLOSED.
    executeNowClosed();
    break;
}
```

If you really want fall-through, you can use a `continue` statement and
a label:

在非空 `case` 陳述式中想要實現 fall-through 的形式，
可以使用 `continue` 陳述式配合 label 的方式實現:

<?code-excerpt "misc/lib/language_tour/control_flow.dart (switch-continue)"?>
```dart
var command = 'CLOSED';
switch (command) {
  case 'CLOSED':
    executeClosed();
    continue nowClosed;
  // Continues executing at the nowClosed label.

  nowClosed:
  case 'NOW_CLOSED':
    // Runs for both CLOSED and NOW_CLOSED.
    executeNowClosed();
    break;
}
```

A `case` clause can have local variables, which are visible only inside
the scope of that clause.

每個 `case` 子句都可以有區域變數且僅在該 case 陳述式內可見。


### Assert

### 斷言

During development, use an assert 
statement—<code>assert(<em>condition</em>, <em>optionalMessage</em>)</code>;—to
disrupt normal execution if a boolean condition is false. 
You can find examples of assert statements throughout this tour. 
Here are some more:

在開發過程中，可以在條件表示式為 false 時
使用 — <code>assert(<em>條件</em>, <em>可選資訊</em>)</code>; — 陳述式
來打斷程式碼的執行。你可以在本文中找到大量使用 assert 的例子。
下面是相關範例：

<?code-excerpt "misc/test/language_tour/control_flow_test.dart (assert)"?>
```dart
// Make sure the variable has a non-null value.
assert(text != null);

// Make sure the value is less than 100.
assert(number < 100);

// Make sure this is an https URL.
assert(urlString.startsWith('https'));
```

To attach a message to an assertion,
add a string as the second argument to `assert`
(optionally with a [trailing comma][trailing commas]):

`assert` 的第二個引數可以為其新增一個字串訊息。

<?code-excerpt "misc/test/language_tour/control_flow_test.dart (assert-with-message)"?>
```dart
assert(urlString.startsWith('https'),
    'URL ($urlString) should start with "https".');
```

The first argument to `assert` can be any expression that
resolves to a boolean value. If the expression’s value
is true, the assertion succeeds and execution
continues. If it's false, the assertion fails and an exception (an
[`AssertionError`][]) is thrown.

`assert` 的第一個引數可以是值為布林值的任何表示式。
如果表示式的值為 true，則斷言成功，繼續執行。
如果表示式的值為 false，則斷言失敗，丟擲一個 [`AssertionError`][] 例外。

When exactly do assertions work?
That depends on the tools and framework you're using:

如何判斷斷言是否生效？
斷言是否生效依賴開發工具和使用的框架：

* Flutter enables assertions in [debug mode.][Flutter debug mode]

  Flutter 在 [除錯模式][Flutter debug mode] 時生效。

* Development-only tools such as [`webdev serve`][]
  typically enable assertions by default.

  一些開發工具比如 [`webdev serve`][] 通常情況下是預設生效的。

* Some tools, such as [`dart run`][] and [`dart compile js`][]
  support assertions through a command-line flag: `--enable-asserts`.

  其他一些工具，比如 [`dart run`][] 以及 [`dart compile js`][]
  透過在執行 Dart 程式時新增命令列引數 `--enable-asserts` 使 assert 生效。

In production code, assertions are ignored, and
the arguments to `assert` aren't evaluated.

在生產環境程式碼中，斷言會被忽略，
與此同時傳入 `assert` 的引數不被判斷。

[webdev serve]: /tools/webdev#serve
[dart compile js]: /tools/dart-compile#js

## Exceptions

## 例外

Your Dart code can throw and catch exceptions. Exceptions are errors
indicating that something unexpected happened. If the exception isn’t
caught, the [isolate](#isolates) that raised the exception is suspended,
and typically the isolate and its program are terminated.

Dart 程式碼可以丟擲和捕獲例外。例外表示一些未知的錯誤情況，
如果例外沒有捕獲則會被丟擲從而導致丟擲例外的程式碼終止執行。

In contrast to Java, all of Dart’s exceptions are unchecked exceptions.
Methods don't declare which exceptions they might throw, and you aren't
required to catch any exceptions.

與 Java 不同的是，Dart 的所有例外都是非必檢例外，
方法不必宣告會丟擲哪些例外，並且你也不必捕獲任何例外。

Dart provides [`Exception`][] and [`Error`][]
types, as well as numerous predefined subtypes. You can, of course,
define your own exceptions. However, Dart programs can throw any
non-null object—not just Exception and Error objects—as an exception.

Dart 提供了 [`Exception`][] 和 [`Error`][] 兩種型別的例外以及它們一系列的子類別，
你也可以定義自己的例外型別。
但是在 Dart 中可以將任何非 null 物件作為例外丟擲
而不侷限於 Exception 或 Error 型別。

### Throw

### 丟擲例外

Here’s an example of throwing, or *raising*, an exception:

下面是關於丟擲或者 **引發** 例外的範例：

<?code-excerpt "misc/lib/language_tour/exceptions.dart (throw-FormatException)"?>
```dart
throw FormatException('Expected at least 1 section');
```

You can also throw arbitrary objects:

你也可以丟擲任意的物件：

<?code-excerpt "misc/lib/language_tour/exceptions.dart (out-of-llamas)"?>
```dart
throw 'Out of llamas!';
```

{{site.alert.note}}

  Production-quality code usually throws types that implement [`Error`][] or
  [`Exception`][].

  優秀的程式碼通常會丟擲 [`Error`][] 或 [`Exception`][] 型別的例外。

{{site.alert.end}}

Because throwing an exception is an expression, you can throw exceptions
in =\> statements, as well as anywhere else that allows expressions:

因為丟擲例外是一個表示式，所以可以在 =\> 陳述式中使用，
也可以在其他使用表示式的地方丟擲例外：

<?code-excerpt "misc/lib/language_tour/exceptions.dart (throw-is-an-expression)"?>
```dart
void distanceTo(Point other) => throw UnimplementedError();
```


### Catch

### 捕獲例外

Catching, or capturing, an exception stops the exception from
propagating (unless you rethrow the exception).
Catching an exception gives you a chance to handle it:

捕獲例外可以避免例外繼續傳遞（重新丟擲例外除外）。
捕獲一個例外可以給你處理它的機會：

<?code-excerpt "misc/lib/language_tour/exceptions.dart (try)"?>
```dart
try {
  breedMoreLlamas();
} on OutOfLlamasException {
  buyMoreLlamas();
}
```

To handle code that can throw more than one type of exception, you can
specify multiple catch clauses. The first catch clause that matches the
thrown object’s type handles the exception. If the catch clause does not
specify a type, that clause can handle any type of thrown object:

對於可以丟擲多種例外型別的程式碼，也可以指定多個 catch 陳述式，
每個陳述式分別對應一個例外型別，
如果 catch 陳述式沒有指定例外型別則表示可以捕獲任意例外型別：

<?code-excerpt "misc/lib/language_tour/exceptions.dart (try-catch)"?>
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

As the preceding code shows, you can use either `on` or `catch` or both.
Use `on` when you need to specify the exception type. Use `catch` when
your exception handler needs the exception object.

如上述程式碼所示可以使用 `on` 或 `catch` 來捕獲例外，
使用 `on` 來指定例外型別，使用 `catch` 來捕獲例外物件，
兩者可同時使用。

You can specify one or two parameters to `catch()`.
The first is the exception that was thrown,
and the second is the stack trace (a [`StackTrace`][] object).

你可以為 `catch` 方法指定兩個引數，
第一個引數為丟擲的例外物件，
第二個引數為棧資訊 [`StackTrace`][] 物件：

<?code-excerpt "misc/lib/language_tour/exceptions.dart (try-catch-2)" replace="/\(e.*?\)/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
try {
  // ···
} on Exception catch [!(e)!] {
  print('Exception details:\n $e');
} catch [!(e, s)!] {
  print('Exception details:\n $e');
  print('Stack trace:\n $s');
}
{% endprettify %}

To partially handle an exception,
while allowing it to propagate,
use the `rethrow` keyword.

關鍵字 `rethrow` 可以將捕獲的例外再次丟擲：

<?code-excerpt "misc/test/language_tour/exceptions_test.dart (rethrow)" replace="/rethrow;/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
void misbehave() {
  try {
    dynamic foo = true;
    print(foo++); // Runtime error
  } catch (e) {
    print('misbehave() partially handled ${e.runtimeType}.');
    [!rethrow;!] // Allow callers to see the exception.
  }
}

void main() {
  try {
    misbehave();
  } catch (e) {
    print('main() finished handling ${e.runtimeType}.');
  }
}
{% endprettify %}


### Finally

To ensure that some code runs whether or not an exception is thrown, use
a `finally` clause. If no `catch` clause matches the exception, the
exception is propagated after the `finally` clause runs:

無論是否丟擲例外，`finally` 陳述式始終執行，
如果沒有指定 `catch` 陳述式來捕獲例外，則例外會在執行完 `finally` 陳述式後丟擲：

<?code-excerpt "misc/lib/language_tour/exceptions.dart (finally)"?>
```dart
try {
  breedMoreLlamas();
} finally {
  // Always clean up, even if an exception is thrown.
  cleanLlamaStalls();
}
```

The `finally` clause runs after any matching `catch` clauses:

`finally` 陳述式會在任何匹配的 `catch` 陳述式後執行：

<?code-excerpt "misc/lib/language_tour/exceptions.dart (try-catch-finally)"?>
```dart
try {
  breedMoreLlamas();
} catch (e) {
  print('Error: $e'); // Handle the exception first.
} finally {
  cleanLlamaStalls(); // Then clean up.
}
```

Learn more by reading the
[Exceptions](/guides/libraries/library-tour#exceptions)
section of the library tour.

你可以閱讀 Dart 核心函式庫概覽的
[例外](/guides/libraries/library-tour#exceptions) 章節獲取更多相關資訊。

## Classes

## 類

Dart is an object-oriented language with classes and mixin-based
inheritance. Every object is an instance of a class, and all classes
except `Null` descend from [`Object`][].
*Mixin-based inheritance* means that although every class
(except for the [top class][top-and-bottom], `Object?`)
has exactly one superclass, a class body can be reused in
multiple class hierarchies.
[Extension methods](#extension-methods) are a way to
add functionality to a class without changing the class or creating a subclass.

Dart 是支援基於 mixin 繼承機制的面嚮物件語言，所有物件都是一個類別的例項，
而除了 `Null` 以外的所有的類都繼承自 [`Object`][] 類別。
**基於 mixin 的繼承** 意味著儘管每個類（[top class][top-and-bottom] `Object?` 除外）
都只有一個超類，一個類別的程式碼可以在其它多個類繼承中重複使用。
[擴充方法](#extension-methods) 是一種在不更改類
或建立子類別的情況下向類新增功能的方式。

### Using class members

### 使用類別的成員

Objects have *members* consisting of functions and data (*methods* and
*instance variables*, respectively). When you call a method, you *invoke*
it on an object: the method has access to that object’s functions and
data.

物件的 **成員** 由函式和資料（即 **方法** 和 **例項變數**）組成。
方法的 **呼叫** 要透過物件來完成，
這種方式可以存取物件的函式和資料。

Use a dot (`.`) to refer to an instance variable or method:

使用（`.`）來存取物件的例項變數或方法：

<?code-excerpt "misc/test/language_tour/classes_test.dart (object-members)"?>
```dart
var p = Point(2, 2);

// Get the value of y.
assert(p.y == 2);

// Invoke distanceTo() on p.
double distance = p.distanceTo(Point(4, 4));
```

Use `?.` instead of `.` to avoid an exception
when the leftmost operand is null:

使用 `?.` 代替 `.` 可以避免因為左邊表示式為 null 而導致的問題：

<?code-excerpt "misc/test/language_tour/classes_test.dart (safe-member-access)"?>
```dart
// If p is non-null, set a variable equal to its y value.
var a = p?.y;
```


### Using constructors

### 使用建構函式

You can create an object using a *constructor*.
Constructor names can be either <code><em>ClassName</em></code> or
<code><em>ClassName</em>.<em>identifier</em></code>. For example,
the following code creates `Point` objects using the
`Point()` and `Point.fromJson()` constructors:

可以使用 **建構函式** 來建立一個物件。
建構函式的命名方式可以為
<code><em>類別名稱</em></code> 或 <code><em> 類別名稱
</em>.<em> 識別符號 </em></code> 的形式。
例如下述程式碼分別使用 `Point()` 和 `Point.fromJson()`
兩種構造器建立了 `Point` 物件：

<?code-excerpt "misc/test/language_tour/classes_test.dart (object-creation)" replace="/ as .*?;/;/g"?>
```dart
var p1 = Point(2, 2);
var p2 = Point.fromJson({'x': 1, 'y': 2});
```

The following code has the same effect, but
uses the optional `new` keyword before the constructor name:

以下程式碼具有相同的效果，
但是建構函式名前面的的 `new` 關鍵字是可選的：

<?code-excerpt "misc/test/language_tour/classes_test.dart (object-creation-new)" replace="/ as .*?;/;/g"?>
```dart
var p1 = new Point(2, 2);
var p2 = new Point.fromJson({'x': 1, 'y': 2});
```

Some classes provide [constant constructors](#constant-constructors).
To create a compile-time constant using a constant constructor,
put the `const` keyword before the constructor name:

一些類提供了[常量建構函式](#constant-constructors)。使用常量建構函式，
在建構函式名之前加 `const` 關鍵字，來建立編譯時常量時：

<?code-excerpt "misc/test/language_tour/classes_test.dart (const)"?>
```dart
var p = const ImmutablePoint(2, 2);
```

Constructing two identical compile-time constants results in a single,
canonical instance:

兩個使用相同建構函式相同引數值構造的編譯時常量是同一個物件：

<?code-excerpt "misc/test/language_tour/classes_test.dart (identical)"?>
```dart
var a = const ImmutablePoint(1, 1);
var b = const ImmutablePoint(1, 1);

assert(identical(a, b)); // They are the same instance!
```

Within a _constant context_, you can omit the `const` before a constructor
or literal. For example, look at this code, which creates a const map:

在 **常量上下文** 場景中，
你可以省略掉建構函式或字面量前的 `const` 關鍵字。
例如下面的例子中我們建立了一個常量 Map：

<?code-excerpt "misc/test/language_tour/classes_test.dart (const-context-withconst)" replace="/pointAndLine1/pointAndLine/g"?>
```dart
// Lots of const keywords here.
const pointAndLine = const {
  'point': const [const ImmutablePoint(0, 0)],
  'line': const [const ImmutablePoint(1, 10), const ImmutablePoint(-2, 11)],
};
```

You can omit all but the first use of the `const` keyword:

根據上下文，你可以只保留第一個 `const` 關鍵字，
其餘的全部省略：

<?code-excerpt "misc/test/language_tour/classes_test.dart (const-context-noconst)" replace="/pointAndLine2/pointAndLine/g"?>
```dart
// Only one const, which establishes the constant context.
const pointAndLine = {
  'point': [ImmutablePoint(0, 0)],
  'line': [ImmutablePoint(1, 10), ImmutablePoint(-2, 11)],
};
```

If a constant constructor is outside of a constant context
and is invoked without `const`,
it creates a **non-constant object**:

但是如果無法根據上下文判斷是否可以省略 `const`，
則不能省略掉 `const` 關鍵字，否則將會建立一個 **非常量物件** 例如：

<?code-excerpt "misc/test/language_tour/classes_test.dart (nonconst-const-constructor)"?>
```dart
var a = const ImmutablePoint(1, 1); // Creates a constant
var b = ImmutablePoint(1, 1); // Does NOT create a constant

assert(!identical(a, b)); // NOT the same instance!
```


### Getting an object's type

### 獲取物件的型別

To get an object's type at runtime,
you can use the `Object` property `runtimeType`,
which returns a [`Type`][] object.

可以使用 `Object` 物件的 `runtimeType` 屬性在執行時獲取一個物件的型別，
該物件型別是 [`Type`][] 的例項。

<?code-excerpt "misc/test/language_tour/classes_test.dart (runtimeType)"?>
```dart
print('The type of a is ${a.runtimeType}');
```

{{site.alert.warn}}
  Use a [type test operator][] rather than `runtimeType`
  to test an object's type.
  In production environments, the test `object is Type` is more stable
  than the test `object.runtimeType == Type`.
{{site.alert.end}}

Up to here, you've seen how to _use_ classes.
The rest of this section shows how to _implement_ classes.

到目前為止，我們已經瞭解瞭如何 **使用** 類別。
本節的其餘部分將向你介紹如何 **實現** 一個類別。

### Instance variables

### 例項變數

Here’s how you declare instance variables:

下面是宣告例項變數的範例：

<?code-excerpt "misc/lib/language_tour/classes/point_with_main.dart (class)"?>
```dart
class Point {
  double? x; // Declare instance variable x, initially null.
  double? y; // Declare y, initially null.
  double z = 0; // Declare z, initially 0.
}
```

All uninitialized instance variables have the value `null`.

所有未初始化的例項變數其值均為 `null`。

All instance variables generate an implicit *getter* method.
Non-final instance variables and
`late final` instance variables without initializers also generate
an implicit *setter* method. For details,
see [Getters and setters](#getters-and-setters).

所有例項變數均會隱含地宣告一個 *Getter* 方法。
非終值的例項變數和 `late final` 宣告但未宣告初始化的例項變數還會隱含地宣告一個 *Setter* 方法。
你可以查閱 [Getter 和 Setter](#getters-and-setters) 獲取更多相關資訊。

<?code-excerpt "misc/lib/language_tour/classes/point_with_main.dart (class+main)" replace="/(double .*?;).*/$1/g" plaster="none"?>
```dart
class Point {
  double? x; // Declare instance variable x, initially null.
  double? y; // Declare y, initially null.
}

void main() {
  var point = Point();
  point.x = 4; // Use the setter method for x.
  assert(point.x == 4); // Use the getter method for x.
  assert(point.y == null); // Values default to null.
}
```

Instance variables can be `final`,
in which case they must be set exactly once.
Initialize `final`, non-`late` instance variables
at declaration,
using a constructor parameter, or
using a constructor's [initializer list](#initializer-list):

<?code-excerpt "misc/lib/effective_dart/usage_good.dart (field-init-at-decl)"?>
```dart
class ProfileMark {
  final String name;
  final DateTime start = DateTime.now();

  ProfileMark(this.name);
  ProfileMark.unnamed() : name = '';
}
```

If you need to assign the value of a `final` instance variable
after the constructor body starts, you can use one of the following:

* Use a [factory constructor](#factory-constructors).
* Use `late final`, but [_be careful:_][late-final-ivar]
  a `late final` without an initializer adds a setter to the API.

### Constructors

### 建構函式

Declare a constructor by creating a function with the same name as its
class (plus, optionally, an additional identifier as described in
[Named constructors](#named-constructors)).
The most common form of constructor, the generative constructor, creates
a new instance of a class:

宣告一個與類別名稱一樣的函式即可宣告一個建構函式
（對於[命名式建構函式](#named-constructors) 還可以新增額外的識別符號）。
大部分的建構函式形式是產生式建構函式，其用於建立一個類別的例項：

<?code-excerpt "misc/lib/language_tour/classes/point_alt.dart (constructor-long-way)" plaster="none"?>
```dart
class Point {
  double x = 0;
  double y = 0;

  Point(double x, double y) {
    // See initializing formal parameters for a better way
    // to initialize instance variables.
    this.x = x;
    this.y = y;
  }
}
```

The `this` keyword refers to the current instance.

使用 `this` 關鍵字參考當前例項。

{{site.alert.note}}

  Use `this` only when there is a name conflict. 
  Otherwise, Dart style omits the `this`.

  當且僅當命名衝突時使用 `this` 關鍵字才有意義，否則 Dart 會忽略 `this` 關鍵字。

{{site.alert.end}}


#### Initializing formal parameters

#### 終值初始化

The pattern of assigning a constructor argument to an instance variable
is so common, 
Dart has initializing formal parameters to make it easy.

對於大多數程式語言來說在建構函式中為例項變數賦值的過程都是類似的，
而 Dart 則提供了一種特殊的語法糖來簡化該步驟。

Initializing parameters can also be used to initialize
non-nullable or `final` instance variables,
which both must be initialized or provided a default value.

構造中初始化的引數可以用於初始化非空或 `final` 修飾的變數，
它們都必須被初始化或提供一個預設值。

<?code-excerpt "misc/lib/language_tour/classes/point.dart (constructor-initializer)" plaster="none"?>
```dart
class Point {
  final double x;
  final double y;

  // Sets the x and y instance variables
  // before the constructor body runs.
  Point(this.x, this.y);
}
```

The variables introduced by the initializing formals
are implicitly final and only in scope of the initializer list.

在初始化時出現的變數預設是隱含終值，且只在初始化時可用。

#### Default constructors

#### 預設建構函式

If you don’t declare a constructor, a default constructor is provided
for you. The default constructor has no arguments and invokes the
no-argument constructor in the superclass.

如果你沒有宣告建構函式，那麼 Dart 會自動產生一個無引數的建構函式並且該建構函式會呼叫其父類別的無引數構造方法。

#### Constructors aren’t inherited

#### 建構函式不被繼承

Subclasses don’t inherit constructors from their superclass. A subclass
that declares no constructors has only the default (no argument, no
name) constructor.

子類別不會繼承父類別的建構函式，如果子類別沒有宣告建構函式，那麼只會有一個預設無引數的建構函式。

#### Named constructors

#### 命名式建構函式

Use a named constructor to implement multiple constructors for a class
or to provide extra clarity:

可以為一個類別宣告多個命名式建構函式來表達更明確的意圖：

<?code-excerpt "misc/lib/language_tour/classes/point.dart (named-constructor)" replace="/Point\.\S*/[!$&!]/g" plaster="none"?>
{% prettify dart tag=pre+code %}
const double xOrigin = 0;
const double yOrigin = 0;

class Point {
  final double x;
  final double y;

  Point(this.x, this.y);

  // Named constructor
  [!Point.origin()!]
      : x = xOrigin,
        y = yOrigin;
}
{% endprettify %}

Remember that constructors are not inherited, which means that a
superclass’s named constructor is not inherited by a subclass. If you
want a subclass to be created with a named constructor defined in the
superclass, you must implement that constructor in the subclass.

記住建構函式是不能被繼承的，這將意味著子類別不能繼承父類別的命名式建構函式，
如果你想在子類別中提供一個與父類命名建構函式名字一樣的命名建構函式，
則需要在子類別中明確地宣告。

#### Invoking a non-default superclass constructor

#### 呼叫父類非預設建構函式

By default, a constructor in a subclass calls the superclass’s unnamed,
no-argument constructor.
The superclass's constructor is called at the beginning of the
constructor body. If an [initializer list](#initializer-list)
is also being used, it executes before the superclass is called.
In summary, the order of execution is as follows:

預設情況下，子類別的建構函式會呼叫父類別的匿名無引數構造方法，
並且該呼叫會在子類別建構函式的函式體程式碼執行前，
如果子類別建構函式還有一個 [初始化列表](#initializer-list)，
那麼該初始化列表會在呼叫父類別的該建構函式之前被執行，
總的來說，這三者的呼叫順序如下：

1. initializer list

   初始化列表

1. superclass's no-arg constructor

   父類別的無引數建構函式

1. main class's no-arg constructor

   當前類別的建構函式

If the superclass doesn’t have an unnamed, no-argument constructor,
then you must manually call one of the constructors in the
superclass. Specify the superclass constructor after a colon (`:`), just
before the constructor body (if any).

如果父類沒有匿名無引數建構函式，那麼子類別必須呼叫父類別的其中一個建構函式，
為子類別的建構函式指定一個父類別的建構函式只需在建構函式體前使用（`:`）指定。

In the following example, the constructor for the Employee class calls the named
constructor for its superclass, Person. Click **Run** to execute the code.

下面的範例中，Employee 類別的建構函式呼叫了父類 Person 的命名建構函式。
點選執行按鈕執行範例程式碼。

<?code-excerpt "misc/lib/language_tour/classes/employee.dart (super)" plaster="none"?>
```dart:run-dartpad:height-450px:ga_id-non_default_superclass_constructor
class Person {
  String? firstName;

  Person.fromJson(Map data) {
    print('in Person');
  }
}

class Employee extends Person {
  // Person does not have a default constructor;
  // you must call super.fromJson().
  Employee.fromJson(super.data) : super.fromJson() {
    print('in Employee');
  }
}

void main() {
  var employee = Employee.fromJson({});
  print(employee);
  // Prints:
  // in Person
  // in Employee
  // Instance of 'Employee'
}
```

Because the arguments to the superclass constructor are evaluated before
invoking the constructor, an argument can be an expression such as a
function call:

因為引數會在子類別建構函式被執行前傳遞給父類別的建構函式，
因此該引數也可以是一個表示式，比如一個函式：

<?code-excerpt "misc/lib/language_tour/classes/employee.dart (method-then-constructor)"?>
```dart
class Employee extends Person {
  Employee() : super.fromJson(fetchDefaultData());
  // ···
}
```

{{site.alert.warning}}

  Arguments to the superclass constructor don't have access to `this`. For
  example, arguments can call static methods but not instance methods.

  傳遞給父類建構函式的引數不能使用 `this` 關鍵字，
  因為在引數傳遞的這一步驟，子類別建構函式尚未執行，
  子類別的例項物件也就還未初始化，
  因此所有的例項成員都不能被存取，但是類成員可以。

{{site.alert.end}}

#### Super parameters

#### 超類引數

To avoid having to manually pass each parameter
into the super invocation of a constructor,
you can use super-initializer parameters to forward parameters
to the specified or default superclass constructor.
This feature can't be used with redirecting constructors.
Super-initializer parameters have similar syntax and semantics to
[initializing formal parameters](#initializing-formal-parameters):

為了不重複地將引數傳遞到超類構造的指定引數，
你可以使用超類引數，直接在子類別的構造中使用超類構造的某個引數。
超類引數不能和重新導向的引數一起使用。
超類引數的表示式和寫法與
[終值初始化](#initializing-formal-parameters) 類似：

<?code-excerpt "misc/lib/language_tour/classes/super_initializer_parameters.dart (positional)" plaster="none"?>
```dart
class Vector2d {
  final double x;
  final double y;

  Vector2d(this.x, this.y);
}

class Vector3d extends Vector2d {
  final double z;

  // Forward the x and y parameters to the default super constructor like:
  // Vector3d(final double x, final double y, this.z) : super(x, y);
  Vector3d(super.x, super.y, this.z);
}
```

Super-initializer parameters cannot be positional 
if the super-constructor invocation already has positional arguments,
but they can always be named:

如果超類構造的位置引數已被使用，
那麼超類構造引數就不能再繼續使用被佔用的位置。
但是超類構造引數可以始終是命名引數：

<?code-excerpt "misc/lib/language_tour/classes/super_initializer_parameters.dart (named)" plaster="none"?>
```dart
class Vector2d {
  // ...

  Vector2d.named({required this.x, required this.y});
}

class Vector3d extends Vector2d {
  // ...

  // Forward the y parameter to the named super constructor like:
  // Vector3d.yzPlane({required double y, required this.z})
  //       : super.named(x: 0, y: y);
  Vector3d.yzPlane({required super.y, required this.z}) : super.named(x: 0);
}
```

{{site.alert.version-note}}

  Using super-initializer parameters 
  requires a [language version][] of at least 2.17.
  If you're using an earlier language version,
  you must manually pass in all super constructor parameters.

  使用超類引數需要 [Dart SDK 版本][language version] 至少為 2.17。
  在先前的版本中，你必須手動傳遞所有的超類構造引數。

{{site.alert.end}}

#### Initializer list

#### 初始化列表

Besides invoking a superclass constructor, you can also initialize
instance variables before the constructor body runs. Separate
initializers with commas.

除了呼叫父類建構函式之外，還可以在建構函式體執行之前初始化例項變數。
每個例項變數之間使用逗號分隔。

{% comment %}
[TODO #2950: Maybe change example or point to discussion of ! (in map section?).]
{% endcomment %}

<?code-excerpt "misc/lib/language_tour/classes/point_alt.dart (initializer-list)"?>
```dart
// Initializer list sets instance variables before
// the constructor body runs.
Point.fromJson(Map<String, double> json)
    : x = json['x']!,
      y = json['y']! {
  print('In Point.fromJson(): ($x, $y)');
}
```

{{site.alert.warning}}

  The right-hand side of an initializer doesn't have access to `this`.

  初始化列表表示式 = 右邊的陳述式不能使用 `this` 關鍵字。

{{site.alert.end}}

During development, you can validate inputs by using `assert` in the
initializer list.

在開發模式下，你可以在初始化列表中使用 `assert` 來驗證輸入資料：

<?code-excerpt "misc/lib/language_tour/classes/point_alt.dart (initializer-list-with-assert)" replace="/assert\(.*?\)/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
Point.withAssert(this.x, this.y) : [!assert(x >= 0)!] {
  print('In Point.withAssert(): ($x, $y)');
}
{% endprettify %}

Initializer lists are handy when setting up final fields. The following example
initializes three final fields in an initializer list. Click **Run** to execute
the code.

使用初始化列表設定 `final` 欄位非常方便，
下面的範例中就使用初始化列表來設定了三個 `final` 變數的值。
點選執行按鈕執行範例程式碼。

<?code-excerpt "misc/lib/language_tour/classes/point_with_distance_field.dart"?>
```dart:run-dartpad:height-340px:ga_id-initializer_list
import 'dart:math';

class Point {
  final double x;
  final double y;
  final double distanceFromOrigin;

  Point(double x, double y)
      : x = x,
        y = y,
        distanceFromOrigin = sqrt(x * x + y * y);
}

void main() {
  var p = Point(2, 3);
  print(p.distanceFromOrigin);
}
```


#### Redirecting constructors

#### 重新導向建構函式

Sometimes a constructor’s only purpose is to redirect to another
constructor in the same class. A redirecting constructor’s body is
empty, with the constructor call
(using `this` instead of the class name)
appearing after a colon (:).

有時候類中的建構函式僅用於呼叫類中其它的建構函式，此時該建構函式沒有函式體，
只需在函式簽名後使用（:）指定需要重新導向到的其它建構函式 (使用 `this` 而非類別名稱)：

<?code-excerpt "misc/lib/language_tour/classes/point_redirecting.dart"?>
```dart
class Point {
  double x, y;

  // The main constructor for this class.
  Point(this.x, this.y);

  // Delegates to the main constructor.
  Point.alongXAxis(double x) : this(x, 0);
}
```

#### Constant constructors

#### 常量建構函式

If your class produces objects that never change, you can make these
objects compile-time constants. To do this, define a `const` constructor
and make sure that all instance variables are `final`.

如果類產生的物件都是不變的，可以在產生這些物件時就將其變為編譯時常量。
你可以在類別的建構函式前加上 `const` 關鍵字
並確保所有例項變數均為 `final` 來實現該功能。

<?code-excerpt "misc/lib/language_tour/classes/immutable_point.dart"?>
```dart
class ImmutablePoint {
  static const ImmutablePoint origin = ImmutablePoint(0, 0);

  final double x, y;

  const ImmutablePoint(this.x, this.y);
}
```

Constant constructors don't always create constants.
For details, see the section on
[using constructors](#using-constructors).

常量建構函式建立的例項並不總是常量，
具體可以參考[使用建構函式](#using-constructors)章節。


#### Factory constructors

#### 工廠建構函式

Use the `factory` keyword when implementing a constructor that doesn’t
always create a new instance of its class. For example, a factory
constructor might return an instance from a cache, or it might
return an instance of a subtype.
Another use case for factory constructors is
initializing a final variable using
logic that can't be handled in the initializer list.

使用 `factory` 關鍵字標識類別的建構函式將會令該建構函式變為工廠建構函式，
這將意味著使用該建構函式構造類別的例項時並非總是會返回新的例項物件。
例如，工廠建構函式可能會從快取中返回一個例項，或者返回一個子型別的例項。

{{site.alert.tip}}

  Another way to handle late initialization of a final variable
  is to [use `late final` (carefully!)][late-final-ivar].

  另一種處理延遲載入變數的方式是
  [使用 `late final`（謹慎使用）][late-final-ivar]。

{{site.alert.end}}

In the following example,
the `Logger` factory constructor returns objects from a cache,
and the `Logger.fromJson` factory constructor
initializes a final variable from a JSON object.

在如下的範例中，
`Logger` 的工廠建構函式從快取中返回物件，
和 `Logger.fromJson` 工廠建構函式從 JSON 物件中
初始化一個最終變數。

<?code-excerpt "misc/lib/language_tour/classes/logger.dart"?>
```dart
class Logger {
  final String name;
  bool mute = false;

  // _cache is library-private, thanks to
  // the _ in front of its name.
  static final Map<String, Logger> _cache = <String, Logger>{};

  factory Logger(String name) {
    return _cache.putIfAbsent(name, () => Logger._internal(name));
  }

  factory Logger.fromJson(Map<String, Object> json) {
    return Logger(json['name'].toString());
  }

  Logger._internal(this.name);

  void log(String msg) {
    if (!mute) print(msg);
  }
}
```

{{site.alert.note}}

  Factory constructors have no access to `this`.

  在工廠建構函式中無法存取 `this`。

{{site.alert.end}}

Invoke a factory constructor just like you would any other constructor:

工廠建構函式的呼叫方式與其他建構函式一樣：

<?code-excerpt "misc/lib/language_tour/classes/logger.dart (logger)"?>
```dart
var logger = Logger('UI');
logger.log('Button clicked');

var logMap = {'name': 'UI'};
var loggerJson = Logger.fromJson(logMap);
```


### Methods

### 方法

Methods are functions that provide behavior for an object.

方法是為物件提供行為的函式。

#### Instance methods

#### 例項方法

Instance methods on objects can access instance variables and `this`.
The `distanceTo()` method in the following sample is an example of an
instance method:

物件的例項方法可以存取例項變數和 `this`。下面的 `distanceTo()` 方法就是一個例項方法的例子：

<?code-excerpt "misc/lib/language_tour/classes/point.dart (class-with-distanceTo)" plaster="none"?>
```dart
import 'dart:math';

class Point {
  final double x;
  final double y;

  Point(this.x, this.y);

  double distanceTo(Point other) {
    var dx = x - other.x;
    var dy = y - other.y;
    return sqrt(dx * dx + dy * dy);
  }
}
```

#### Operators {#_operators}

#### 運運算元

Operators are instance methods with special names.
Dart allows you to define operators with the following names:

運算子是有著特殊名稱的例項方法。
Dart 允許您使用以下名稱定義運算子：

`<`  | `+`  | `|`  | `>>>`
`>`  | `/`  | `^`  | `[]`
`<=` | `~/` | `&`  | `[]=`
`>=` | `*`  | `<<` | `~`
`-`  | `%`  | `>>` | `==`
{:.table}

{{site.alert.note}}

  You may have noticed that some [operators](#operators), like `!=`, aren't in
  the list of names. That's because they're just syntactic sugar. For example,
  the expression `e1 != e2` is syntactic sugar for `!(e1 == e2)`.

  你可能注意到有一些 [運運算元](#operators) 沒有出現在列表中，例如 `!=`。
  因為它們僅僅是語法糖。
  表示式 `e1 != e2` 僅僅是 `!(e1 == e2)` 的一個語法糖。

{{site.alert.end}}

{%- comment %}
  Internal note from https://github.com/dart-lang/site-www/pull/2691#discussion_r506184100:
  -  `??`, `&&` and `||` are excluded because they are lazy / short-circuiting operators
  - `!` is probably excluded for historical reasons
{% endcomment %}

An operator declaration is identified using the built-in identifier `operator`.
The following example defines vector 
addition (`+`), subtraction (`-`), and equality (`==`):

為了表示重寫運運算元，我們使用 `operator` 標識來進行標記。
下面是重寫 `+` 和 `-` 運運算元的例子

<?code-excerpt "misc/lib/language_tour/classes/vector.dart"?>
```dart
class Vector {
  final int x, y;

  Vector(this.x, this.y);

  Vector operator +(Vector v) => Vector(x + v.x, y + v.y);
  Vector operator -(Vector v) => Vector(x - v.x, y - v.y);

  @override
  bool operator ==(Object other) =>
      other is Vector && x == other.x && y == other.y;

  @override
  int get hashCode => Object.hash(x, y);
}

void main() {
  final v = Vector(2, 3);
  final w = Vector(2, 2);

  assert(v + w == Vector(4, 5));
  assert(v - w == Vector(0, 1));
}
```


#### Getters and setters

#### Getter 和 Setter

Getters and setters are special methods that provide read and write
access to an object’s properties. Recall that each instance variable has
an implicit getter, plus a setter if appropriate. You can create
additional properties by implementing getters and setters, using the
`get` and `set` keywords:

Getter 和 Setter 是一對用來讀寫物件屬性的特殊方法，
上面說過例項物件的每一個屬性都有一個隱含的 Getter 方法，
如果為非 final 屬性的話還會有一個 Setter 方法，
你可以使用 `get` 和 `set` 關鍵字為額外的屬性新增 Getter 和 Setter 方法：

<?code-excerpt "misc/lib/language_tour/classes/rectangle.dart"?>
```dart
class Rectangle {
  double left, top, width, height;

  Rectangle(this.left, this.top, this.width, this.height);

  // Define two calculated properties: right and bottom.
  double get right => left + width;
  set right(double value) => left = value - width;
  double get bottom => top + height;
  set bottom(double value) => top = value - height;
}

void main() {
  var rect = Rectangle(3, 4, 20, 15);
  assert(rect.left == 3);
  rect.right = 12;
  assert(rect.left == -8);
}
```

With getters and setters, you can start with instance variables, later
wrapping them with methods, all without changing client code.

使用 Getter 和 Setter 的好處是，你可以先使用你的例項變數，
過一段時間過再將它們包裹成方法且不需要改動任何程式碼，
即先定義後更改且不影響原有邏輯。

{{site.alert.note}}

  Operators such as increment (++) work in the expected way, whether or
  not a getter is explicitly defined. To avoid any unexpected side
  effects, the operator calls the getter exactly once, saving its value
  in a temporary variable.

  像自增（++）這樣的運運算元不管是否定義了 Getter 方法都會正確地執行。
  為了避免一些不必要的例外情況，
  運算子只會呼叫 Getter 一次，然後將其值儲存在一個臨時變數中。

{{site.alert.end}}

#### Abstract methods

#### 抽象方法

Instance, getter, and setter methods can be abstract, defining an
interface but leaving its implementation up to other classes.
Abstract methods can only exist in [abstract classes](#abstract-classes).

例項方法、Getter 方法以及 Setter 方法都可以是抽象的，
定義一個介面方法而不去做具體的實現讓實現它的類去實現該方法，
抽象方法只能存在於 [抽象類別](#abstract-classes)中。

To make a method abstract, use a semicolon (;) instead of a method body:

直接使用分號（;）替代方法體即可宣告一個抽象方法：

<?code-excerpt "misc/lib/language_tour/classes/doer.dart"?>
```dart
abstract class Doer {
  // Define instance variables and methods...

  void doSomething(); // Define an abstract method.
}

class EffectiveDoer extends Doer {
  void doSomething() {
    // Provide an implementation, so the method is not abstract here...
  }
}
```


### Abstract classes

### 抽象類別

Use the `abstract` modifier to define an *abstract class*—a class that
can’t be instantiated. Abstract classes are useful for defining
interfaces, often with some implementation. If you want your abstract
class to appear to be instantiable, define a [factory constructor][factory].

使用關鍵字 `abstract` 標識類可以讓該類成為 **抽象類別**，抽象類別將無法被例項化。
抽象類別常用於宣告介面方法、有時也會有具體的方法實現。
如果想讓抽象類同時可被例項化，可以為其定義 [工廠建構函式][factory]。

[factory]: #factory-constructors

Abstract classes often have [abstract methods](#abstract-methods).
Here’s an example of declaring an abstract class that has an abstract
method:

抽象類別常常會包含 [抽象方法](#abstract-methods)。
下面是一個宣告具有抽象方法的抽象類別範例：

<?code-excerpt "misc/lib/language_tour/classes/misc.dart (abstract)"?>
```dart
// This class is declared abstract and thus
// can't be instantiated.
abstract class AbstractContainer {
  // Define constructors, fields, methods...

  void updateChildren(); // Abstract method.
}
```

<a id="interfaces"></a>
### Implicit interfaces

### 隱含介面

Every class implicitly defines an interface containing all the instance
members of the class and of any interfaces it implements. If you want to
create a class A that supports class B’s API without inheriting B’s
implementation, class A should implement the B interface.

每一個類別都隱含地定義了一個介面並實現了該介面，
這個介面包含所有這個類別的例項成員以及這個類所實現的其它介面。
如果想要建立一個 A 類支援呼叫 B 類別的 API 且不想繼承 B 類，
則可以實現 B 類別的介面。

A class implements one or more interfaces by declaring them in an
`implements` clause and then providing the APIs required by the
interfaces. For example:

一個類別可以透過關鍵字 `implements` 來實現一個或多個介面
並實現每個介面定義的 API：

<?code-excerpt "misc/lib/language_tour/classes/impostor.dart"?>
```dart
// A person. The implicit interface contains greet().
class Person {
  // In the interface, but visible only in this library.
  final String _name;

  // Not in the interface, since this is a constructor.
  Person(this._name);

  // In the interface.
  String greet(String who) => 'Hello, $who. I am $_name.';
}

// An implementation of the Person interface.
class Impostor implements Person {
  String get _name => '';

  String greet(String who) => 'Hi $who. Do you know who I am?';
}

String greetBob(Person person) => person.greet('Bob');

void main() {
  print(greetBob(Person('Kathy')));
  print(greetBob(Impostor()));
}
```

Here’s an example of specifying that a class implements multiple
interfaces:

如果需要實現多個類介面，可以使用逗號分割每個介面類：

<?code-excerpt "misc/lib/language_tour/classes/misc.dart (point_interfaces)"?>
```dart
class Point implements Comparable, Location {...}
```


### Extending a class

### 擴充一個類別

Use `extends` to create a subclass, and `super` to refer to the
superclass:

使用 `extends` 關鍵字來建立一個子類，
並可使用 `super` 關鍵字參考一個父類：

<?code-excerpt "misc/lib/language_tour/classes/extends.dart" replace="/extends|super/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
class Television {
  void turnOn() {
    _illuminateDisplay();
    _activateIrSensor();
  }
  // ···
}

class SmartTelevision [!extends!] Television {
  void turnOn() {
    [!super!].turnOn();
    _bootNetworkInterface();
    _initializeMemory();
    _upgradeApps();
  }
  // ···
}
{% endprettify %}

For another usage of `extends`, see the discussion of
[parameterized types](#restricting-the-parameterized-type)
in [generics](#generics).

想了解其他 `extends` 的用法，請檢視 [泛型](#generics) 部分中的
[引數化型別](#restricting-the-parameterized-type)。

<a name="overridable-operators"></a>

#### Overriding members

#### 重寫類成員

Subclasses can override instance methods (including [operators](#_operators)),
getters, and setters.
You can use the `@override` annotation to indicate that you are
intentionally overriding a member:

子類別可以重寫父類別的例項方法（包括 [運運算元](#_operators)）、
Getter 以及 Setter 方法。
你可以使用 `@override` 註解來表示你重寫了一個成員：

<?code-excerpt "misc/lib/language_tour/metadata/television.dart (override)" replace="/@override/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
class Television {
  // ···
  set contrast(int value) {...}
}

class SmartTelevision extends Television {
  [!@override!]
  set contrast(num value) {...}
  // ···
}
{% endprettify %}

An overriding method declaration must match
the method (or methods) that it overrides in several ways:

* The return type must be the same type as (or a subtype of)
  the overridden method's return type.
* Argument types must be the same type as (or a supertype of)
  the overridden method's argument types.
  In the preceding example, the `contrast` setter of `SmartTelevision`
  changes the argument type from `int` to a supertype, `num`.
* If the overridden method accepts _n_ positional parameters,
  then the overriding method must also accept _n_ positional parameters.
* A [generic method](#using-generic-methods) can't override a non-generic one,
  and a non-generic method can't override a generic one.

Sometimes you might want to narrow the type of
a method parameter or an instance variable.
This violates the normal rules, and
it's similar to a downcast in that it can cause a type error at runtime.
Still, narrowing the type is possible
if the code can guarantee that a type error won't occur.
In this case, you can use the 
[`covariant` keyword](/guides/language/sound-problems#the-covariant-keyword)
in a parameter declaration.
For details, see the 
[Dart language specification][].

你可以使用 [`covariant` 關鍵字](/guides/language/sound-problems#the-covariant-keyword)
來縮小程式碼中那些符合 [型別安全](/guides/language/type-system) 的方法引數或例項變數的型別。

{{site.alert.warning}}

  If you override `==`, you should also override Object's `hashCode` getter.
  For an example of overriding `==` and `hashCode`, see
  [Implementing map keys](/guides/libraries/library-tour#implementing-map-keys).

  如果重寫 `==` 運運算元，必須同時重寫物件 `hashCode` 的 Getter 方法。
  你可以查閱 [實現對映鍵](/guides/libraries/library-tour#implementing-map-keys)
  獲取更多關於重寫的 `==` 和 `hashCode` 的例子。

{{site.alert.end}}

#### noSuchMethod()

#### noSuchMethod 方法

To detect or react whenever code attempts to use a non-existent method or
instance variable, you can override `noSuchMethod()`:

如果呼叫了物件上不存在的方法或例項變數將會觸發 `noSuchMethod` 方法，
你可以重寫 `noSuchMethod` 方法來追蹤和記錄這一行為：

<?code-excerpt "misc/lib/language_tour/classes/no_such_method.dart" replace="/noSuchMethod(?!,)/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
class A {
  // Unless you override noSuchMethod, using a
  // non-existent member results in a NoSuchMethodError.
  @override
  void [!noSuchMethod!](Invocation invocation) {
    print('You tried to use a non-existent member: '
        '${invocation.memberName}');
  }
}
{% endprettify %}

You **can't invoke** an unimplemented method unless
**one** of the following is true:

只有下面其中一個條件成立時，你才能呼叫一個未實現的方法：

* The receiver has the static type `dynamic`.

  接收方是靜態的 `dynamic` 型別。

* The receiver has a static type that
defines the unimplemented method (abstract is OK),
and the dynamic type of the receiver has an implementation of `noSuchMethod()`
that's different from the one in class `Object`.

  接收方具有靜態型別，定義了未實現的方法（抽象亦可），
  並且接收方的動態型別實現了 `noSuchMethod`
  方法且具體的實現與 `Object` 中的不同。

For more information, see the informal
[noSuchMethod forwarding specification.](https://github.com/dart-lang/language/blob/master/archive/feature-specifications/nosuchmethod-forwarding.md)

你可以查閱
[noSuchMethod 轉發規範](https://github.com/dart-lang/language/blob/master/archive/feature-specifications/nosuchmethod-forwarding.md)
獲取更多相關資訊。

### Extension methods

### 擴充方法

Extension methods are a way to add functionality to existing libraries.
You might use extension methods without even knowing it.
For example, when you use code completion in an IDE,
it suggests extension methods alongside regular methods.

擴充方法是向現有庫新增功能的一種方式。
你可能已經在不知道它是擴充方法的情況下使用了它。
例如，當您在 IDE 中使用程式碼完成功能時，
它建議將擴充方法與常規方法一起使用。

Here's an example of using an extension method on `String`
named `parseInt()` that's defined in `string_apis.dart`:

這裡是一個在 `String` 中使用擴充方法的範例，
我們取名為 `parseInt()`，它在 `string_apis.dart` 中定義：

```dart
import 'string_apis.dart';
...
print('42'.padLeft(5)); // Use a String method.
print('42'.parseInt()); // Use an extension method.
```

For details of using and implementing extension methods, see the
[extension methods page][].

有關使用以及實現擴充方法的詳細資訊，請參閱
[擴充方法頁面][extension methods page]。

<a id="enums"></a>
### Enumerated types

### 列舉型別

Enumerated types, often called _enumerations_ or _enums_,
are a special kind of class used to represent
a fixed number of constant values.

列舉型別是一種特殊的型別，也稱為 **enumerations** 或 **enums**，
用於定義一些固定數量的常量值。

{{site.alert.note}}

  All enums automatically extend the [`Enum`][] class.
  They are also sealed, 
  meaning they cannot be subclassed, implemented, mixed in, 
  or otherwise explicitly instantiated.

  所有的列舉都繼承於 [`Enum`][] 類別。
  列舉類是封閉的，即不能被繼承、被實現、被 mixin 混入或顯式被例項化。

  Abstract classes and mixins can explicitly implement or extend `Enum`,
  but unless they are then implemented by or mixed into an enum declaration,
  no objects can actually implement the type of that class or mixin.

  抽象類別和 mixin 可以顯式的實現或繼承 `Enum`，
  但只有列舉可以實現或混入這個類，其他類無法享有同樣的操作。

{{site.alert.end}}

#### Declaring simple enums

#### 宣告簡單的列舉

To declare a simple enumerated type,
use the `enum` keyword and
list the values you want to be enumerated:

你可以使用關鍵字 `enum` 來定義簡單的列舉型別和列舉值：

<?code-excerpt "misc/lib/language_tour/classes/enum.dart (enum)"?>
```dart
enum Color { red, green, blue }
```

{{site.alert.tip}}
  You can also use [trailing commas][] when declaring an enumerated type
  to help prevent copy-paste errors.
{{site.alert.end}}

#### Declaring enhanced enums

#### 宣告增強的列舉型別

Dart also allows enum declarations to declare classes
with fields, methods, and const constructors
which are limited to a fixed number of known constant instances.

Dart 中的列舉也支援定義欄位、方法和常量構造，
常量構造只能構造出已知數量的常量例項（已定義的列舉值）。

To declare an enhanced enum,
follow a syntax similar to normal [classes](#classes),
but with a few extra requirements:

你可以使用與定義 [類](#classes) 類似的陳述式來定義增強的列舉，
但是這樣的定義有一些限制條件：

* Instance variables must be `final`, 
  including those added by [mixins](#mixins).

  例項的欄位必須是 `final`，包括由 [mixin](#mixins) 混入的欄位。

* All [generative constructors](#constant-constructors) must be constant.

  所有的 [例項化構造](#constant-constructors) 必須以 `const` 修飾。

* [Factory constructors](#factory-constructors) can only return
  one of the fixed, known enum instances.

  [工廠構造](#factory-constructors) 只能返回已知的一個列舉例項。

* No other class can be extended as [`Enum`] is automatically extended.

  由於 [`Enum`][] 已經自動進行了繼承，所以列舉類不能再繼承其他類別。

* There cannot be overrides for `index`, `hashCode`, the equality operator `==`.

  不能重載 `index`、`hashCode` 和比較運運算元 `==`。

* A member named `values` cannot be declared in an enum,
  as it would conflict with the automatically generated static `values` getter.

  不能宣告 `values` 欄位，否則它將與列舉本身的靜態 `values` getter 衝突。

* All instances of the enum must be declared
  in the beginning of the declaration,
  and there must be at least one instance declared.

  在進行列舉定義時，所有的例項都需要首先進行宣告，
  且至少要宣告一個列舉例項。


Here is an example that declares an enhanced enum
with multiple instances, instance variables,
a getter, and an implemented interface:

下方是一個增強列舉的例子，
它包含多個列舉例項、成員變數、getter 並且實現了介面：

<?code-excerpt "misc/lib/language_tour/classes/enum.dart (enhanced)"?>
```dart
enum Vehicle implements Comparable<Vehicle> {
  car(tires: 4, passengers: 5, carbonPerKilometer: 400),
  bus(tires: 6, passengers: 50, carbonPerKilometer: 800),
  bicycle(tires: 2, passengers: 1, carbonPerKilometer: 0);

  const Vehicle({
    required this.tires,
    required this.passengers,
    required this.carbonPerKilometer,
  });

  final int tires;
  final int passengers;
  final int carbonPerKilometer;

  int get carbonFootprint => (carbonPerKilometer / passengers).round();

  @override
  int compareTo(Vehicle other) => carbonFootprint - other.carbonFootprint;
}
```

To learn more about declaring enhanced enums,
see the section on [Classes](#classes).

想要了解更多關於定義增強列舉的內容，
可以閱讀 [類](#classes) 小節。

{{site.alert.version-note}}

  Enhanced enums require a [language version][] of at least 2.17.

  增強列舉僅在 [Dart SDK 版本][language version] 2.17 以上可用。

{{site.alert.end}}

#### Using enums

#### 使用列舉

Access the enumerated values like
any other [static variable](#static-variables):

你可以像存取 [靜態變數](#static-variables) 一樣存取列舉值：

<?code-excerpt "misc/lib/language_tour/classes/enum.dart (access)"?>
```dart
final favoriteColor = Color.blue;
if (favoriteColor == Color.blue) {
  print('Your favorite color is blue!');
}
```

Each value in an enum has an `index` getter,
which returns the zero-based position of the value in the enum declaration.
For example, the first value has index 0,
and the second value has index 1.

每一個列舉值都有一個名為 `index` 成員變數的 Getter 方法，
該方法將會返回以 0 為基準索引的位置值。
例如，第一個列舉值的索引是 0 ，第二個列舉值的索引是 1。以此類推。

<?code-excerpt "misc/lib/language_tour/classes/enum.dart (index)"?>
```dart
assert(Color.red.index == 0);
assert(Color.green.index == 1);
assert(Color.blue.index == 2);
```

To get a list of all the enumerated values,
use the enum's `values` constant.

想要獲得全部的列舉值，使用列舉類別的 `values` 方法獲取套件含它們的列表：

<?code-excerpt "misc/lib/language_tour/classes/enum.dart (values)"?>
```dart
List<Color> colors = Color.values;
assert(colors[2] == Color.blue);
```

You can use enums in [switch statements](#switch-and-case), and
you'll get a warning if you don't handle all of the enum's values:

你可以在 [Switch 陳述式](#switch-and-case)中使用列舉，
但是需要注意的是必須處理列舉值的每一種情況，
即每一個列舉值都必須成為一個 case 子句，不然會出現警告：

<?code-excerpt "misc/lib/language_tour/classes/enum.dart (switch)"?>
```dart
var aColor = Color.blue;

switch (aColor) {
  case Color.red:
    print('Red as roses!');
    break;
  case Color.green:
    print('Green as grass!');
    break;
  default: // Without this, you see a WARNING.
    print(aColor); // 'Color.blue'
}
```

If you need to access the name of an enumerated value,
such as `'blue'` from `Color.blue`,
use the `.name` property:

如果你想要獲取一個列舉值的名稱，例如 `Color.blue` 的 `'blue'`，
請使用 `.name` 屬性：

<?code-excerpt "misc/lib/language_tour/classes/enum.dart (name)"?>
```dart
print(Color.blue.name); // 'blue'
```


<a id="mixins"></a>
### Adding features to a class: mixins

### 使用 Mixin 為類新增功能

Mixins are a way of reusing a class's code in multiple class
hierarchies.

Mixin 是一種在多重繼承中複用某個類中程式碼的方法模式。

To _use_ a mixin, use the `with` keyword followed by one or more mixin
names. The following example shows two classes that use mixins:

使用 `with` 關鍵字並在其後跟上 Mixin 類別的名字來使用 Mixin 模式：

<?code-excerpt "misc/lib/language_tour/classes/orchestra.dart (Musician and Maestro)" replace="/(with.*) \{/[!$1!] {/g"?>
{% prettify dart tag=pre+code %}
class Musician extends Performer [!with Musical!] {
  // ···
}

class Maestro extends Person [!with Musical, Aggressive, Demented!] {
  Maestro(String maestroName) {
    name = maestroName;
    canConduct = true;
  }
}
{% endprettify %}

To _implement_ a mixin, create a class that extends Object and
declares no constructors.
Unless you want your mixin to be usable as a regular class,
use the `mixin` keyword instead of `class`.
For example:

想要實現一個 Mixin，請建立一個繼承自 Object 且未宣告建構函式的類別。
除非你想讓該類與普通的類一樣可以被正常地使用，否則請使用關鍵字 `mixin` 替代 `class`。
例如：

<?code-excerpt "misc/lib/language_tour/classes/orchestra.dart (Musical)"?>
```dart
mixin Musical {
  bool canPlayPiano = false;
  bool canCompose = false;
  bool canConduct = false;

  void entertainMe() {
    if (canPlayPiano) {
      print('Playing piano');
    } else if (canConduct) {
      print('Waving hands');
    } else {
      print('Humming to self');
    }
  }
}
```

Sometimes you might want to restrict the types that can use a mixin.
For example, the mixin might depend on being able to invoke a method
that the mixin doesn't define.
As the following example shows, you can restrict a mixin's use
by using the `on` keyword to specify the required superclass:

可以使用關鍵字 `on` 來指定哪些類可以使用該 Mixin 類，
比如有 Mixin 類 A，但是 A 只能被 B 類使用，
則可以這樣定義 A：

<?code-excerpt "misc/lib/language_tour/classes/orchestra.dart (mixin-on)" plaster="none" replace="/on Musician2/[!on Musician!]/g" ?>
```dart
class Musician {
  // ...
}
mixin MusicalPerformer [!on Musician!] {
  // ...
}
class SingerDancer extends Musician with MusicalPerformer {
  // ...
}
```

In the preceding code,
only classes that extend or implement the `Musician` class
can use the mixin `MusicalPerformer`.
Because `SingerDancer` extends `Musician`,
`SingerDancer` can mix in `MusicalPerformer`.

{{site.alert.version-note}}

  Support for the `mixin` keyword was introduced in Dart 2.1. Code in earlier
  releases usually used `abstract class` instead. For more information on 2.1
  mixin changes, see the [Dart SDK changelog][] and [2.1 mixin specification.][]

  `mixin` 關鍵字在 Dart 2.1 中才被參考支援。
  早期版本中的程式碼通常使用 `abstract class` 代替。
  你可以查閱
  [Dart SDK 變更日誌][Dart SDK changelog]
  和 [2.1 mixin 規範][2.1 mixin specification.]
  獲取更多有關 Mixin 在 2.1 中的變更資訊。

{{site.alert.end}}

[Dart SDK changelog]: https://github.com/dart-lang/sdk/blob/master/CHANGELOG.md
[2.1 mixin specification.]: https://github.com/dart-lang/language/blob/master/accepted/2.1/super-mixins/feature-specification.md#dart-2-mixin-declarations

[Dart SDK 變更日誌]: https://github.com/dart-lang/sdk/blob/master/CHANGELOG.md
[2.1 mixin 規範]: https://github.com/dart-lang/language/blob/master/accepted/2.1/super-mixins/feature-specification.md#dart-2-mixin-declarations


### Class variables and methods

### 類變數和方法

Use the `static` keyword to implement class-wide variables and methods.

使用關鍵字 `static` 可以宣告類變數或類方法。

#### Static variables

#### 靜態變數

Static variables (class variables) are useful for class-wide state and
constants:

靜態變數（即類變數）常用於宣告類範圍內所屬的狀態變數和常量：

<?code-excerpt "misc/lib/language_tour/classes/misc.dart (static-field)"?>
```dart
class Queue {
  static const initialCapacity = 16;
  // ···
}

void main() {
  assert(Queue.initialCapacity == 16);
}
```

Static variables aren’t initialized until they’re used.

靜態變數在其首次被使用的時候才被初始化。

{{site.alert.note}}

  This page follows the [style guide
  recommendation](/guides/language/effective-dart/style#identifiers)
  of preferring `lowerCamelCase` for constant names.

  本文程式碼準守
  [風格推薦指南](/guides/language/effective-dart/style#identifiers)
  中的命名規則，使用 `駝峰式大小寫` 來命名常量。

{{site.alert.end}}

#### Static methods

#### 靜態方法

Static methods (class methods) don't operate on an instance, and thus
don't have access to `this`.
They do, however, have access to static variables.
As the following example shows,
you invoke static methods directly on a class:

靜態方法（即類方法）不能對例項進行操作，因此不能使用 `this`。
但是他們可以存取靜態變數。
如下面的例子所示，你可以在一個類別上直接呼叫靜態方法：

<?code-excerpt "misc/lib/language_tour/classes/point_with_distance_method.dart"?>
```dart
import 'dart:math';

class Point {
  double x, y;
  Point(this.x, this.y);

  static double distanceBetween(Point a, Point b) {
    var dx = a.x - b.x;
    var dy = a.y - b.y;
    return sqrt(dx * dx + dy * dy);
  }
}

void main() {
  var a = Point(2, 2);
  var b = Point(4, 4);
  var distance = Point.distanceBetween(a, b);
  assert(2.8 < distance && distance < 2.9);
  print(distance);
}
```

{{site.alert.note}}

  Consider using top-level functions, instead of static methods, for
  common or widely used utilities and functionality.

  對於一些通用或常用的靜態方法，應該將其定義為最上層函式而非靜態方法。

{{site.alert.end}}

You can use static methods as compile-time constants. For example, you
can pass a static method as a parameter to a constant constructor.

可以將靜態方法作為編譯時常量。例如，你可以將靜態方法作為一個引數傳遞給一個常量建構函式。


## Generics

## 泛型

If you look at the API documentation for the basic array type,
[`List`][], you’ll see that the
type is actually `List<E>`. The \<...\> notation marks List as a
*generic* (or *parameterized*) type—a type that has formal type
parameters. [By convention][], most type variables have single-letter names,
such as E, T, S, K, and V.

如果你檢視陣列的 API 文件，你會發現陣列 [`List`][] 的實際型別為 `List<E>`。
\<...\> 符號表示陣列是一個 *泛型*（或 *引數化型別*）
[通常][By convention] 使用一個字母來代表型別引數，比如 E、T、S、K 和 V 等等。

[By convention]: /guides/language/effective-dart/design#do-follow-existing-mnemonic-conventions-when-naming-type-parameters


### Why use generics?

### 為什麼使用泛型？

Generics are often required for type safety, but they have more benefits
than just allowing your code to run:

泛型常用於需要要求型別安全的情況，但是它也會對程式碼執行有好處：

* Properly specifying generic types results in better generated code.

  適當地指定泛型可以更好地幫助程式碼產生器。

* You can use generics to reduce code duplication.

  使用泛型可以減少程式碼重複。

If you intend for a list to contain only strings, you can
declare it as `List<String>` (read that as “list of string”). That way
you, your fellow programmers, and your tools can detect that assigning a non-string to
the list is probably a mistake. Here’s an example:

比如你想宣告一個只能包含 String 型別的陣列，
你可以將該陣列宣告為 `List<String>`（讀作“字串型別的 list”），
這樣的話就可以很容易避免因為在該陣列放入非 String 類變數而導致的諸多問題，
同時編譯器以及其他閱讀程式碼的人都可以很容易地發現並定位問題：

{:.fails-sa}
```dart
var names = <String>[];
names.addAll(['Seth', 'Kathy', 'Lars']);
names.add(42); // Error
```

Another reason for using generics is to reduce code duplication.
Generics let you share a single interface and implementation between
many types, while still taking advantage of static
analysis. For example, say you create an interface for
caching an object:

另一個使用泛型的原因是可以減少重複程式碼。
泛型可以讓你在多個不同型別實現之間共享同一個介面宣告，
比如下面的例子中聲明瞭一個類別用於快取物件的介面：

<?code-excerpt "misc/lib/language_tour/generics/cache.dart (ObjectCache)"?>
```dart
abstract class ObjectCache {
  Object getByKey(String key);
  void setByKey(String key, Object value);
}
```

You discover that you want a string-specific version of this interface,
so you create another interface:

不久後你可能又會想專門為 String 類物件做一個快取，
於是又有了專門為 String 做快取的類：

<?code-excerpt "misc/lib/language_tour/generics/cache.dart (StringCache)"?>
```dart
abstract class StringCache {
  String getByKey(String key);
  void setByKey(String key, String value);
}
```

Later, you decide you want a number-specific version of this
interface... You get the idea.

如果過段時間你又想為數字型別也建立一個類別，
那麼就會有很多諸如此類別的程式碼……

Generic types can save you the trouble of creating all these interfaces.
Instead, you can create a single interface that takes a type parameter:

這時候可以考慮使用泛型來宣告一個類別，
讓不同型別的快取實現該類做出不同的具體實現即可：

<?code-excerpt "misc/lib/language_tour/generics/cache.dart (Cache)"?>
```dart
abstract class Cache<T> {
  T getByKey(String key);
  void setByKey(String key, T value);
}
```

In this code, T is the stand-in type. It’s a placeholder that you can
think of as a type that a developer will define later.

在上述程式碼中，T 是一個替代型別。
其相當於型別佔位符，在開發者呼叫該介面的時候會指定具體型別。


### Using collection literals

### 使用集合字面量

List, set, and map literals can be parameterized. Parameterized literals are
just like the literals you’ve already seen, except that you add
<code>&lt;<em>type</em>></code> (for lists and sets) or
<code>&lt;<em>keyType</em>, <em>valueType</em>></code> (for maps)
before the opening bracket. Here is an example of using typed literals:

List、Set 以及 Map 字面量也可以是引數化的。
定義引數化的 List 只需在中括號前新增
<code>&lt;<em>type</em>></code>；
定義引數化的 Map 只需要在大括號前新增
<code>&lt;<em>keyType</em>, <em>valueType</em>></code>：

<?code-excerpt "misc/lib/language_tour/generics/misc.dart (collection-literals)"?>
```dart
var names = <String>['Seth', 'Kathy', 'Lars'];
var uniqueNames = <String>{'Seth', 'Kathy', 'Lars'};
var pages = <String, String>{
  'index.html': 'Homepage',
  'robots.txt': 'Hints for web robots',
  'humans.txt': 'We are people, not machines'
};
```


### Using parameterized types with constructors

### 使用型別引數化的建構函式

To specify one or more types when using a constructor, put the types in
angle brackets (`<...>`) just after the class name. For example:

在呼叫構造方法時也可以使用泛型，
只需在類別名稱後用尖括號（`<...>`）將一個或多個型別包裹即可：

<?code-excerpt "misc/test/language_tour/generics_test.dart (constructor-1)"?>
```dart
var nameSet = Set<String>.from(names);
```

{% comment %}[TODO #2950: It isn't idiomatic to use a constructor for an empty Map. Change to a class that doesn't have literal support.]{% endcomment %}

The following code creates a map that has integer keys and values of
type View:

下面程式碼建立了一個鍵為 Int 型別，值為 View 型別的 Map 物件：

<?code-excerpt "misc/test/language_tour/generics_test.dart (constructor-2)"?>
```dart
var views = Map<int, View>();
```


### Generic collections and the types they contain

### 泛型集合以及它們所包含的型別

Dart generic types are *reified*, which means that they carry their type
information around at runtime. For example, you can test the type of a
collection:

Dart的泛型型別是 **固化的**，這意味著即便在執行時也會保持型別資訊：

<?code-excerpt "misc/test/language_tour/generics_test.dart (generic-collections)"?>
```dart
var names = <String>[];
names.addAll(['Seth', 'Kathy', 'Lars']);
print(names is List<String>); // true
```

{{site.alert.note}}

  In contrast, generics in Java use *erasure*, which means that generic
  type parameters are removed at runtime. In Java, you can test whether
  an object is a List, but you can’t test whether it’s a `List<String>`.

  與 Java 不同的是，Java 中的泛型是型別 **擦除** 的，這意味著泛型型別會在執行時被移除。在 Java 中你可以判斷物件是否為 List 但不可以判斷物件是否為 `List<String>`。

{{site.alert.end}}


### Restricting the parameterized type

### 限制引數化型別

When implementing a generic type,
you might want to limit the types that can be provided as arguments,
so that the argument must be a subtype of a particular type.
You can do this using `extends`.

有時使用泛型的時候，你可能會想限制可作為引數的泛型範圍，
也就是引數必須是指定型別的子類別，
這時候可以使用 `extends` 關鍵字。

A common use case is ensuring that a type is non-nullable
by making it a subtype of `Object`
(instead of the default, [`Object?`][top-and-bottom]).

一種常見的非空型別處理方式，是將子類別限制繼承 `Object`
（而不是預設的 [`Object?`][top-and-bottom]）。

<?code-excerpt "misc/lib/language_tour/generics/misc.dart (non-nullable)"?>
```dart
class Foo<T extends Object> {
  // Any type provided to Foo for T must be non-nullable.
}
```

You can use `extends` with other types besides `Object`.
Here's an example of extending `SomeBaseClass`,
so that members of `SomeBaseClass` can be called on objects of type `T`:

<?code-excerpt "misc/lib/language_tour/generics/base_class.dart" replace="/extends SomeBaseClass(?=. \{)/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
class Foo<T [!extends SomeBaseClass!]> {
  // Implementation goes here...
  String toString() => "Instance of 'Foo<$T>'";
}

class Extender extends SomeBaseClass {...}
{% endprettify %}

It's OK to use `SomeBaseClass` or any of its subtypes as the generic argument:

這時候就可以使用 `SomeBaseClass` 或者它的子類別來作為泛型引數：

<?code-excerpt "misc/test/language_tour/generics_test.dart (SomeBaseClass-ok)" replace="/Foo.\w+./[!$&!]/g"?>
{% prettify dart tag=pre+code %}
var someBaseClassFoo = [!Foo<SomeBaseClass>!]();
var extenderFoo = [!Foo<Extender>!]();
{% endprettify %}

It's also OK to specify no generic argument:

這時候也可以指定無引數的泛型，這時無引數泛型的型別則為 `Foo<SomeBaseClass>`：

<?code-excerpt "misc/test/language_tour/generics_test.dart (no-generic-arg-ok)" replace="/expect\((.*?).toString\(\), .(.*?).\);/print($1); \/\/ $2/g"?>
```dart
var foo = Foo();
print(foo); // Instance of 'Foo<SomeBaseClass>'
```

Specifying any non-`SomeBaseClass` type results in an error:

將非 `SomeBaseClass` 的型別作為泛型引數則會導致編譯錯誤：

{:.fails-sa}
{% prettify dart tag=pre+code %}
var foo = [!Foo<Object>!]();
{% endprettify %}


### Using generic methods

### 使用泛型方法

Methods and functions also allow type arguments:

方法和引數也可以使用型別引數了:

<!-- {{site.dartpad}}/a02c53b001977efa4d803109900f21bb -->
<!-- https://gist.github.com/a02c53b001977efa4d803109900f21bb -->
<?code-excerpt "misc/test/language_tour/generics_test.dart (method)" replace="/<T.(?=\()|T/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
[!T!] first[!<T>!](List<[!T!]> ts) {
  // Do some initial work or error checking, then...
  [!T!] tmp = ts[0];
  // Do some additional checking or processing...
  return tmp;
}
{% endprettify %}

Here the generic type parameter on `first` (`<T>`)
allows you to use the type argument `T` in several places:

方法 `first<T>` 的泛型 `T` 可以在如下地方使用：

* In the function's return type (`T`).

  函式的返回值型別 (`T`)。

* In the type of an argument (`List<T>`).

  引數的型別 (`List<T>`)。

* In the type of a local variable (`T tmp`).

  區域變數的型別 (`T tmp`)。

For more information about generics, see
[Using Generic Methods.](https://github.com/dart-lang/sdk/blob/master/pkg/dev_compiler/doc/GENERIC_METHODS.md)

你可以查閱
[使用泛型函式](https://github.com/dart-lang/sdk/blob/master/pkg/dev_compiler/doc/GENERIC_METHODS.md)
獲取更多關於泛型的資訊。


## Libraries and visibility

## 庫和可見性

The `import` and `library` directives can help you create a
modular and shareable code base. Libraries not only provide APIs, but
are a unit of privacy: identifiers that start with an underscore (`_`)
are visible only inside the library. *Every Dart app is a library*, even
if it doesn’t use a `library` directive.

`import` 和 `library` 關鍵字可以幫助你建立一個模組化和可共享的程式碼庫。
程式碼庫不僅只是提供 API 而且還起到了封裝的作用：以下劃線（`_`）開頭的成員僅在程式碼庫中可見。
**每個 Dart 程式都是一個函式庫**，即便沒有使用關鍵字 `library` 指定。

Libraries can be distributed using [packages](/guides/packages).

Dart 的函式庫可以使用 [包工具](/guides/packages) 來發布和部署。

{{site.alert.info}}

  If you're curious why Dart uses underscores instead of
  access modifier keywords like `public` or `private`, see
  [SDK issue 33383](https://github.com/dart-lang/sdk/issues/33383).

  如果你對 Dart 為何使用下劃線而不使用 `public` 或 `private` 作為可及性關鍵字，
  可以檢視 [SDK issue 33383](https://github.com/dart-lang/sdk/issues/33383)。

{{site.alert.end}}

### Using libraries


### 使用庫

Use `import` to specify how a namespace from one library is used in the
scope of another library.

使用 `import` 來指定名稱空間以便其它庫可以存取。

For example, Dart web apps generally use the [dart:html][]
library, which they can import like this:

比如你可以匯入程式碼庫 [dart:html][] 來使用 Dart Web 中相關 API：

<?code-excerpt "misc/test/language_tour/browser_test.dart (dart-html-import)"?>
```dart
import 'dart:html';
```

The only required argument to `import` is a URI specifying the
library.
For built-in libraries, the URI has the special `dart:` scheme.
For other libraries, you can use a file system path or the `package:`
scheme. The `package:` scheme specifies libraries provided by a package
manager such as the pub tool. For example:

`import` 的唯一引數是用於指定程式碼庫的 URI，對於 Dart 內建的函式庫，使用 `dart:xxxxxx` 的形式。
而對於其它的函式庫，你可以使用一個檔案系統路徑或者以 `package:xxxxxx` 的形式。
`package:xxxxxx` 指定的函式庫透過包管理器（比如 pub 工具）來提供：

<?code-excerpt "misc/test/language_tour/browser_test.dart (package-import)"?>
```dart
import 'package:test/test.dart';
```

{{site.alert.note}}

  *URI* stands for uniform resource identifier.

  **URI** 代表統一資源識別符號。

  *URLs* (uniform resource locators) are a common kind of URI.

  **URL**（統一資源定位符）是一種常見的 URI。

{{site.alert.end}}


#### Specifying a library prefix

#### 指定庫字首

If you import two libraries that have conflicting identifiers, then you
can specify a prefix for one or both libraries. For example, if library1
and library2 both have an Element class, then you might have code like
this:

如果你匯入的兩個程式碼庫有衝突的識別符號，你可以為其中一個指定字首。
比如如果 library1 和 library2 都有 Element 類，那麼可以這麼處理：

<?code-excerpt "misc/lib/language_tour/libraries/import_as.dart" replace="/(lib\d)\.dart/package:$1\/$&/g"?>
```dart
import 'package:lib1/lib1.dart';
import 'package:lib2/lib2.dart' as lib2;

// Uses Element from lib1.
Element element1 = Element();

// Uses Element from lib2.
lib2.Element element2 = lib2.Element();
```

#### Importing only part of a library

#### 匯入庫的一部分

If you want to use only part of a library, you can selectively import
the library. For example:

如果你只想使用程式碼庫中的一部分，你可以有選擇地匯入程式碼庫。例如：

<?code-excerpt "misc/lib/language_tour/libraries/show_hide.dart" replace="/(lib\d)\.dart/package:$1\/$&/g"?>
```dart
// Import only foo.
import 'package:lib1/lib1.dart' show foo;

// Import all names EXCEPT foo.
import 'package:lib2/lib2.dart' hide foo;
```

<a id="deferred-loading"></a>
#### Lazily loading a library

#### 延遲載入庫

_Deferred loading_ (also called _lazy loading_)
allows a web app to load a library on demand,
if and when the library is needed.
Here are some cases when you might use deferred loading:

**延遲載入**（也常稱為 **延遲載入**）允許應用在需要時再去載入程式碼庫，
下面是可能使用到延遲載入的場景：

* To reduce a web app's initial startup time.

  為了減少應用的初始化時間。

* To perform A/B testing—trying out
  alternative implementations of an algorithm, for example.

  處理 A/B 測試，比如測試各種演算法的不同實現。

* To load rarely used functionality, such as optional screens and dialogs.

  載入很少會使用到的功能，比如可選的螢幕和對話方塊。


{{site.alert.warn}}

  **Only `dart compile js` supports deferred loading.**
  Flutter and the Dart VM don't support deferred loading.
  To learn more, see
  [issue #33118](https://github.com/dart-lang/sdk/issues/33118) and
  [issue #27776.](https://github.com/dart-lang/sdk/issues/27776)

  **目前只有 `dart compile js` 支援延遲載入**
  Flutter 和 Dart VM目前都不支援延遲載入。
  你可以查閱 [issue #33118](https://github.com/dart-lang/sdk/issues/33118)
  和 [issue #27776](https://github.com/dart-lang/sdk/issues/27776) 獲取更多的相關資訊。

{{site.alert.end}}

To lazily load a library, you must first
import it using `deferred as`.

使用 `deferred as` 關鍵字來標識需要延時載入的程式碼庫：

<?code-excerpt "misc/lib/language_tour/libraries/greeter.dart (import)" replace="/hello\.dart/package:greetings\/$&/g"?>
```dart
import 'package:greetings/hello.dart' deferred as hello;
```

When you need the library, invoke
`loadLibrary()` using the library's identifier.

當實際需要使用到庫中 API 時先呼叫 `loadLibrary` 函式載入庫：

<?code-excerpt "misc/lib/language_tour/libraries/greeter.dart (loadLibrary)"?>
```dart
Future<void> greet() async {
  await hello.loadLibrary();
  hello.printGreeting();
}
```

In the preceding code,
the `await` keyword pauses execution until the library is loaded.
For more information about `async` and `await`,
see [asynchrony support](#asynchrony-support).

在前面的程式碼，使用 `await` 關鍵字暫停程式碼執行直到庫載入完成。更多關於 `async` 和 `await` 的資訊請參考[非同步支援](#asynchrony-support)。

You can invoke `loadLibrary()` multiple times on a library without problems.
The library is loaded only once.

`loadLibrary` 函式可以呼叫多次也沒關係，程式碼庫只會被載入一次。

Keep in mind the following when you use deferred loading:

當你使用延遲載入的時候需要牢記以下幾點：

* A deferred library's constants aren't constants in the importing file.
  Remember, these constants don't exist until the deferred library is loaded.

  延遲載入的程式碼庫中的常量需要在程式碼庫被載入的時候才會匯入，未載入時是不會匯入的。

* You can't use types from a deferred library in the importing file.
  Instead, consider moving interface types to a library imported by
  both the deferred library and the importing file.

  匯入檔案的時候無法使用延遲載入庫中的型別。如果你需要使用型別，則考慮把介面型別轉移到另一個函式庫中然後讓兩個庫都分別匯入這個介面庫。

* Dart implicitly inserts `loadLibrary()` into the namespace that you define
  using <code>deferred as <em>namespace</em></code>.
  The `loadLibrary()` function returns a [`Future`](/guides/libraries/library-tour#future).

  Dart會隱含地將 `loadLibrary()` 匯入到使用了
  <code>deferred as <em>名稱空間</em></code> 的類中。
  `loadLibrary()` 函式返回的是一個 [Future](/guides/libraries/library-tour#future)。

### Implementing libraries

### 實現庫

See
[Create Library Packages](/guides/libraries/create-library-packages)
for advice on how to implement a library package, including:

查閱 [建立依賴庫包](/guides/libraries/create-library-packages) 可以獲取有關如何實現庫套件的建議，包括：

* How to organize library source code.

  如何組織庫的原始檔。

* How to use the `export` directive.

  如何使用 `export` 命令。

* When to use the `part` directive.

  何時使用 `part` 命令。

* When to use the `library` directive.

  何時使用 `library` 命令。

* How to use conditional imports and exports to implement
  a library that supports multiple platforms.

  如何使用匯入和匯出命令實現多平臺的函式庫支援。


<a id="asynchrony"></a>
## Asynchrony support

## 非同步支援

Dart libraries are full of functions that
return [`Future`][] or [`Stream`][] objects.
These functions are _asynchronous_:
they return after setting up
a possibly time-consuming operation
(such as I/O),
without waiting for that operation to complete.

Dart 程式碼庫中有大量返回 [`Future`][] 或 [`Stream`][] 物件的函式，這些函式都是 **非同步** 的，
它們會在耗時操作（比如I/O）執行完畢前直接返回而不會等待耗時操作執行完畢。

The `async` and `await` keywords support asynchronous programming,
letting you write asynchronous code that
looks similar to synchronous code.

`async` 和 `await` 關鍵字用於實現非同步程式設計，並且讓你的程式碼看起來就像是同步的一樣。

<a id="await"></a>
### Handling Futures

### 處理 Future

When you need the result of a completed Future,
you have two options:

可以透過下面兩種方式，獲得 Future 執行完成的結果：

* Use `async` and `await`, as described here and in the
  [asynchronous programming codelab](/codelabs/async-await).

  使用 `async` 和 `await`，在
  [非同步程式設計 codelab](/codelabs/async-await)
  中有更多描述；

* Use the Future API, as described
  [in the library tour](/guides/libraries/library-tour#future).

  使用 Future API，具體描述參考 [函式庫概覽](/guides/libraries/library-tour#future)。

Code that uses `async` and `await` is asynchronous,
but it looks a lot like synchronous code.
For example, here's some code that uses `await`
to wait for the result of an asynchronous function:

使用 `async` 和 `await` 的程式碼是非同步的，但是看起來有點像同步程式碼。
例如，下面的程式碼使用 `await` 等待非同步函式的執行結果。

<?code-excerpt "misc/lib/language_tour/async.dart (await-lookUpVersion)"?>
```dart
await lookUpVersion();
```

To use `await`, code must be in an `async` function—a
function marked as `async`:

必須在帶有 async 關鍵字的 **非同步函式** 中使用 `await`：

<?code-excerpt "misc/lib/language_tour/async.dart (checkVersion)" replace="/async|await/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
Future<void> checkVersion() [!async!] {
  var version = [!await!] lookUpVersion();
  // Do something with version
}
{% endprettify %}

{{site.alert.note}}

  Although an `async` function might perform time-consuming operations, it
  doesn't wait for those operations. Instead, the `async` function executes only
  until it encounters its first `await` expression
  ([details][synchronous-async-start]). Then it returns a Future object,
  resuming execution only after the `await` expression completes.

  儘管非同步函式可以處理耗時操作，但是它並不會等待這些耗時操作完成，
  非同步函式執行時會在其遇到第一個 `await` 表示式
  （[程式碼行][synchronous-async-start]）時返回一個 Future 物件，
  然後等待 await 表示式執行完畢後繼續執行。

{{site.alert.end}}

Use `try`, `catch`, and `finally` to handle errors and cleanup in code that uses
`await`:

使用 `try`、`catch` 以及 `finally` 來處理使用 `await` 導致的例外：

<?code-excerpt "misc/lib/language_tour/async.dart (try-catch)"?>
```dart
try {
  version = await lookUpVersion();
} catch (e) {
  // React to inability to look up the version
}
```

You can use `await` multiple times in an `async` function.
For example, the following code waits three times
for the results of functions:

你可以在非同步函式中多次使用 `await` 關鍵字。
例如，下面程式碼中等待了三次函式結果：

<?code-excerpt "misc/lib/language_tour/async.dart (repeated-await)"?>
```dart
var entrypoint = await findEntryPoint();
var exitCode = await runExecutable(entrypoint, args);
await flushThenExit(exitCode);
```

In <code>await <em>expression</em></code>,
the value of <code><em>expression</em></code> is usually a Future;
if it isn't, then the value is automatically wrapped in a Future.
This Future object indicates a promise to return an object.
The value of <code>await <em>expression</em></code> is that returned object.
The await expression makes execution pause until that object is available.

<code>await <em>表示式的返回值通常是一個 Future 物件；
如果不是的話也會自動將其包裹在一個 Future 物件裡。
Future 物件代表一個“承諾”，
<code>await <em>表示式</em></code>會阻塞直到需要的物件返回。

**If you get a compile-time error when using `await`,
make sure `await` is in an `async` function.**
For example, to use `await` in your app's `main()` function,
the body of `main()` must be marked as `async`:

**如果在使用 `await` 時導致編譯錯誤，請確保 `await` 在一個非同步函式中使用**。
例如，如果想在 main() 函式中使用 `await`，
那麼 `main()` 函式就必須使用 `async` 關鍵字標識。

<?code-excerpt "misc/lib/language_tour/async.dart (main)" replace="/async|await/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
void main() [!async!] {
  checkVersion();
  print('In main: version is ${[!await!] lookUpVersion()}');
}
{% endprettify %}

{{site.alert.note}}

  The preceding example uses an `async` function (`checkVersion()`)
  without waiting for a result—a practice that can cause problems
  if the code assumes that the function has finished executing.
  To avoid this problem,
  use the [unawaited_futures linter rule][].

  如上的例子使用了宣告為 `async` 的函式 `checkVersion()`，但沒有等待其結果。
  在實際的開發中，如果程式碼假設函式已經執行完成，則可能導致一些非同步的問題。
  想要避免這些問題，請使用
  [unawaited_futures 提示規則][unawaited_futures linter rule]。

{{site.alert.end}}

[unawaited_futures linter rule]: /tools/linter-rules#unawaited_futures

For an interactive introduction to using futures, `async`, and `await`,
see the [asynchronous programming codelab](/codelabs/async-await).


<a id="async"></a>
### Declaring async functions

### 宣告非同步函式

An `async` function is a function whose body is marked with
the `async` modifier.

**非同步函式** 是函式體由 `async` 關鍵字標記的函式。

Adding the `async` keyword to a function makes it return a Future.
For example, consider this synchronous function,
which returns a String:

將關鍵字 `async` 新增到函式並讓其返回一個 Future 物件。
假設有如下返回 String 物件的方法：

<?code-excerpt "misc/lib/language_tour/async.dart (sync-lookUpVersion)"?>
```dart
String lookUpVersion() => '1.0.0';
```

If you change it to be an `async` function—for example,
because a future implementation will be time consuming—the
returned value is a Future:

將其改為非同步函式，返回值是 Future：

<?code-excerpt "misc/lib/language_tour/async.dart (async-lookUpVersion)"?>
```dart
Future<String> lookUpVersion() async => '1.0.0';
```

Note that the function's body doesn't need to use the Future API.
Dart creates the Future object if necessary.

注意，函式體不需要使用 Future API。如有必要，Dart 會建立 Future 物件。

If your function doesn't return a useful value,
make its return type `Future<void>`.

如果函式沒有返回有效值，需要設定其返回型別為 `Future<void>`。

For an interactive introduction to using futures, `async`, and `await`,
see the [asynchronous programming codelab](/codelabs/async-await).

關於 Future、`async` 和 `await` 的使用介紹，可以參見這個 codelab:
[asynchronous programming codelab](/codelabs/async-await)。

{% comment %}
TODO #1117: Where else should we cover generalized void?
{% endcomment %}


<a id="await-for"></a>
### Handling Streams

### 處理 Stream

When you need to get values from a Stream,
you have two options:

如果想從 Stream 中獲取值，可以有兩種選擇：

* Use `async` and an _asynchronous for loop_ (`await for`).

  使用 `async` 關鍵字和一個 **非同步迴圈**（使用 `await for` 關鍵字標識）。

* Use the Stream API, as described
  [in the library tour](/guides/libraries/library-tour#stream).

  使用 Stream API。詳情參考 [函式庫概覽](/guides/libraries/library-tour#stream)。

{{site.alert.note}}

  Before using `await for`, be sure that it makes the code clearer and that you
  really do want to wait for all of the stream's results. For example, you
  usually should **not** use `await for` for UI event listeners, because UI
  frameworks send endless streams of events.

  在使用 `await for` 關鍵字前，
  確保其可以令程式碼邏輯更加清晰並且是真的需要等待所有的結果執行完畢。
  例如，通常不應該在 UI 事件監聽器上使用 `await for` 關鍵字，
  因為 UI 框架發出的事件流是無窮盡的。

{{site.alert.end}}

An asynchronous for loop has the following form:

使用 await for 定義非同步迴圈看起來是這樣的：

<?code-excerpt "misc/lib/language_tour/async.dart (await-for)"?>
```dart
await for (varOrType identifier in expression) {
  // Executes each time the stream emits a value.
}
```

The value of <code><em>expression</em></code> must have type Stream.
Execution proceeds as follows:

<code><em>表示式</em></code> 的型別必須是 Stream。執行流程如下：

1. Wait until the stream emits a value.

   等待直到 Stream 返回一個數據。

2. Execute the body of the for loop,
   with the variable set to that emitted value.

   使用 1 中 Stream 返回的資料執行迴圈體。

3. Repeat 1 and 2 until the stream is closed.

   重複 1、2 過程直到 Stream 資料返回完畢。

To stop listening to the stream,
you can use a `break` or `return` statement,
which breaks out of the for loop
and unsubscribes from the stream.

使用 `break` 和 `return` 陳述式可以停止接收 Stream 資料，這樣就跳出了迴圈並取消註冊監聽 Stream。

**If you get a compile-time error when implementing an asynchronous for loop,
make sure the `await for` is in an `async` function.**
For example, to use an asynchronous for loop in your app's `main()` function,
the body of `main()` must be marked as `async`:

**如果在實現非同步 for迴圈時遇到編譯時錯誤，請檢查確保 `await for` 處於非同步函式中。
** 例如，要在應用程式的 `main()` 函式中使用非同步 for迴圈，`main()` 函式體必須標記為 `async`：

<?code-excerpt "misc/lib/language_tour/async.dart (number_thinker)" replace="/async|await for/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
void main() [!async!] {
  // ...
  [!await for!] (final request in requestServer) {
    handleRequest(request);
  }
  // ...
}
{% endprettify %}

For more information about asynchronous programming, in general, see the
[dart:async](/guides/libraries/library-tour#dartasync---asynchronous-programming)
section of the library tour.

你可以查閱函式庫概覽中有關 [dart:async](/guides/libraries/library-tour#dartasync---asynchronous-programming) 的部分獲取更多有關非同步程式設計的資訊。


<a id="generator"></a>
## Generators

## 產生器

When you need to lazily produce a sequence of values,
consider using a _generator function_.
Dart has built-in support for two kinds of generator functions:

當你需要延遲地產生一連串的值時，可以考慮使用 _產生器函式_。Dart 內建支援兩種形式的產生器方法：

* **Synchronous** generator: Returns an **[`Iterable`]** object.

  **同步** 產生器：返回一個 **[`Iterable`]** 物件。

* **Asynchronous** generator: Returns a **[`Stream`]** object.

  **非同步** 產生器：返回一個 **[`Stream`]** 物件。

To implement a **synchronous** generator function,
mark the function body as `sync*`,
and use `yield` statements to deliver values:

透過在函式上加 `sync*` 關鍵字並將返回值型別設定為 Iterable 來實現一個 **同步** 產生器函式，
在函式中使用 `yield` 陳述式來傳遞值：

<?code-excerpt "misc/test/language_tour/async_test.dart (sync-generator)"?>
```dart
Iterable<int> naturalsTo(int n) sync* {
  int k = 0;
  while (k < n) yield k++;
}
```

To implement an **asynchronous** generator function,
mark the function body as `async*`,
and use `yield` statements to deliver values:

實現 **非同步** 產生器函式與同步類似，
只不過關鍵字為 `async*` 並且返回值為 Stream：

<?code-excerpt "misc/test/language_tour/async_test.dart (async-generator)"?>
```dart
Stream<int> asynchronousNaturalsTo(int n) async* {
  int k = 0;
  while (k < n) yield k++;
}
```

If your generator is recursive,
you can improve its performance by using `yield*`:

如果產生器是遞迴呼叫的，可是使用 `yield*` 陳述式提升執行效能：

<?code-excerpt "misc/test/language_tour/async_test.dart (recursive-generator)"?>
```dart
Iterable<int> naturalsDownFrom(int n) sync* {
  if (n > 0) {
    yield n;
    yield* naturalsDownFrom(n - 1);
  }
}
```

## Callable classes

## 可呼叫類

To allow an instance of your Dart class to be called like a function,
implement the `call()` method.

透過實現類別的 `call()` 方法，允許使用類似函式呼叫的方式來使用該類別的例項。

The `call()` method allows any class that defines it to emulate a function.
This method supports the same functionality as normal [functions](#functions)
such as parameters and return types.

所有的類都可以定義並模擬 `call()` 方法，這個方法與普通 [函式](#functions) 是一樣的，
支援傳參和定義返回型別等。

In the following example, the `WannabeFunction` class defines a `call()` function
that takes three strings and concatenates them, separating each with a space,
and appending an exclamation. Click **Run** to execute the code.

在下面的範例中，`WannabeFunction` 類定義了一個 `call()` 函式，
函式接受三個字串引數，函式體將三個字串拼接，字串間用空格分割，
並在結尾附加了一個感嘆號。單擊執行按鈕執行程式碼。

<?code-excerpt "misc/lib/language_tour/callable_classes.dart"?>
```dart:run-dartpad:height-350px:ga_id-callable_classes
class WannabeFunction {
  String call(String a, String b, String c) => '$a $b $c!';
}

var wf = WannabeFunction();
var out = wf('Hi', 'there,', 'gang');

void main() => print(out);
```

## Isolates

## 隔離區

Most computers, even on mobile platforms, have multi-core CPUs.
To take advantage of all those cores, developers traditionally use
shared-memory threads running concurrently. However, shared-state
concurrency is error prone and can lead to complicated code.

大多數計算機中，甚至在移動平臺上，都在使用多核 CPU。為了有效利用多核效能，開發者一般使用共享記憶體的方式讓執行緒併發地執行。然而，多執行緒共享資料通常會導致很多潛在的問題，並導致程式碼執行出錯。

Instead of threads, all Dart code runs inside of *isolates*. 
Each Dart isolate has a single thread of execution and
shares no mutable objects with other isolates.  

為了解決多執行緒帶來的併發問題，Dart 使用 isolate 替代執行緒，所有的 Dart 程式碼均執行在一個 **isolate** 中。每一個 isolate 有它自己的堆記憶體以確保其狀態不被其它 isolate 存取。

所有的 Dart 程式碼都是在一個 isolate 中執行，而非執行緒。
每個 isolate 都有一個單獨的執行執行緒，並且不與其他的 isolate 共享任何可變物件。

For more information, see the following:

你可以查閱下面的文件獲取更多相關資訊：
* [Concurrency in Dart](/guides/language/concurrency)
  
  [Dart 中的併發特性](/guides/language/concurrency)

* [dart:isolate API reference,][dart:isolate]
  including [Isolate.spawn()][] and
  [TransferableTypedData][]

  [dart:isolate API 參考][dart:isolate] 介紹了 [Isolate.spawn()][] 和 [TransferableTypedData][] 的用法

* [Background parsing][background json] cookbook on the Flutter site

   Flutter 文件上關於 [後臺解析][background json] 的實用課程

* [Isolate sample app][]

[Isolate.spawn()]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-isolate/Isolate/spawn.html
[TransferableTypedData]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-isolate/TransferableTypedData-class.html
[background json]: {{site.flutter-docs}}/cookbook/networking/background-parsing
[Isolate sample app]: https://github.com/flutter/samples/tree/main/isolate_example

## Typedefs

A type alias—often called a _typedef_ because
it's declared with the keyword `typedef`—is
a concise way to refer to a type.
Here's an example of declaring and using a type alias named `IntList`:
  
類型別名是參考某一型別的簡便方法，因為其使用關鍵字 `typedef`，因此通常被稱作 **typedef**。下面是一個使用 `IntList` 來宣告和使用類型別名的例子:

<?code-excerpt "misc/lib/language_tour/typedefs/misc.dart (int-list)"?>
```dart
typedef IntList = List<int>;
IntList il = [1, 2, 3];
```

A type alias can have type parameters:

類型別名可以有型別引數:

<?code-excerpt "misc/lib/language_tour/typedefs/misc.dart (list-mapper)"?>
```dart
typedef ListMapper<X> = Map<X, List<X>>;
Map<String, List<String>> m1 = {}; // Verbose.
ListMapper<String> m2 = {}; // Same thing but shorter and clearer.
```

{{site.alert.version-note}}
  Before 2.13, typedefs were restricted to function types.
  Using the new typedefs requires a [language version][] of at least 2.13.
{{site.alert.end}}

We recommend using [inline function types][] instead of typedefs for functions,
in most situations.
However, function typedefs can still be useful:

針對函式，在大多數情況下，我們推薦使用 [行內函數型別][inline function types]
替代 typedefs。然而，函式的 typedefs 仍然是有用的:

<?code-excerpt "misc/lib/language_tour/typedefs/misc.dart (compare)"?>
```dart
typedef Compare<T> = int Function(T a, T b);

int sort(int a, int b) => a - b;

void main() {
  assert(sort is Compare<int>); // True!
}
```

[typedef-functions]: /guides/language/effective-dart/design#dont-use-the-legacy-typedef-syntax
[inline function types]: /guides/language/effective-dart/design#prefer-inline-function-types-over-typedefs


## Metadata

## 元資料

Use metadata to give additional information about your code. A metadata
annotation begins with the character `@`, followed by either a reference
to a compile-time constant (such as `deprecated`) or a call to a
constant constructor.

使用元資料可以為程式碼增加一些額外的資訊。元資料註解以 `@` 開頭，
其後緊跟一個編譯時常量（比如 `deprecated`）或者呼叫一個常量建構函式。

Three annotations are available to all Dart code: 
`@Deprecated`, `@deprecated`, and `@override`. 
For examples of using `@override`,
see [Extending a class](#extending-a-class).
Here’s an example of using the `@Deprecated` annotation:

Dart 中有兩個註解是所有程式碼都可以使用的：
`@deprecated`、`@Deprecated` 和 `@override`。
你可以查閱 [擴充一個類別](#extending-a-class) 獲取有關 `@override` 的使用範例。
下面是使用 `@deprecated` 的範例：

<?code-excerpt "misc/lib/language_tour/metadata/television.dart (deprecated)" replace="/@Deprecated.*/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
class Television {
  /// Use [turnOn] to turn the power on instead.
  [!@Deprecated('Use turnOn instead')!]
  void activate() {
    turnOn();
  }

  /// Turns the TV's power on.
  void turnOn() {...}
  // ···
}
{% endprettify %}

You can define your own metadata annotations. Here’s an example of
defining a `@Todo` annotation that takes two arguments:

可以自訂元資料註解。下面的範例定義了一個帶有兩個引數的 @todo 註解：

<?code-excerpt "misc/lib/language_tour/metadata/todo.dart"?>
```dart
class Todo {
  final String who;
  final String what;

  const Todo(this.who, this.what);
}
```

And here’s an example of using that `@Todo` annotation:

使用 @Todo 註解的範例：

<?code-excerpt "misc/lib/language_tour/metadata/misc.dart"?>
```dart
@Todo('Dash', 'Implement this function')
void doSomething() {
  print('Do something');
}
```

Metadata can appear before a library, class, typedef, type parameter,
constructor, factory, function, field, parameter, or variable
declaration and before an import or export directive. You can
retrieve metadata at runtime using reflection.

元資料可以在 library、class、typedef、type parameter、
constructor、factory、function、field、parameter
或者 variable 宣告之前使用，
也可以在 import 或 export 之前使用。
可使用反射在執行時獲取元資料資訊。


## Comments

## 註釋

Dart supports single-line comments, multi-line comments, and
documentation comments.

Dart 支援單行註釋、多行註釋和文件註釋。

### Single-line comments

### 單行註釋

A single-line comment begins with `//`. Everything between `//` and the
end of line is ignored by the Dart compiler.

單行註釋以 `//` 開始。所有在 `//` 和該行結尾之間的內容均被編譯器忽略。

<?code-excerpt "misc/lib/language_tour/comments.dart (single-line-comments)"?>
```dart
void main() {
  // TODO: refactor into an AbstractLlamaGreetingFactory?
  print('Welcome to my Llama farm!');
}
```


### Multi-line comments

### 多行註釋

A multi-line comment begins with `/*` and ends with `*/`. Everything
between `/*` and `*/` is ignored by the Dart compiler (unless the
comment is a documentation comment; see the next section). Multi-line
comments can nest.

多行註釋以 `/*` 開始， 以 `*/` 結尾。所有在 `/*` 和 `*/`
之間的內容均被編譯器忽略（不會忽略文件註釋），
多行註釋可以巢狀(Nesting)。

<?code-excerpt "misc/lib/language_tour/comments.dart (multi-line-comments)"?>
```dart
void main() {
  /*
   * This is a lot of work. Consider raising chickens.

  Llama larry = Llama();
  larry.feed();
  larry.exercise();
  larry.clean();
   */
}
```


### Documentation comments

### 文件註釋

Documentation comments are multi-line or single-line comments that begin
with `///` or `/**`. Using `///` on consecutive lines has the same
effect as a multi-line doc comment.

文件註釋可以是多行註釋，也可以是單行註釋，
文件註釋以 `///` 或者 `/**` 開始。
在連續行上使用 `///` 與多行文件註釋具有相同的效果。

Inside a documentation comment, the analyzer ignores all text
unless it is enclosed in brackets. Using brackets, you can refer to
classes, methods, fields, top-level variables, functions, and
parameters. The names in brackets are resolved in the lexical scope of
the documented program element.

在文件註釋中，除非用中括號括起來，否則分析器會忽略所有文字。
使用中括號可以參考類、方法、欄位、最上層變數、函式和引數。
括號中的符號會在已記錄的程式元素的詞法域中進行解析。

Here is an example of documentation comments with references to other
classes and arguments:

下面是一個參考其他類和成員的文件註釋：

<?code-excerpt "misc/lib/language_tour/comments.dart (doc-comments)"?>
```dart
/// A domesticated South American camelid (Lama glama).
///
/// Andean cultures have used llamas as meat and pack
/// animals since pre-Hispanic times.
///
/// Just like any other animal, llamas need to eat,
/// so don't forget to [feed] them some [Food].
class Llama {
  String? name;

  /// Feeds your llama [food].
  ///
  /// The typical llama eats one bale of hay per week.
  void feed(Food food) {
    // ...
  }

  /// Exercises your llama with an [activity] for
  /// [timeLimit] minutes.
  void exercise(Activity activity, int timeLimit) {
    // ...
  }
}
```

In the class's generated documentation, `[feed]` becomes a link
to the docs for the `feed` method,
and `[Food]` becomes a link to the docs for the `Food` class.

在產生的文件中，`[feed]` 會成為一個連結，指向 `feed` 方法的文件，
`[Food]` 會成為一個連結，指向 `Food` 類別的 API 文件。

To parse Dart code and generate HTML documentation, you can use Dart's
documentation generation tool, [`dart doc`](/tools/dart-doc).
For an example of generated documentation, see the 
[Dart API documentation.]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}) 
For advice on how to structure your comments, see
[Effective Dart: Documentation.](/guides/language/effective-dart/documentation)

解析 Dart 程式碼並產生 HTML 文件，
可以使用 Dart 的文件產生工具 [`dart doc`](/tools/dartdoc)。
關於產生文件的範例，請參考
[Dart API documentation]({{site.dart_api}}/{{site.data.pkg-vers.SDK.channel}})
檢視關於文件結構的建議，
請參考文件：
[Guidelines for Dart Doc Comments.](/guides/language/effective-dart/documentation)。


## Summary

## 總結

This page summarized the commonly used features in the Dart language.
More features are being implemented, but we expect that they won’t break
existing code. For more information, see the [Dart language specification][] and
[Effective Dart](/guides/language/effective-dart).

本頁概述了 Dart 語言中常用的功能。還有更多特性有待實現，
但我們希望它們不會破壞現有程式碼。有關更多資訊，
請參考 [Dart 語言規範][Dart language specification] 和
[高效 Dart 語言指南](/guides/language/effective-dart)。

To learn more about Dart's core libraries, see
[A Tour of the Dart Libraries](/guides/libraries/library-tour).

要了解更多關於 Dart 核心函式庫的內容，
請參考 [Dart 核心函式庫概覽](/guides/libraries/library-tour)。

[`AssertionError`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/AssertionError-class.html
[`Characters`]: {{site.pub-api}}/characters/latest/characters/Characters-class.html
[characters API]: {{site.pub-api}}/characters
[characters example]: {{site.pub-pkg}}/characters/example
[characters package]: {{site.pub-pkg}}/characters
[`dart run`]: /tools/dart-run
[dart:html]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-html
[dart:isolate]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-isolate
[dart:math]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-math
[Dart language specification]: /guides/language/spec
[dartdevc]: /tools/dartdevc
[DON’T use const redundantly]: /guides/language/effective-dart/usage#dont-use-const-redundantly
[`double`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/double-class.html
[`Enum`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Enum-class.html
[`Error`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Error-class.html
[`Exception`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Exception-class.html
[extension methods page]: /guides/language/extension-methods
[Flutter]: {{site.flutter}}
[Flutter debug mode]: {{site.flutter-docs}}/testing/debugging#debug-mode-assertions
[forEach()]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Iterable/forEach.html
[Function API reference]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Function-class.html
[`Future`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-async/Future-class.html
[grapheme clusters]: https://unicode.org/reports/tr29/#Grapheme_Cluster_Boundaries
[identical()]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/identical.html
[`int`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/int-class.html
[`Iterable`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Iterable-class.html
[iteration]: /guides/libraries/library-tour#iteration
[js numbers]: https://stackoverflow.com/questions/2802957/number-of-bits-in-javascript-numbers/2803010#2803010
[language version]: /guides/language/evolution#language-versioning
[late-final-ivar]: /guides/language/effective-dart/design#avoid-public-late-final-fields-without-initializers
[`List`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/List-class.html
[`Map`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Map-class.html
[meta]: {{site.pub-pkg}}/meta
[ns]: /null-safety
[ns-enable]: /null-safety#enable-null-safety
[`num`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/num-class.html
[`Object`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Object-class.html
[ObjectVsDynamic]: /guides/language/effective-dart/design#avoid-using-dynamic-unless-you-want-to-disable-static-checking
[runes]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Runes-class.html
[`Set`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Set-class.html
[`StackTrace`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/StackTrace-class.html
[`Stream`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-async/Stream-class.html
[`String`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/String-class.html
[`Symbol`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Symbol-class.html
[synchronous-async-start]: https://github.com/dart-lang/language/blob/master/archive/newsletter/20170915.md#synchronous-async-start
[top-and-bottom]: /null-safety/understanding-null-safety#top-and-bottom
[trailing commas]: #trailing-comma
[type test operator]: #type-test-operators
[`Type`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Type-class.html
[Understanding null safety]: /null-safety/understanding-null-safety
