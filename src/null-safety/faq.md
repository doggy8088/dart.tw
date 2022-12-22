---
title: "Null safety: Frequently asked questions"
title: 空安全：常見問題
description: FAQs to help you migrate your Dart code to null safety
description: 幫助你遷移到空安全的常見問題解答
short-title: FAQ (null safety)
short-title: 空安全常見問題
---

This page collects some common questions we've heard about [null safety](/null-safety)
based on the experience of migrating Google internal code.

此處涵蓋了一些我們在遷移 Google 內部程式碼至 [空安全](/null-safety) 時遇到的常見問題。

## What runtime changes should I be aware of for users of migrated code?

## 在遷移程式碼時，我應該注意哪些執行時的改動？

Most of the effects of migration do not immediately affect users of migrated
code:

在空安全遷移中的大部分影響，不會立刻出現在剛剛遷移完成的開發者身上：

-   Static null safety checks for users first apply when they migrate their
    code.

    靜態的空安全檢查，會在開發者完成遷移後立刻生效。

-   Full null safety checks happen when all the code is migrated and sound mode
    is turned on.

    完整的空安全檢查，只會在所有程式碼都已遷移，並且啟用了完整的空安全模式時生效。

Two exceptions to be aware of are:

但是有兩項例外你需要注意：

-   The `!` operator is a runtime null check in all modes, for all users. So,
    when migrating, ensure that you only add `!` where it's an error for a
    `null` to flow to that location, even if the calling code has not migrated
    yet.

    對於任何模式而言，`!` 運運算元都是在執行時進行的空檢查。
    所以在進行遷移時，請確保你僅對 `null` 可能由混合模式造成汙染的程式碼位置新增 `!`，
    就算發起呼叫的程式碼還未遷移至空安全，也應如此。

-   Runtime checks associated with the `late` keyword apply in all modes, for
    all users. Only mark a field `late` if you are sure it is always initialized
    before it is used.

    `late` 會在執行時期檢查。所以請你僅在確定它被使用前一定會被初始化的情況下使用 `late`。

## What if a value is only `null` in tests?

## 如果在測試中的值始終為 `null` 應該怎樣處理？

If a value is only ever `null` in tests, the code can be improved by marking it
non-nullable and making the tests pass non-null values.

如果在測試中某個值始終為 `null`，可以透過將測試傳值和測試需要的值改為非空，來改進你的測試。

## How does `@required` compare to the new `required` keyword?

## 新的 `required` 和 `@required` 關鍵詞有何異同？

The `@required` annotation marks named arguments that must be passed; if not,
the analyzer reports a hint.

`@required` 註解會將引數標記為必須傳遞。如果未傳，分析器會給出一個提示。

With null safety, a named argument with a non-nullable type must either have a
default or be marked with the new `required` keyword. Otherwise, it wouldn't
make sense for it to be non-nullable, because it would default to `null` when
not passed.

有了空安全，非空型別的命名引數要麼需要預設值，要麼需要使用 `required` 關鍵字修飾。
否則，它在未傳遞時預設會是 `null`，就顯得不合理了。

When null safe code is called from legacy code the `required` keyword is treated
exactly like the `@required` annotation: failure to supply the argument will
cause an analyzer hint.

在舊的程式碼中，`required` 關鍵詞會被看作 `@required` 註解，
即引數未傳遞時會顯示一個分析器提示。

When null safe code is called from null safe code, failing to supply a
`required` argument is an error.

當你在空安全的程式碼中使用空安全程式碼時，如果 `required` 修飾的引數未傳遞，
會顯示一個錯誤。

What does this mean for migration? Be careful if adding `required` where there
was no `@required` before. Any callers not passing the newly-required argument
will no longer compile. Instead, you could add a default or make the argument
type nullable.

那麼它對於遷移來說意味著什麼呢？
當你給以前沒有使用 `@required` 註解的引數加上 `required` 時要十分小心。
任何沒有傳遞新需要的引數的程式碼，都無法進行編譯。
實際上，你可以加上預設值，或是將引數變為可空型別。

## How should I migrate non-nullable fields that should be `final`, but aren't?

## 我應該如何遷移應該為 `final` 而目前並不是的欄位？

Some computations can be moved to the static initializer. Instead of:

