---
title: The Dart type system
title: Dart 語言裡的型別體系
description: Why and how to write sound Dart code.
description: 如何寫出優雅的 Dart 程式碼。
---
<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /([A-Z]\w*)\d\b/$1/g; /\b(main)\d\b/$1/g; /(^|\n) *\/\/\s+ignore:[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore:[^\n]+\n/$1\n/g"?>
<?code-excerpt path-base="type_system"?>

The Dart language is type safe: it uses a combination of static type checking
and [runtime checks](#runtime-checks) to
ensure that a variable's value always matches the variable's static type,
sometimes referred to as sound typing.
Although _types_ are mandatory, type _annotations_ are optional
because of [type inference](#type-inference).

Dart 是型別安全的程式語言：Dart 使用靜態型別檢查和
[執行時期檢查](#runtime-checks)
的組合來確保變數的值始終與變數的靜態型別或其他安全型別相匹配。儘管型別是必需的，但由於
[型別推斷](#type-inference)，型別的註釋是可選的。

For a full introduction to the Dart language, including types, see the
[language tour](/guides/language/language-tour).

有關 Dart 語言的完整介紹（包括型別），請參閱[語言概覽](/guides/language/language-tour)。

One benefit of static type checking is the ability to find bugs
at compile time using Dart's [static analyzer.][analysis]

靜態型別檢查的一個好處是能夠使用 Dart 的
[靜態分析器][analysis] 在編譯時找到錯誤。

You can fix most static analysis errors by adding type annotations to generic
classes. The most common generic classes are the collection types
`List<T>` and `Map<K,V>`.

可以向泛型類新增型別註釋來修復大多數靜態分析錯誤。
最常見的泛型類是集合型別 `List<T>` 和 `Map<K,V>` 。

For example, in the following code the `printInts()` function prints an integer list,
and `main()` creates a list and passes it to `printInts()`.

例如，在下面的程式碼中，`main()` 建立一個列表並將其傳遞給 `printInts()`，
由 `printInts()` 函式列印這個整數列表。

{:.fails-sa}
<?code-excerpt "lib/strong_analysis.dart (opening-example)" replace="/list(?=\))/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
void printInts(List<int> a) => print(a);

void main() {
  final list = [];
  list.add(1);
  list.add('2');
  printInts([!list!]);
}
{% endprettify %}

The preceding code results in a type error on `list` (highlighted
above) at the call of `printInts(list)`:

上面的程式碼在呼叫 `printInts(list)` 時會在 `list` （高亮提示）上產生型別錯誤：

{:.console-output}
<?code-excerpt "analyzer-results-stable.txt" retain="/strong_analysis.*List.*argument_type_not_assignable/" replace="/-(.*?):(.*?):(.*?)-/-/g; /. • (lib|test)\/\w+\.dart:\d+:\d+//g"?>
```nocode
error - The argument type 'List<dynamic>' can't be assigned to the parameter type 'List<int>'. - argument_type_not_assignable
```

The error highlights an unsound implicit cast from `List<dynamic>` to `List<int>`.
The `list` variable has static type `List<dynamic>`. This is because the
initializing declaration `var list = []` doesn't provide the analyzer with
enough information for it to infer a type argument more specific than `dynamic`.
The `printInts()` function expects a parameter of type `List<int>`,
causing a mismatch of types.

高亮錯誤是因為產生了從 `List<dynamic>` 到 `List<int>` 的不正確的隱含轉換。
`list` 變數是 `List<dynamic>` 靜態型別。這是因為 `list` 變數的初始化宣告 `var list = []`
沒有為分析器提供足夠的資訊來推斷比 `dynamic` 更具體的型別引數。
`printInts()` 函式需要 `List<int>` 型別的引數，因此導致型別不匹配。

When adding a type annotation (`<int>`) on creation of the list
(highlighted below) the analyzer complains that
a string argument can't be assigned to an `int` parameter. 
Removing the quotes in `list.add('2')` results in code
that passes static analysis and runs with no errors or warnings.

在建立 `list` 時新增型別註釋 `<int>`（程式碼中高亮顯示部分）後，分析器會提示無法將字串引數分配給 `int` 引數。
刪除 `list.add("2")` 中的字串引號使程式碼透過靜態分析並能夠正常執行。

{:.passes-sa}
<?code-excerpt "test/strong_test.dart (opening-example)" replace="/<int.(?=\[)|2/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
void printInts(List<int> a) => print(a);

void main() {
  final list = [!<int>!][];
  list.add(1);
  list.add([!2!]);
  printInts(list);
}
{% endprettify %}

[Try it in DartPad]({{site.dartpad}}/25074a51a00c71b4b000f33b688dedd0).

[嘗試在 DartPad 中練習]({{site.dartpad}}/f64e963cb5f894e2146c2b28d5efa4ed).

## What is soundness?

## 什麼是型別安全

*Soundness* is about ensuring your program can't get into certain
invalid states. A sound *type system* means you can never get into
a state where an expression evaluates to a value that doesn't match
the expression's static type. For example, if an expression's static
type is `String`, at runtime you are guaranteed to only get a string
when you evaluate it.

型別安全是為了確保程式不會進入某些無效狀態。
安全的型別系統意味著程式永遠不會進入表示式求值為與表示式的靜態型別不匹配的值的狀態。
例如，如果表示式的靜態型別是 `String` ，則在執行時保證在評估它的時候只會獲取字串。

Dart's type system, like the type systems in Java and C#, is sound. It
enforces that soundness using a combination of static checking
(compile-time errors) and runtime checks. For example, assigning a `String`
to `int` is a compile-time error. Casting an object to a `String` using
`as String` fails with a runtime error if the object isn't a `String`.

Dart 的型別系統，同 Java 和 C＃ 中的型別系統類似，是安全的。
它使用靜態檢查（編譯時錯誤）和執行時期檢查的組合來強制執行型別安全。
例如，將 `String` 分配給 `int` 是一個編譯時錯誤。如果`物件`不是字串，
使用 `as String` 將`物件`轉換為字串時，會由於執行時錯誤而導致轉換失敗。


## The benefits of soundness

## 型別安全的好處

A sound type system has several benefits:

安全的型別系統有以下幾個好處：

* Revealing type-related bugs at compile time.<br>
  A sound type system forces code to be unambiguous about its types,
  so type-related bugs that might be tricky to find at runtime are
  revealed at compile time.

  在編譯時就可以檢查並顯示型別相關的錯誤。<br>
  安全的型別系統強制要求程式碼明確型別，
  因此在編譯時會顯示與型別相關的錯誤，
  這些錯誤可能在執行時可能很難發現。

* More readable code.<br>
  Code is easier to read because you can rely on a value actually having
  the specified type. In sound Dart, types can't lie.

  程式碼更容易閱讀。<br>
  程式碼更容易閱讀，因為我們信賴一個擁有指定型別的值。
  在型別安全的 Dart 中，型別是不會騙人的。 因為一個擁有指定型別的值是可以被信賴的。

* More maintainable code.<br>
  With a sound type system, when you change one piece of code, the
  type system can warn you about the other pieces
  of code that just broke.

  程式碼可維護性更高。<br>
  在安全的型別系統下，當更改一處程式碼後，
  型別系統會警告因此影響到的其他程式碼塊。

* Better ahead of time (AOT) compilation.<br>
  While AOT compilation is possible without types, the generated
  code is much less efficient.

  更好的 AOT 編譯。<br>
  雖然在沒有型別的情況下可以進行 AOT 編譯，
  但產生的程式碼效率要低很多。


## Tips for passing static analysis

## 靜態檢查中的一些技巧

Most of the rules for static types are easy to understand.
Here are some of the less obvious rules:

大多數靜態型別的規則都很容易理解。
下面是一些不太明顯的規則：

* Use sound return types when overriding methods.

  重寫方法時，使用型別安全返回值。

* Use sound parameter types when overriding methods.

  重寫方法時，使用型別安全的引數。

* Don't use a dynamic list as a typed list.

  不要將動態型別的 List 看做是有型別的 List。

Let's see these rules in detail, with examples that use the following
type hierarchy:

讓我們透過下面範例的型別結構，來更深入的瞭解這些規則：

<img src="images/type-hierarchy.png" alt="a hierarchy of animals where the supertype is Animal and the subtypes are Alligator, Cat, and HoneyBadger. Cat has the subtypes of Lion and MaineCoon">

<a name="use-proper-return-types"></a>
### Use sound return types when overriding methods

### 重寫方法時，使用型別安全的返回值

The return type of a method in a subclass must be the same type or a
subtype of the return type of the method in the superclass. 
Consider the getter method in the `Animal` class:

子類別方法中返回值型別必須與父類方法中返回值型別的型別相同或其子類別型。
考慮 Animal 類中的 Getter 方法：

<?code-excerpt "lib/animal.dart (Animal)" replace="/Animal get.*/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
class Animal {
  void chase(Animal a) { ... }
  [!Animal get parent => ...!]
}
{% endprettify %}

The `parent` getter method returns an `Animal`. In the `HoneyBadger` subclass,
you can replace the getter's return type with `HoneyBadger` 
(or any other subtype of `Animal`), but an unrelated type is not allowed.

`父類` Getter 方法返回一個 Animal 。在 HoneyBadger 子類別中，
可以使用 HoneyBadger（或 Animal 的任何其他子類別型）替換 Getter 的返回值型別，
但不允許使用其他的無關型別。

{:.passes-sa}
<?code-excerpt "lib/animal.dart (HoneyBadger)" replace="/(\w+)(?= get)/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
class HoneyBadger extends Animal {
  @override
  void chase(Animal a) { ... }

  @override
  [!HoneyBadger!] get parent => ...
}
{% endprettify %}

{:.fails-sa}
{% prettify dart tag=pre+code %}
class HoneyBadger extends Animal {
  @override
  void chase(Animal a) { ... }
  
  @override
  [!Root!] get parent => ...
}
{% endprettify %}

<a name="use-proper-param-types"></a>
### Use sound parameter types when overriding methods

### 重寫方法時，使用型別安全的引數。

The parameter of an overridden method must have either the same type
or a supertype of the corresponding parameter in the superclass.
Don't "tighten" the parameter type by replacing the type with a
subtype of the original parameter.

子類別方法的引數必須與父類方法中引數的型別相同或是其引數的父型別。
不要使用原始引數的子類別型，替換原有型別，這樣會導致引數型別"收緊"。

{{site.alert.note}}

  If you have a valid reason to use a subtype, you can use the
  [`covariant` keyword](/guides/language/sound-problems#the-covariant-keyword).
  
  **提示：** 如果有合理的理由使用子類別型，可以使用 [`covariant` 關鍵字](/guides/language/sound-problems#the-covariant-keyword)。
{{site.alert.end}}

Consider the `chase(Animal)` method for the `Animal` class:

考慮 Animal 的 `chase(Animal)` 方法：

<?code-excerpt "lib/animal.dart (Animal)" replace="/void chase.*/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
class Animal {
  [!void chase(Animal a) { ... }!]
  Animal get parent => ...
}
{% endprettify %}

The `chase()` method takes an `Animal`. A `HoneyBadger` chases anything.
It's OK to override the `chase()` method to take anything (`Object`).

`chase()` 方法的引數型別是 Animal 。一個 HoneyBadger 可以追逐任何東西。
因此可以在重寫 `chase()` 方法時將引數型別指定為任意類別型  (Object)  。

{:.passes-sa}
<?code-excerpt "lib/animal.dart (chase-Object)" replace="/Object/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
class HoneyBadger extends Animal {
  @override
  void chase([!Object!] a) { ... }

  @override
  Animal get parent => ...
}
{% endprettify %}

The following code tightens the parameter on the `chase()` method
from `Animal` to `Mouse`, a subclass of `Animal`.

Mouse 是 Animal 的子類別，下面的程式碼將 `chase()`
方法中引數的範圍從 Animal 縮小到 Mouse 。

{:.fails-sa}
{% prettify dart tag=pre+code %}
class Mouse extends Animal {...}

class Cat extends Animal {
  @override
  void chase([!Mouse!] x) { ... }
}
{% endprettify %}

This code is not type safe because it would then be possible to define
a cat and send it after an alligator:

下面的程式碼不是型別安全的，因為 a 可以是一個 cat 物件，
卻可以給它傳入一個 alligator 物件。

{% prettify dart tag=pre+code %}
Animal a = Cat();
a.chase([!Alligator!]()); // Not type safe or feline safe.
{% endprettify %}

### Don't use a dynamic list as a typed list

### 不要將動態型別的 List 看做是有型別的 List 

A `dynamic` list is good when you want to have a list with
different kinds of things in it. However, you can't use a
`dynamic` list as a typed list.

當期望在一個 List 中可以包含不同型別的物件時，動態列表是很好的選擇。
但是不能將動態型別的 List 看做是有型別的 List 。

This rule also applies to instances of generic types.

這個規則也適用於泛型型別的例項。

The following code creates a `dynamic` list of `Dog`, and assigns it to
a list of type `Cat`, which generates an error during static analysis.

下面程式碼建立一個 `Dog` 的動態 List ，並將其分配給 `Cat` 型別的 List ，
表示式在靜態分析期間會產生錯誤。

{:.fails-sa}
{% prettify dart tag=pre+code %}
class Cat extends Animal { ... }

class Dog extends Animal { ... }

void main() {
  List<Cat> foo = [!<dynamic>!][Dog()]; // Error
  List<dynamic> bar = <dynamic>[Dog(), Cat()]; // OK
}
{% endprettify %}

## Runtime checks

## 執行時期檢查

Runtime checks deal with type safety issues
that can't be detected at compile time.

執行時期檢查工具會處理分析器無法捕獲的型別安全問題。

For example, the following code throws an exception at runtime
because it's an error to cast a list of dogs to a list of cats:

例如，以下程式碼在執行時會丟擲例外，
因為將 Dog 型別的 List 賦值給 Cat 型別的 List 是錯誤的：

{:.runtime-fail}
<?code-excerpt "test/strong_test.dart (runtime-checks)" replace="/animals as[^;]*/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
void main() {
  List<Animal> animals = [Dog()];
  List<Cat> cats = [!animals as List<Cat>!];
}
{% endprettify %}


## Type inference

## 型別推斷

The analyzer can infer types for fields, methods, local variables,
and most generic type arguments.
When the analyzer doesn't have enough information to infer
a specific type, it uses the `dynamic` type.

分析器 (analyzer) 可以推斷欄位，方法，區域變數和大多數泛型型別引數的型別。
當分析器沒有足夠的資訊來推斷出一個特定型別時，會使用 `dynamic` 作為型別。

Here's an example of how type inference works with generics.
In this example, a variable named `arguments` holds a map that
pairs string keys with values of various types.

下面是在泛型中如何進行型別推斷的範例。在此範例中，名為 `arguments` 的變數包含一個 Map ，
該 Map 將字串鍵與各種型別的值配對。

If you explicitly type the variable, you might write this:

如果顯式鍵入變數，則可以這樣寫：

<?code-excerpt "lib/strong_analysis.dart (type-inference-1-orig)" replace="/Map<String, dynamic\x3E/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
[!Map<String, dynamic>!] arguments = {'argA': 'hello', 'argB': 42};
{% endprettify %}

Alternatively, you can use `var` or `final` and let Dart infer the type:

或者，使用 `var` 讓 Dart 來推斷型別：

<?code-excerpt "lib/strong_analysis.dart (type-inference-1)" replace="/var/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
[!var!] arguments = {'argA': 'hello', 'argB': 42}; // Map<String, Object>
{% endprettify %}

The map literal infers its type from its entries,
and then the variable infers its type from the map literal's type.
In this map, the keys are both strings, but the values have different
types (`String` and `int`, which have the upper bound `Object`).
So the map literal has the type `Map<String, Object>`,
and so does the `arguments` variable.

Map 字面量從其條目中推斷出它的型別，然後變數從 Map 字面量的型別中推斷出它的型別。
在此 Map 中，鍵都是字串，但值具有不同的型別（ String 和 int ，它們具有共同的上限型別 Object ）。
因此，Map 字面量的型別為 `Map<String, Object>` ，也就是 `arguments` 的型別。

### Field and method inference

### 欄位和方法推斷

A field or method that has no specified type and that overrides
a field or method from the superclass, inherits the type of the
superclass method or field.

重寫父類別的且沒有指定型別的欄位或方法，繼承父類中欄位或方法的型別。

A field that does not have a declared or inherited type but that is declared
with an initial value, gets an inferred type based on the initial value.

沒有宣告型別且不存在繼承型別的欄位，如果在宣告時被初始化，
那麼欄位的型別為初始化值的型別。

### Static field inference

### 靜態欄位推斷

Static fields and variables get their types inferred from their
initializer. Note that inference fails if it encounters a cycle
(that is, inferring a type for the variable depends on knowing the
type of that variable).

靜態欄位和變數的型別從其初始化程式中推斷獲得。 
需要注意的是，如果推斷是個迴圈，推斷會失敗
（也就是說，推斷變數的型別取決於知道該變數的型別）。

### Local variable inference

### 區域變數推斷

Local variable types are inferred from their initializer, if any.
Subsequent assignments are not taken into account.
This may mean that too precise a type may be inferred.
If so, you can add a type annotation.

在不考慮連續賦值的情況下，區域變數如果有初始化值的情況下，其型別是從初始化值推斷出來的。
這可能意味著推斷出來的型別會非常嚴格。
如果是這樣，可以為他們新增型別註釋。

{:.fails-sa}
<?code-excerpt "lib/strong_analysis.dart (local-var-type-inference-error)"?>
{% prettify dart tag=pre+code %}
var x = 3; // x is inferred as an int.
x = 4.0;
{% endprettify %}

{:.passes-sa}
<?code-excerpt "lib/strong_analysis.dart (local-var-type-inference-ok)"?>
{% prettify dart tag=pre+code %}
num y = 3; // A num can be double or int.
y = 4.0;
{% endprettify %}

### Type argument inference

### 引數型別推斷

Type arguments to constructor calls and
[generic method](/guides/language/language-tour#using-generic-methods) invocations
are inferred based on a combination of downward information from the context
of occurrence, and upward information from the arguments to the constructor
or generic method. If inference is not doing what you want or expect,
you can always explicitly specify the type arguments.

建構函式呼叫的型別引數和
[泛型方法](/guides/language/language-tour#using-generic-methods)呼叫
是根據上下文的向下資訊和建構函式或泛型方法的引數的向上資訊組合推斷的。
如果推斷沒有按照意願或期望進行，那麼你可以顯式的指定他們的引數型別。

{:.passes-sa}
<?code-excerpt "lib/strong_analysis.dart (type-arg-inference)"?>
{% prettify dart tag=pre+code %}
// Inferred as if you wrote <int>[].
List<int> listOfInt = [];

// Inferred as if you wrote <double>[3.0].
var listOfDouble = [3.0];

// Inferred as Iterable<int>.
var ints = listOfDouble.map((x) => x.toInt());
{% endprettify %}

In the last example, `x` is inferred as `double` using downward information.
The return type of the closure is inferred as `int` using upward information.
Dart uses this return type as upward information when inferring the `map()`
method's type argument: `<int>`.

在最後一個範例中，根據向下資訊 `x` 被推斷為 `double` 。 閉套件的返回型別根據向上資訊推斷為 `int` 。
在推斷 `map()` 方法的型別引數：`<int>` 時，Dart 使用此返回值的型別作為向上資訊。


## Substituting types

## 替換型別

When you override a method, you are replacing something of one type (in the
old method) with something that might have a new type (in the new method).
Similarly, when you pass an argument to a function,
you are replacing something that has one type (a parameter
with a declared type) with something that has another type
(the actual argument). When can you replace something that
has one type with something that has a subtype or a supertype?

當重寫方法時，可以使用一個新型別（在新方法中）替換舊型別（在舊方法中）。
類似地，當引數傳遞給函式時，可以使用另一種型別（實際引數）
的物件替換現有型別（具有宣告型別的引數）要求的物件。
什麼時候可以用具有子類別型或父型別的物件替換具有一種型別的物件那？

When substituting types, it helps to think in terms of _consumers_
and _producers_. A consumer absorbs a type and a producer generates a type.

從_消費者_和_生產者_的角度有助於我們思考替換型別的情況。
消費者接受型別，生產者產生型別。

**You can replace a consumer's type with a supertype and a producer's
type with a subtype.**

**可以使用父型別替換消費者型別，使用子類別型替換生產者型別。**

Let's look at examples of simple type assignment and assignment with
generic types.

下面讓我們看一下普通型別賦值和泛型型別賦值的範例。

### Simple type assignment

### 普通型別賦值

When assigning objects to objects, when can you replace a type with a
different type? The answer depends on whether the object is a consumer
or a producer.

將物件賦值給物件時，什麼時候可以用其他型別替換當前型別？
答案取決於物件是消費者還是生產者。

Consider the following type hierarchy:

分析以下型別層次結構：

<img src="images/type-hierarchy.png" alt="a hierarchy of animals where the supertype is Animal and the subtypes are Alligator, Cat, and HoneyBadger. Cat has the subtypes of Lion and MaineCoon">

Consider the following simple assignment where `Cat c` is a _consumer_
and `Cat()` is a _producer_:

思考下面範例中的普通賦值，
其中 `Cat c` 是 **消費者** 而 `Cat()` 是 **生產者**：

<?code-excerpt "lib/strong_analysis.dart (Cat-Cat-ok)"?>
{% prettify dart tag=pre+code %}
Cat c = Cat();
{% endprettify %}

In a consuming position, it's safe to replace something that consumes a
specific type (`Cat`) with something that consumes anything (`Animal`),
so replacing `Cat c` with `Animal c` is allowed, because `Animal` is
a supertype of `Cat`.

在消費者的位置，任意類別型（`Animal`）的物件替換特定型別（`Cat`）的物件是安全的。
因此使用 `Animal c` 替換 `Cat c` 是允許的，因為 Animal 是 Cat 的父類別。

{:.passes-sa}
<?code-excerpt "lib/strong_analysis.dart (Animal-Cat-ok)"?>
{% prettify dart tag=pre+code %}
Animal c = Cat();
{% endprettify %}

But replacing `Cat c` with `MaineCoon c` breaks type safety, because the
superclass may provide a type of Cat with different behaviors, such
as `Lion`:

但是使用 `MaineCoon c` 替換 `Cat c` 會打破型別的安全性，
因為父類可能會提供一種具有不同行為的 Cat ，例如 Lion ：

{:.fails-sa}
<?code-excerpt "lib/strong_analysis.dart (MaineCoon-Cat-err)"?>
{% prettify dart tag=pre+code %}
MaineCoon c = Cat();
{% endprettify %}

In a producing position, it's safe to replace something that produces a
type (`Cat`) with a more specific type (`MaineCoon`). So, the following
is allowed:

在生產者的位置，可以安全地將生產型別 (Cat) 替換成一個更具體的型別
 (MaineCoon) 的物件。因此，下面的操作是允許的：

{:.passes-sa}
<?code-excerpt "lib/strong_analysis.dart (Cat-MaineCoon-ok)"?>
{% prettify dart tag=pre+code %}
Cat c = MaineCoon();
{% endprettify %}

### Generic type assignment

### 泛型賦值

Are the rules the same for generic types? Yes. Consider the hierarchy
of lists of animals—a `List` of `Cat` is a subtype of a `List` of
`Animal`, and a supertype of a `List` of `MaineCoon`:

上面的規則同樣適用於泛型型別嗎？是的。
考慮動物列表的層次結構&mdash; Cat 型別的 List 是 Animal 型別 List 的子類別型，
是 MaineCoon 型別 List 的父型別。

<img src="images/type-hierarchy-generics.png" alt="List<Animal> -> List<Cat> -> List<MaineCoon>">

In the following example, 
you can assign a `MaineCoon` list to `myCats`
because `List<MaineCoon>` is a subtype of `List<Cat>`:

在下面的範例中，可以將 `MaineCoon` 型別的 List 賦值給 `myCats` ，
因為 `List<MaineCoon>` 是 `List<Cat>` 的子類別型：

{:.passes-sa}
<?code-excerpt "lib/strong_analysis.dart (generic-type-assignment-MaineCoon)" replace="/<MaineCoon/<[!MaineCoon!]/g"?>
{% prettify dart tag=pre+code %}
List<[!MaineCoon!]> myMaineCoons = ...
List<Cat> myCats = myMaineCoons;
{% endprettify %}

What about going in the other direction? 
Can you assign an `Animal` list to a `List<Cat>`?

從另一個角度看，可以將 `Animal` 型別的 List 賦值給 `List<Cat>` 嗎？

{:.fails-sa}
<?code-excerpt "lib/strong_analysis.dart (generic-type-assignment-Animal)" replace="/<Animal/<[!Animal!]/g"?>
{% prettify dart tag=pre+code %}
List<[!Animal!]> myAnimals = ...
List<Cat> myCats = myAnimals;
{% endprettify %}

This assignment doesn't pass static analysis 
because it creates an implicit downcast, 
which is disallowed from non-`dynamic` types such as `Animal`.

這個賦值不能透過靜態分析，因為它建立了一個隱含的向下轉型 (downcast)，
這在非 `dynamic` 型別中是不允許的，比如 `Animal`。

{{site.alert.version-note}}
  In packages that use a [language version][] before 2.12
  (when support for [null safety][] was introduced),
  code can implicitly downcast from these non-`dynamic` types.
  You can disallow non-`dynamic` downcasts in a pre-2.12 project
  by specifying `implicit-casts: false` 
  in the [analysis options file.][analysis]
{{site.alert.end}}

To make this type of code pass static analysis, 
you can use an explicit cast. 

若要這段程式碼能夠透過靜態分析，需要使用一個顯式轉換，
這可能會在執行時導致失敗。

<?code-excerpt "lib/strong_analysis.dart (generic-type-assignment-implied-cast)" replace="/as.*(?=;)/[!$&!]/g"?>
{% prettify dart tag=pre+code %}
List<Animal> myAnimals = ...
List<Cat> myCats = myAnimals [!as List<Cat>!];
{% endprettify %}

An explicit cast might still fail at runtime, though,
depending on the actual type of the list being cast (`myAnimals`).

不過，顯式轉換在執行時仍然可能會失敗，
這取決於轉換被轉換內容的實際型別 (此處是 `myAnimals`)。

### Methods

### 方法

When overriding a method, the producer and consumer rules still apply.
For example:

在重寫方法中，生產者和消費者規則仍然適用。例如：

<img src="images/consumer-producer-methods.png" alt="Animal class showing the chase method as the consumer and the parent getter as the producer">

For a consumer (such as the `chase(Animal)` method), you can replace
the parameter type with a supertype. For a producer (such as
the `parent` getter method), you can replace the return type with
a subtype.

對於使用者（例如 `chase(Animal)` 方法），可以使用父型別替換引數型別。
對於生產者（例如 `父類` 的 Getter 方法），可以使用子類別型替換返回值型別。

For more information, see
[Use sound return types when overriding methods](#use-proper-return-types)
and [Use sound parameter types when overriding methods](#use-proper-param-types).

有關更多資訊，請參閱
[重寫方法時，使用型別安全的返回值](#use-proper-return-types)
以及
[重寫方法時，使用型別安全的引數](#use-proper-param-types)。


## Other resources

## 其他資源

The following resources have further information on sound Dart:

以下是更多關於 Dart 型別安全的相關資源：

* [Fixing common type problems](/guides/language/sound-problems) -
  Errors you may encounter when writing sound Dart code, and how to fix them.

  [修復常見型別問題](/guides/language/sound-problems) -
  編寫型別安全的 Dart 程式碼時可能遇到的錯誤，
  以及解決錯誤的方法。
  
* [Fixing type promotion failures](/tools/non-promotion-reasons) - 
  Understand and learn how to fix type promotion errors.

  [修復型別轉換錯誤](/tools/non-promotion-reasons) -
  瞭解和學習如何修復型別轉換錯誤

* [Sound null safety](/null-safety) - 
  Learn about writing code with sound null safety enabled.

  [健全的空安全](/null-safety) - 
  學習關於如何撰寫健全的空安全程式碼。
  
* [Customizing static analysis][analysis] -
  How to set up and customize the analyzer and linter
  using an analysis options file.

  [Customizing static analysis][analysis] - 
  如何使用分析配置檔案設定及自訂分析器和 linter。

* [Dart 2 migration guide](/dart-2) - How to update Dart 1.x code to Dart 2.

  [Dart 2 遷移指南](/dart-2) - 如何從 Dart 1.x 程式碼遷移到 Dart 2 。


[analysis]: /guides/language/analysis-options
[dartdevc]: /tools/dartdevc
[language version]: /guides/language/evolution#language-versioning
[null safety]: /null-safety