一些賦值計算可以移動到靜態的初始化中。
與其使用下面的方式：

{:.bad}
{% prettify dart tag=pre+code %}
// Initialized without values
ListQueue _context;
Float32List _buffer;
dynamic _readObject;

Vec2D(Map<String, dynamic> object) {
  _buffer = Float32List.fromList([0.0, 0.0]);
  _readObject = object['container'];
  _context = ListQueue<dynamic>();
}
{% endprettify %}

you can do:

你可以這樣做：

{:.good}
{% prettify dart tag=pre+code %}
// Initialized with values
final ListQueue _context = ListQueue<dynamic>();
final Float32List _buffer = Float32List.fromList([0.0, 0.0]);
final dynamic _readObject;

Vec2D(Map<String, dynamic> object) : _readObject = object['container'];
{% endprettify %}

However, if a field is initialized by doing computation in the constructor, then
it can't be `final`. With null safety, you'll find this also makes it harder for
it to be non-nullable; if it's initialized too late, then it's `null` until it's
initialized, and must be nullable. Fortunately, you have options:

然而，在建構函式中透過計算進行初始化的欄位，是無法為 `final` 的。
在使用空安全的時候，你會發現想讓它們的型別為非空，也不是一件容易的事。
如果初始化的時機不合適，那麼直到初始化前，它都只能是可空的型別。
幸運的是，你還有其他選擇：

-   Turn the constructor into a factory, then make it delegate to an actual
    constructor that initializes all the fields directly. A common name for such
    a private constructor is just an underscore: `_`. Then, the field can be
    `final` and non-nullable. This refactoring can be done *before* the
    migration to null safety.

    將構造轉變為工廠方法，並將其委託給一個直接初始化所有欄位的真正的建構函式。
    在 Dart 中，這樣的私有構造通常是一個下劃線：`_`。
    如此一來，欄位就可以是 `final` 且非空了。
    在空安全遷移介入 **之前**，你就可以這樣進行調整。

-   Or, mark the field `late final`. This enforces that it's initialized exactly
    once. It must be initialized before it can be read.

    或者，將欄位標記為 `late final`。這會使得欄位只被初始化一次。
    在它被讀取之前必須被初始化。

## How should I migrate a `built_value` class?

## 我應該如何遷移 `built_value` 類？

Getters that were annotated `@nullable` should instead have nullable types; then
remove all `@nullable` annotations. For example:

使用了 `@nullable` 註解的 getter 應當直接轉變為可空的型別，
然後移除所有的 `@nullable` 註解。例如：

```dart
@nullable
int get count;
```

becomes

變成

```dart
int? get count; //  Variable initialized with ?
```

Getters that were *not* marked `@nullable` should *not* have nullable types,
even if the migration tool suggests them. Add `!` hints as needed then rerun the
analysis.

就算遷移工具建議，**沒有** 使用 `@nullable` 註解的 getter 也不應該是可空的型別。
這時可以根據需要新增 `!` 運運算元，並且重新進行分析。

## How should I migrate a factory that can return `null`?

## 我應該如何遷移可能返回 `null` 的工廠方法？

_Prefer factories that do not return null._ We have seen code that meant to
throw an exception due to invalid input but instead ended up returning null.

**優先使用不返回 null 的工廠方法**。
我們看到了很多程式碼，本意是想在呼叫不正確時丟擲一個例外，但最終卻返回了空。

Instead of:

與其這樣寫：

{:.bad}
{% prettify dart tag=pre+code %}
  factory StreamReader(dynamic data) {
    StreamReader reader;
    if (data is ByteData) {
      reader = BlockReader(data);
    } else if (data is Map) {
      reader = JSONBlockReader(data);
    }
    return reader;
  }
{% endprettify %}

Do:

不如這樣：

{:.good}
{% prettify dart tag=pre+code %}
  factory StreamReader(dynamic data) {
    if (data is ByteData) {
      // Move the readIndex forward for the binary reader.
      return BlockReader(data);
    } else if (data is Map) {
      return JSONBlockReader(data);
    } else {
      throw ArgumentError('Unexpected type for data');
    }
  }
{% endprettify %}


If the intent of the factory was indeed to return null, then you can turn it
into a static method so it is allowed to return `null`.

如果一個工廠方法的初衷就是可能返回空值，將其轉為可返回 `null` 的靜態方法是更好的選擇。

## How should I migrate an `assert(x != null)` that now shows as unnecessary?

## 我應該如何遷移現在提示無用的 `assert(x != null)`？

The assert will be unnecessary when everything is fully migrated, but for now it
*is* needed if you actually want to keep the check. Options:

對於完全遷移的程式碼而言，這個斷言是不必要的，但是如果你希望保留該檢查，那麼它 **也需要** 留下。
幾種方式可供你選擇：

-   Decide that the assert is not really necessary, and remove it. This is a
    change in behavior when asserts are enabled.

    確定是否真的需要這個斷言，然後將其刪除。
    當斷言啟用時，這是一種行為上的變更。

-   Decide that the assert can be checked always, and turn it into
    `ArgumentError.checkNotNull`. This is a change in behavior when asserts are
    not enabled.

    確定斷言始終會被檢查，接著將其轉換為 `ArgumentError.checkNotNull`。
    當斷言未啟用時，這是一種行為上的變更。

-   Keep the behavior exactly as is: add `// ignore:
    unnecessary_null_comparison` to bypass the warning.

    透過新增 `//ignore: unnecessary_null_comparison` 來繞過警告並且保持原有的行為。

## How should I migrate a runtime null check that now shows as unnecessary?

## 我應該如何遷移現在提示不必要的執行時空判斷？

The compiler flags an explicit runtime null check as an unnecessary
comparison if you make `arg` non-nullable.

如果 `arg` 為非空時，編譯器會在執行時將顯式空安全判斷標記為非必要。

```dart
if (arg == null) throw ArgumentError(...)`
```

You must include this check if the program is a mixed-version one.
Until everything is fully migrated and the code switches to running
with sound null safety, `arg` might be set to `null`.

混合模式下的程式必須包含這樣的判斷。
在所有程式碼都遷移且執行在完全的空安全模式下前，
`arg` 仍然可能為 `null`。

The simplest way to preserve behavior is change the check into
[`ArgumentError.checkNotNull`]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/ArgumentError/checkNotNull.html).

保留這項行為的最簡單的方法是將判斷改為
[`ArgumentError.checkNotNull`](https://api.dart.dev/stable/dart-core/ArgumentError/checkNotNull.html)。

The same applies to some runtime type checks. If `arg`
has static type `String`, then `if (arg is! String)` is actually checking
whether `arg` is `null`. It might look like migrating to null safety means `arg`
can never be `null`, but it could be `null` in unsound null safety. So, to preserve
behavior, the null check should remain.

執行時的檢查同樣適用。如果 `arg` 指定了靜態型別為 `String`，
那麼 `if (arg is! String)` 實際上是在檢查 `arg` 是否為 `null`。
儘管程式碼在遷移到空安全後，`arg` 應該是永遠不為 `null` 的，
但是在不完全的空安全中它仍有可能為 `null` 的。

## The `Iterable.firstWhere` method no longer accepts `orElse: () => null`.

## `Iterable.firstWhere` 方法不再接受 `orElse: () => null`。

Import `package:collection` and use the extension method `firstWhereOrNull`
instead of `firstWhere`.

匯入 `package:collection` package 並使用 `firstWhereOrNull` 擴充方法代替 `firstWhere`。

## How do I deal with attributes that have setters?

## 我應該如何處理有 setters 的屬性？

Unlike the `late final` suggestion above, these attributes cannot be marked as
final. Often, settable attributes also do not have initial values since they are
expected to be set sometime later.

與上文說到的 `late final` 的建議不同的是，這些欄位不能被標記為終值。
通常，可被修改的屬性也沒有初始值，因為它們可能會在稍後才被賦值。

In such cases, you have two options:

在這樣的情況下，你有兩種選擇：

-   Set it to an initial value. Often times, the omission of an initial value is
    by mistake rather than deliberate.

    為其設定初始值。通常情況下，初始值未被設定是無意的錯誤，而不是有意為之。

-   If you are _sure_ that the attribute needs to be set before accessed, mark
    it as `late`.

    如果你 **確定** 這個屬性需要在存取之前被賦值，將它標記為 `late`。

    WARNING: The `late` keyword adds a runtime check. If any user calls `get`
    before `set` they'll get an error at runtime.

    **注意**：`late` 關鍵詞會在執行時新增檢查。
    如果在 `set` 之前呼叫了 `get`，會在執行時丟擲例外。

## How do I signal that the return value from a Map is non-nullable?

## 我需要怎樣標記對映的返回值為非空型別？

The
[lookup operator]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/Map/operator_get.html)
on Map (`[]`) by default returns a nullable type. There's no way to signal to
the language that the value is guaranteed to be there.

對映型別的
[查詢運運算元](https://api.dart.dev/stable/dart-core/Map/operator_get.html)
(`[]`) 返回的值預設是可空型別。
此處沒有辦法告訴 Dart ，返回的值一定是非空的。

In this case, you should use the bang operator (`!`) to cast the value back to
V:

在這種情況下，你應該使用強制非空運運算元 (`!`) 將可空的型別轉為非空 (V)。

```dart
return blockTypes[key]!;
```

Which will throw if the map returns null. If you want explicit handling for that case:

如果 map 返回了 null，則會丟擲例外。如果你希望手動處理這些情況：

```dart
var result = blockTypes[key];
if (result != null) return result;
// Handle the null case here, e.g. throw with explanation.
```

## Why is the generic type on my List/Map nullable?

## 為什麼我的 List/Map 中的泛型是可空的？

It is typically a code smell to end up with nullable code like this:

下面這樣以可空內容結尾的程式碼是一種典型的程式碼異味：

{:.bad}
{% prettify dart tag=pre+code %}
List<Foo?> fooList; // fooList can contain null values
{% endprettify %}

This implies `fooList` might contain null values. This might happen if you are
initializing the list with length and filling it in via a loop.

它隱含了 `fooList` 可能包含空值的資訊。
在你以長度初始化列表並迴圈填入值時，這種情況可能會出現。

If you are simply initializing the list with the same value, you should instead
use the 
[`filled`]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/List/List.filled.html) 
constructor.

如果你僅僅想要以相同的值初始化列表，你應該使用
[`filled`](https://api.dart.dev/stable/dart-core/List/List.filled.html)
構造。

{:.bad}
{% prettify dart tag=pre+code %}
_jellyCounts = List<int?>(jellyMax + 1);
for (var i = 0; i <= jellyMax; i++) {
  _jellyCounts[i] = 0; // List initialized with the same value
}
{% endprettify %}

{:.good}
{% prettify dart tag=pre+code %}
_jellyCounts = List<int>.filled(jellyMax + 1, 0); // List initialized with filled constructor
{% endprettify %}

If you are setting the elements of the list via an index, or you are populating
each element of the list with a distinct value, you should instead use the
list literal syntax to build the list.

如果你需要透過索引來設定元素，或者使用不同的值填充每個元素，
則應該使用列表的字面量表達式來建構列表。

{:.bad}
{% prettify dart tag=pre+code %}
_jellyPoints = List<Vec2D?>(jellyMax + 1);
for (var i = 0; i <= jellyMax; i++) {
  _jellyPoints[i] = Vec2D(); // Each list element is a distinct Vec2D
}
{% endprettify %}

{:.good}
{% prettify dart tag=pre+code %}
_jellyPoints = [
  for (var i = 0; i <= jellyMax; i++)
    Vec2D() // Each list element is a distinct Vec2D
];
{% endprettify %}

To generate a fixed-length list,
use the [`List.generate`][] constructor
with the `growable` parameter set to `false`:

你可以使用 [`List.generate`][] 構造加
`growable` 引數設定為 `false` 來產生固定長度的列表：

```dart
_jellyPoints = List.generate(jellyMax, (_) => Vec2D(), growable: false);
```

[`List.generate`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-core/List/List.generate.html

{% comment %}
  Would preferably suggest a language syntax here,
  which is being suggested in https://github.com/dart-lang/language/issues/2477.
{% endcomment %}
 
## What happened to the default List constructor?

## 預設的 List 構造有什麼改動？

You may encounter this error:

你可能會遇到這樣的錯誤：

```none
The default 'List' constructor isn't available when null safety is enabled. #default_list_constructor
```

The default list constructor fills the list with `null`, which is a problem.

預設的列表構造會將列表用 `null` 填充，會造成問題。

Change it to `List.filled(length, default)` instead.

將它變為 `List.filled(length, default)` 即可。

## I'm using `package:ffi` and get a failure with `Dart_CObject_kUnsupported` when I migrate. What happened?

## 我在遷移使用了 `package:ffi` 的程式碼的時候遇到了 `Dart_CObject_kUnsupported` 的錯誤。發生了什麼？

Lists sent via ffi can only be `List<dynamic>`, not `List<Object>` or
`List<Object?>`. If you didn't change a list type explicitly in your migration,
a type might still have changed because of changes to type inference that happen
when you enable null safety.

透過 ffi 傳送的列表只能是 `List<dynamic>`，
而不能是 `List<Object>` 或 `List<Object?>`。
就算你未在遷移過程中手動更改型別，型別也可能會被改變，
因為啟用空安全後，型別推導推算出了這樣的結果。

The fix is to explicitly create such lists as `List<dynamic>`.

手動建立 `List<dynamic>` 型別的列表可以解決這個問題。

## Why does the migration tool add comments to my code? {#migration-comments}

## 為什麼遷移工具在我的程式碼中添加了註釋 {#migration-comments}

The migration tool adds `/* == false */` or `/* == true */` comments when it
sees conditions that will always be false or true while running in sound mode.
Comments like these might indicate that the automatic migration is incorrect and
needs human intervention. For example:

空安全模式下，在當某個表示式的結果一定為 false 或 true 的時候，
遷移工具會自動新增 `/* == false */` 或者 `/* == true */` 這樣的註釋。
這樣的註釋將會導致自動遷移出現錯誤，並且需要人工干預。例如：

```dart
if (registry.viewFactory(viewDescriptor.id) == null /* == false */)
```

In these cases, the migration tool can't distinguish defensive-coding situations
and situations where a null value is really expected. So the tool tells you what
it knows ("it looks like this condition will always be false!") and lets you
decide what to do.

在這些情況下，遷移工具無法區分防禦性編碼情況或是確實需要空值的情況。
那麼該工具會告訴你「這看起來永遠為 false！」並讓你進行決定。

## What should I know about compiling to JavaScript and null safety?

## 關於編譯為 JavaScript 時的空安全我應該知道什麼？

Null safety brings many benefits like reduced code size and improved
app performance. Such benefits surface more when compiled to native
targets like Flutter and AOT. Previous work on the production web
compiler had introduced optimizations similar to what null safety
later introduced. This may make resulting gains to production web apps
seem less than their native targets.

空安全帶來了程式碼體積減小及效能提升等最佳化。
表面上 Flutter 編譯為原生端的建構的最佳化會更加明顯，例如 AOT。
我們先前已經在 Web 的生產建構器上已經引入了一些類似空安全的最佳化。
所以，Web 應用上的變化可能並不如原生端明顯。

A few notes that are worth highlighting:

依然有幾點值得注意：

* The production JavaScript compiler generates `!` null assertions. You might
  not notice them when comparing the output of the compiler before and
  after adding null assertions. That's because the compiler already
  generated null checks in programs that weren't null safe.

  生產環境下的 JavaScript 編譯器會產生 `!` 空斷言，
  在比較輸出的時候你可能不會注意到它。
  這是因為編譯器已支援了對空值進行檢查。

* The compiler generates these null assertions regardless of the
  soundness of null safety or optimization level. In fact, the compiler
  doesn't remove `!` when using `-O3` or `--omit-implicit-checks`.

  無論是健全或非健全的安全，又或是不同的最佳化等級，編譯器都會產生這些空斷言，
  實際上在使用 `-O3` 或 `--omit-implicit-checks` 時編譯器都不會移除 `!`。

* The production JavaScript compiler might remove unnecessary null checks.
  This happens because the optimizations that the production web
  compiler made prior to null safety removed those checks when it
  knew the value was not null.

  生產環境下的 JavaScript 編譯器可能會移除沒有必要的空檢查，
  一般會發生在生產環境下的 Web 編譯器在空安全之前做了一些最佳化，
  當它知道值不為空的時候，就刪除了這些檢查。

* By default, the compiler would generate parameter subtype checks.
  These runtime checks ensure covariant virtual calls have appropriate
  arguments. The compiler skips these checks with the
  `--omit-implicit-checks` option. Using this option can generate apps
  with unexpected behavior if the code includes invalid types.
  To avoid any surprises, continue provide strong test coverage for
  your code. In particular, the compiler optimizes code based
  on the fact that inputs should comply with the type declaration. If
  the code provides arguments of an invalid type, those optimizations
  would be wrong and the program could misbehave. This was true for
  inconsistent types before, and is true with inconsistent 
  nullabilities now with sound null-safety.

  預設情況下，編譯器會產生引數子類別型的檢查。
  （用於確保協變的虛擬呼叫使用了合適的引數的執行時期檢查）。
  與先前相同，使用 `--omit-implicit-checks` 編譯器會省略它們。
  回想一下，如果型別無效，這個開關會讓程式出現例外，
  因此我們依然建議程式碼測試覆蓋率儘可能地高，以避免任何事故。
  特別是編譯器會基於傳入值符合型別宣告這一條件來最佳化程式碼。
  如果程式碼提供了無效型別的引數，最佳化將不是正確的，導致程式例外。
  這對於之前的型別不一致是正確的，對於現在健全的空安全的可空性不一致也是如此。

* You may notice that the development JavaScript compiler and the Dart 
  VM have special error messages for null checks, but to keep 
  applications small, the production JavaScript compiler does not.

  你可能會注意到開發版的 JavaScript 編譯器和 Dart VM
  對於空檢查的錯誤有比較特殊的錯誤提示，
  但是為了保持應用的輕量體積，
  生產環境下的編譯器並沒有這樣的提示。

* You may see errors indicating that `.toString` is not found on `null`.
  This is not a bug. The compiler has always encoded some null checks
  in this way. That is, the compiler represents some null checks
  compactly by making an unguarded access of a property of the
  receiver. So instead of `if (a == null) throw`, it generates
  `a.toString`. The `toString` method is defined in JavaScript Object
  and is a fast way to verify that an object is not null.

  你可能會看到 `.toString` 在 `null` 上未找到的錯誤。
  這不是一個 bug，是編譯器一直以來新增的空檢查。
  編譯器會透過對接收者物件屬性的存取來壓縮一些空檢查。
  它產生的是 `a.toString` 而不是 `if (a == null) throw`。
  在 JavaScript 物件中定義的 `toString` 方法可以快速驗證物件是否可空。

  If the very first action after a null check is an action that crashes
  when the value is null, the compiler can remove the null check and
  let the action cause the error.

  如果空檢查後的第一個行為是當值為空時會崩潰，
  編譯器可以刪除空檢查並讓動作丟擲錯誤。

  For example, a Dart expression `print(a!.foo());` could turn directly
  into:

  例如，`print(a!.foo());` 陳述式可以直接轉換為：

  ```js
    P.print(a.foo$0());
  ```

  This is because the call `a.foo$()` will crash if `a` is null.
  If the compiler inlines `foo`, it will preserve the null check.
  So for example, if `foo` was `int foo() => 1;`  the compiler might 
  generate:

  這是因為呼叫 `a.foo$()` 會在 `a` 為空時崩潰。
  如果 dart2js 內聯 `foo`，它將保留空檢查。
  例如，如果 `foo` 是 `int foo() => 1;`，編譯器可能會產生：

  ```js
    a.toString;
    P.print(1);
  ```

  If the inlined method first accessed a field on the receiver, like
  `int foo() => this.x + 1;`, then the production compiler can remove
  the redundant `a.toString` null check, as non-inlined calls, and
  generate:

  如果內聯方法做的第一件事是對接收者的欄位存取，例如 `int foo() => this.x + 1;`，
  那麼 dart2js 可以再次刪除多餘的 `a.toString` 空檢查，就像非內聯呼叫一樣產生：

  ```js
    P.print(a.x + 1);
  ```
    
## Resources

## 資源

*   [DartPad with Null Safety]({{site.dartpad}})

    [支援空安全的 DartPad]({{site.dartpad}})

*   [Sound null safety](/null-safety)

    [健全的空安全](/null-safety)
