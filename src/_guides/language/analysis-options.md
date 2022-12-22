---
title: Customizing static analysis
title: 自訂靜態分析
description: Use an analysis options file and code comments to customize static analysis.
description: 透過對分析檔案和程式碼註釋來自訂靜態分析的內容。
---

<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /(^|\n) *\/\/\s+ignore: (stable|beta|dev)[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore: (stable|beta|dev)[^\n]+\n/$1\n/g; /. • (lib|test)\/\w+\.dart:\d+:\d+//g"?>

<style>
li.L0, li.L1, li.L2, li.L3,
li.L5, li.L6, li.L7, li.L8, li.L9 {
  background: none;
  list-style-type: decimal;
}
pre.prettyprint.analyzer .highlight {
    border-bottom: 2px red dashed;
    background: inherit;
    padding-bottom: 1px;
}
</style>

Static analysis allows you to find problems before
executing a single line of code. It's a powerful tool
used to prevent bugs and ensure that code conforms to style
guidelines.

靜態分析讓你的程式碼問題能在執行前被發現，
它在防止問題產生和程式碼風格指南的遵循上很有幫助。

With the help of the analyzer, you can find
simple typos. For example, perhaps an accidental semicolon
made its way into an `if` statement:

在分析器的幫助下，你可以發現簡單的拼寫錯誤。
例如，可能在 `if` 陳述式中不小心多打了一個分號：

<blockquote class="ml-3" markdown="1">
<?code-excerpt "analysis/lib/lint.dart (empty_statements)" replace="/(if .*?)(;)/$1[!$2!]/g"?>
{% prettify dart class="linenums:8 analyzer"%}
void increment() {
  if (count < 10) [!;!]
  count++;
}
{% endprettify %}

If properly configured, the analyzer points to the semicolon and
produces the following warning:

如果配置得當，分析器會指出這個分號的位置並輸出如下警告：

{:.console-output}
<?code-excerpt "analysis/analyzer-results-stable.txt" retain="empty_statements" replace="/lib\/lint.dart/example.dart/g"?>
```nocode
info - example.dart:9:19 - Avoid empty statements. - empty_statements
```
</blockquote>

The analyzer can also help you find more subtle problems.
For example, perhaps you've forgotten to close a sink method:

分析器也能幫你找出更多細節問題。
例如，也許你忘記關閉一個 sink 了：

<blockquote class="ml-3" markdown="1">
<?code-excerpt "analysis/lib/lint.dart (close_sinks)" replace="/(contr.*?)(;)/[!$1!]$2/g"?>
{% prettify dart class="analyzer"%}
var [!controller = StreamController<String>()!];
{% endprettify %}

{:.console-output}
<?code-excerpt "analysis/analyzer-results-stable.txt" retain="close_sinks" replace="/-(.*?):(.*?):(.*?)-/-/g"?>
```nocode
info - Close instances of `dart.core.Sink`. - close_sinks
```
</blockquote>

In the Dart ecosystem,
the Dart Analysis Server and other tools use the
[analyzer package]({{site.pub-pkg}}/analyzer)
to perform static analysis.

在 Dart 生態系統中，Dart 分析服務和其他相關工具使用了
[analyzer]({{site.pub-pkg}}/analyzer)
來提供靜態分析。

You can customize static analysis to look for a variety of potential
problems, including errors and warnings specified in the
[Dart language spec](/guides/language/spec).
You can also configure the linter, one of the analyzer's plugins,
to ensure that your code complies with the
[Dart Style Guide](/guides/language/effective-dart/style)
and other suggested guidelines in
[Effective Dart][]. Dart tools such as the
[Dart compiler (`dart compile`)](/tools/dart-compile),
[`dart analyze`](/tools/dart-analyze),
[`flutter analyze`]({{site.flutter-docs}}/testing/debugging#the-dart-analyzer),
and [JetBrains IDEs](/tools/jetbrains-plugin)
use the analyzer package to evaluate your code.

你可以自訂靜態分析以尋找各種潛在的問題，
包括在 [Dart 程式設計語言規範](/guides/language/spec) 中規定的錯誤和警告。
你同樣能透過配置 linter ——分析器的一個外掛，
來確保你的程式碼遵循 [Dart 程式碼風格指南](/guides/language/effective-dart/style)
和 [高效 Dart][Effective Dart] 中其他建議的準則。
諸如 [Dart 編譯器 (`dart compile`)](/tools/dart-compile)，
[`dart analyze`](/tools/dart-analyze),
[`flutter analyze`]({{site.flutter-docs}}/testing/debugging#the-dart-analyzer),
和 [JetBrains IDEs](/tools/jetbrains-plugin)
等 Dart 工具都會使用 analyzer package 來評估你的程式碼。

This document explains how to customize the behavior of the analyzer
using either an analysis options file or comments in Dart source code. If you want to
add static analysis to your tool, see the
[analyzer package]({{site.pub-pkg}}/analyzer) docs and the
[Analysis Server API Specification.](https://htmlpreview.github.io/?https://github.com/dart-lang/sdk/blob/main/pkg/analysis_server/doc/api.html)

這篇文件解釋瞭如何透過使用分析配置檔案，
或在 Dart 原始碼中添加註釋來自訂分析器的行為。
如果你想在工具中新增靜態分析規則，請參考 [analyzer package]({{site.pub-pkg}}/analyzer) 的文件
和 [Analysis Server API 規範](https://htmlpreview.github.io/?https://github.com/dart-lang/sdk/blob/main/pkg/analysis_server/doc/api.html)。

{{site.alert.note}}
  
  To view various analyzer diagnostics with explanations and common fixes,
  see [Diagnostic messages][diagnostics].

  要檢視分析器的各種診斷及其解釋和通用修復，請參考 [診斷訊息][diagnostics]。

{{site.alert.end}}

## The analysis options file

## 分析配置檔案

Place the analysis options file, `analysis_options.yaml`,
at the root of the package, in the same directory as the pubspec file.

將分析配置檔案 `analysis_options.yaml` 放在套件的根目錄，
即和 pubspec 檔案同樣的目錄下。

{{site.alert.tip}}

  The older name for the analysis options file was `.analysis_options`;
  support for that filename was dropped in Dart 2.8.
  To upgrade an `.analysis_options` file,
  just change its name to `analysis_options.yaml`.

  分析配置檔案的舊名稱是 `.analysis_options`，
  自 Dart 2.8 起已經停止了對該檔名的支援。
  只需將檔名改為 `analysis_options.yaml`，便可從舊檔案升級。

{{site.alert.end}}

Here's a sample analysis options file:

這是一個分析配置檔案的範例：

<?code-excerpt "analysis_options.yaml" from="include" remove="implicit-dynamic" retain="/^$|\w+:|- cancel/" remove="https:"?>
```yaml
include: package:lints/recommended.yaml

analyzer:
  exclude: [build/**]
  language:
    strict-casts: true
    strict-raw-types: true

linter:
  rules:
    - cancel_subscriptions
```

The sample illustrates the most common top-level entries:

該範例說明了一些最常用的最上層配置入口：

- Use <code>include: <em>url</em></code> to
  bring in options from the specified URL—in this case,
  from a file in the `lints` package.
  Because YAML doesn't allow duplicate keys,
  you can include at most one file.
  
  使用 <code>include: <em>url</em></code> 來從指定的 URL 引入選項 —— 在這種情況下，
  通常是引入來自 lints 套件中的檔案。
  由於 YAML 不支援多個重複的 key，你只能引入最多一個檔案；
  
- Use the `analyzer:` entry to customize static analysis:
  [enabling stricter type checks](#enabling-additional-type-checks),
  [excluding files](#excluding-files),
  [ignoring specific rules](#ignoring-rules),
  [changing the severity of rules](#changing-the-severity-of-rules), or
  [enabling experiments](/tools/experiment-flags#using-experiment-flags-with-the-dart-analyzer-command-line-and-ide).
  
  使用 `analyzer:` 入口來自訂靜態分析：
  [啟用更嚴格的型別檢查](#enabling-additional-type-checks),
  [排除檔案](#excluding-files),
  [忽略特定規則](#ignoring-rules),
  [改變規則的警告等級](#changing-the-severity-of-rules), or
  [開啟實驗性功能](/tools/experiment-flags#using-experiment-flags-with-the-dart-analyzer-command-line-and-ide)；

- Use the `linter:` entry to configure [linter rules](#enabling-linter-rules).

  使用 `linter:` 入口來配置 [linter 規則](#enabling-linter-rules)。

{{site.alert.warn}}

  **YAML is sensitive to whitespace.** 
  Don't use tabs in a YAML file,
  and use 2 spaces to denote each level of indentation.

  **YAML 對空格敏感。**
  不要在 YAML 檔案中使用 tab，
  用 2 個空格來表示每一級縮排。

{{site.alert.end}}

If the analyzer can't find an analysis options file at the package root,
it walks up the directory tree, looking for one.
If no file is available, the analyzer defaults to standard checks.

如果分析器在 package 的根目錄下無法找到一個分析配置檔案，
它將會往下查詢整個目錄樹。
如果還是沒有可用的配置檔案，分析器則預設使用標準檢查規則。

Consider the following directory structure for a large project:

對於如下所示的一個大型專案的目錄結構而言：

<img 
  src="/assets/img/guides/analysis-options-directory-structure.png"
  alt="project root contains analysis_options.yaml (#1) and 3 packages, one of which (my_package) contains an analysis_options.yaml file (#2).">

The analyzer uses file #1 to analyze the code in `my_other_package`
and `my_other_other_package`, and file #2 to analyze the code in
`my_package`.

分析器使用 #1 檔案來分析 `my_other_package` 
和 `my_other_other_package` 中的程式碼，
使用 #2 檔案來分析 `my_package` 中的程式碼。

## Enabling stricter type checks {#enabling-additional-type-checks}

## 啟用更嚴格的型別檢查 {#enabling-additional-type-checks}

If you want stricter static checks than
the [Dart type system][type-system] requires,
consider enabling the 
`strict-casts`, `strict-inference`, and `strict-raw-types` language modes:

如果你想要比 [Dart 型別系統][type-system] 所要求的更加嚴格的型別檢查，
考慮開啟 `strict-casts`，`strict-inference`，
和 `strict-raw-types` 語言模式：

<?code-excerpt "analysis/analysis_options.yaml" from="analyzer" to="strict-raw-types" remove="exclude"?>
```yaml
analyzer:
  language:
    strict-casts: true
    strict-inference: true
    strict-raw-types: true
```

You can use the modes together or separately; all default to `false`.

你可以單獨或一起使用這些模式，他們預設都為 `false` 狀態。

`strict-casts: <bool>`
<br> A value of `true` ensures that the type inference engine never
  implicitly casts from `dynamic` to a more specific type.
  The following valid Dart code includes an implicit downcast from the
  `dynamic` value returned by `jsonDecode` to `List<String>`
  that could fail at runtime.
  This mode reports the potential error, 
  requiring you to add an explicit cast or otherwise adjust your code.

`strict-casts: <bool>`
<br> 設為 `true` 可確保型別推理引擎不再將 `dynamic` 進行隱含型別轉換。
  下方的 Dart 程式碼在 `List<String>` 引數上傳遞了一個 `jsonDecode` 方法的返回值，
  實際是將返回的 `dynamic` 做了隱含向下轉換，在執行時可能導致錯誤。
  該模式會報告此類潛在的錯誤，要求你新增一個顯示的型別轉換或者調整你的程式碼。

{:.fails-sa}
<?code-excerpt "analysis/lib/strict_modes.dart (strict-casts)" replace="/jsonDecode\(jsonText\)/[!$&!]/g"?>
{% prettify dart class="analyzer" %}
void foo(List<String> lines) {
  ...
}

void bar(String jsonText) {
  foo([!jsonDecode(jsonText)!]); // Implicit cast
}
{% endprettify %}

{:.console-output}
<?code-excerpt "analysis/analyzer-results-stable.txt" retain="The argument type 'dynamic' can't be assigned"  replace="/-(.*?):(.*?):(.*?)-/-/g"?>
```nocode
error - The argument type 'dynamic' can't be assigned to the parameter type 'List<String>'. - argument_type_not_assignable
```

{{site.alert.version-note}}

  The `strict-casts` mode was introduced in Dart 2.16.
  To enable similar checks with earlier SDK releases,
  consider using the now deprecated `implicit-casts` option:

  `strict-casts` 模式在 Dart 2.16 中被引入。
  想在更早的 SDK 釋出版中啟用類似的檢查，
  考慮使用當前已廢棄的 `implicit-casts` 選項：

  ```yaml
  analyzer:
    strong-mode:
      implicit-casts: false
  ```
{{site.alert.end}}

`strict-inference: <bool>`
<br> A value of `true` ensures that the type inference engine never chooses
  the `dynamic` type when it can't determine a static type.
  The following valid Dart code creates a `Map`
  whose type argument cannot be inferred, 
  resulting in an inference failure hint by this mode:

`strict-inference: <bool>`
<br> 設為 `true` 可確保當型別推理引擎無法確定靜態型別時，不再選擇`dynamic` 型別。
  下方合法 Dart 程式碼建立了一個類別型引數無法被推斷的 `Map`，
  在該模式下會觸發推斷失敗的 hint 提示：

{:.fails-sa}
<?code-excerpt "analysis/lib/strict_modes.dart (strict-inference)" replace="/{}/[!$&!]/g"?>
{% prettify dart class="analyzer" %}
final lines = [!{}!]; // Inference failure
lines['Dart'] = 10000;
lines['C++'] = 'one thousand';
lines['Go'] = 2000;
print('Lines: ${lines.values.reduce((a, b) => a + b)}'); // Runtime error
{% endprettify %}

{:.console-output}
<?code-excerpt "analysis/analyzer-results-stable.txt" retain="The type argument(s) of 'Map'"  replace="/. Use.*'Map'. / /g; /-(.*?):(.*?):(.*?)-/-/g"?>
```nocode
info - The type argument(s) of 'Map' can't be inferred - inference_failure_on_collection_literal
```

{{site.alert.info}}

  The `strict-inference` mode can identify many situations
  which result in an inference failure.
  
  `strict-inference` 模式能識別多種推斷失敗的情況。

  See [Conditions for strict inference failure][] 
  for an exhaustive list of inference failure conditions.

  檢視 [嚴格型別推斷失敗的條件][Conditions for strict inference failure]
  來了解詳細的推斷失敗條件清單。

{{site.alert.end}}

[Conditions for strict inference failure]: https://github.com/dart-lang/language/blob/master/resources/type-system/strict-inference.md#conditions-for-strict-inference-failure

`strict-raw-types: <bool>`
<br> A value of `true` ensures that the type inference engine never chooses
  the `dynamic` type when it can't determine a static type
  due to omitted type arguments.
  The following valid Dart code has a `List` variable with a raw type,
  resulting in a raw type hint by this mode:

`strict-raw-types: <bool>`
<br> 設為 `true` 可確保當型別推理引擎，由於省略型別引數而無法確定靜態型別時，
  不再選擇`dynamic` 型別。
  下方合法 Dart 程式碼中有一個原始型別的 `List` 變數，
  導致在該模式下觸發了原始型別 hint 提示。

{:.fails-sa}
<?code-excerpt "analysis/lib/strict_modes.dart (strict-raw-types)" replace="/List n/[!List!] n/g"?>
{% prettify dart class="analyzer" %}
[!List!] numbers = [1, 2, 3]; // List with raw type
for (final n in numbers) {
  print(n.length); // Runtime error
}
{% endprettify %}

{:.console-output}
<?code-excerpt "analysis/analyzer-results-stable.txt" retain="The generic type" replace="/. Use explicit.*\. / /g; /-(.*?):(.*?):(.*?)-/-/g"?>
```nocode
info - The generic type 'List<dynamic>' should have explicit type arguments but doesn't - strict_raw_type
```

## Enabling and disabling linter rules {#enabling-linter-rules}

## 啟用和停用 linter 規則 {#enabling-linter-rules}

The analyzer package also provides a code linter. A wide variety of
[linter rules][] are available. Linters tend to be
nondenominational—rules don't have to agree with each other.
For example, some rules are more appropriate for library packages
and others are designed for Flutter apps.
Note that linter rules can have false positives, unlike static analysis.

analyzer 包同樣提供一個程式碼 linter，幷包含一份廣泛多樣的 [linter 規則][linter rules]。
提示規則之間往往是無關聯性的，各種規則之間不必彼此遵守。
例如，有些規則更合適支援庫，而另一些則是為 Flutter 應用設計的。
注意，linter 規則可能會觸發誤報，靜態分析則不會。

### Enabling Dart team recommended linter rules {#lints}

### 啟用 Dart 團隊推薦的 linter 規則 {#lints}

The Dart team provides two sets of recommended linter rules
in the [lints package][]:

Dart 團隊在 [lints package][] 中提供了 2 個推薦的 linter 規則集合：

Core rules
<br> Help identify critical issues that are likely to lead to problems
  when running or consuming Dart code.
  All code should pass these linter rules.
  Packages that are uploaded to [pub.dev]({{site.pub}})
  have a [package score]({{site.pub}}/help/scoring)
  that's based in part on passing these rules.

核心規則
<br> 幫助確認可能導致在執行或使用 Dart 程式碼時引發問題的關鍵事項。
  所有型別的程式碼都應該符合這些 linter 規則。
  被上傳至 [pub.dev]({{site.pub}}) 的 package，
  會有一個部分基於透過這些規則的情況而產生的 [package 評分]({{site.pub}}/help/scoring)。

Recommended rules
<br> Help identify additional issues
  that may lead to problems when running or consuming Dart code,
  and enforce a single, idiomatic style and format.
  We recommend that all Dart code use these rules,
  which are a superset of the core rules.

推薦規則
<br> 幫助確認其他可能導致執行或使用 Dart 程式碼時引發問題的事項，
  並強制使用單一、慣用的程式碼風格和程式碼格式化。
  作為一個核心規則的超集，我們推薦所有的 Dart 程式碼使用它。

{{site.alert.tip}}

  If you're working on Flutter code, then instead of using the `lints` package, 
  use [`flutter_lints`]({{site.pub-pkg}}/flutter_lints),
  which provides a superset of the recommended rules.

  如果你專注於 Flutter 程式碼，可以用 [`flutter_lints`]({{site.pub-pkg}}/flutter_lints)
  來取代 `lints` package，它提供了一個推薦規則的超集。

{{site.alert.end}}

To enable either set of lints,
add the [lints package][] as a dev dependency:

將 [lints package][] 作為 dev dependency 新增，
來啟用任意 lints 集合。

```terminal
$ dart pub add --dev lints
```

Then edit your `analysis_options.yaml` file to include
your preferred rule set:

然後編輯 `analysis_options.yaml` 檔案來引入你想要的規則集合：

```yaml
include: package:lints/<RULE_SET>.yaml
```

For example, you can include the recommended rule set like this:

例如，你可以像這樣引入推薦規則的集合：

```yaml
include: package:lints/recommended.yaml
```

{{site.alert.important}}

  When a **new version of `lints`** is published,
  code that previously passed analysis might **start failing analysis.**
  We recommend updating your code to work with the new rules.
  Other options are to explicitly enable individual linter rules 
  or [disable individual rules][].

  當一個 **新版本的 `lints`** 上線後，
  之前透過 analysis 檢查的程式碼，有可能 **開始無法透過**。
  我們推薦更新你的程式碼以符合新版規則。
  其他選項可以透過顯式的方法，啟用單條規則或 [停用單條規則][disable individual rules]。

{{site.alert.end}}

[lints package]: {{site.pub-pkg}}/lints

### Enabling individual rules {#individual-rules}

### 啟用單條規則 {#individual-rules}

To enable a single linter rule, add `linter:` to the analysis options file
as a top-level key,
followed by `rules:` as a second-level key.
On subsequent lines, specify the rules that you want to apply,
prefixed with dashes (the syntax for a YAML list).
For example:

在分析配置檔案中新增最上層 key `linter:` 來啟用單條規則，
緊跟著用 `rules:` 作為二級 key。
在後續行中，以短橫槓為字首（YAML 列表的語法），指定你想要新增的規則。
例如：

<?code-excerpt "analysis_options.yaml" from="linter:" take="12" remove="https:"?>
```yaml
linter:
  rules:
    - always_declare_return_types
    - cancel_subscriptions
    - close_sinks
    - comment_references
    - one_member_abstracts
    - only_throw_errors
    - package_api_docs
    - prefer_final_in_for_each
    - prefer_single_quotes
```


### Disabling individual rules

### 停用單條規則

If you include an analysis options file such as the one in `lints`,
you might want to disable some of the included rules.
Disabling individual rules is similar to enabling them,
but requires the use of a map rather than a list
as the value for the `rules:` entry,
so each line should contain the name of a rule followed by
either `: false` or `: true`.

如果你引入了一個分析配置檔案（比如 `lints` 中的某一個），
你可能會想要停用其中的一部分。
停用單條規則和啟用單條規則是類似的，
但要求使用鍵值對而不是列表來作為 `rules:` 的值。
因此每一行應該包括規則的名字，後面跟上 `: false` 或者 `: true`。

Here's an example of an analysis options file
that uses all the recommended rules from `lints`
except `avoid_shadowing_type_parameters`.
It also enables the lint `await_only_futures`:

這裡是一個分析配置檔案的範例，
其中使用了來自 `lints` 的所有推薦規則，
除了 `avoid_shadowing_type_parameters` 被單獨停用。
這裡同樣單獨啟用了 `await_only_futures` 這條 lint。

<?code-excerpt "analysis_alt/analysis_options_linter.yaml"?>
```yaml
include: package:lints/recommended.yaml

linter:
  rules:
    avoid_shadowing_type_parameters: false
    await_only_futures: true
```

{{site.alert.note}}

  Due to YAML restrictions, 
  **you can't mix list and key-value syntax in the same `rules` entry.**
  You can, however, use the other syntax for rules in an included file.

  由於 YAML 的限制，
  **你不能把列表和鍵值對在同一個 `rules` 入口下混用。**
  如果想這樣做，你只能在參考的檔案中使用另一種語法。

{{site.alert.end}}

## Excluding code from analysis

## 從 analysis 中排除程式碼

Sometimes it's OK for some code to fail analysis.
For example, you might rely on code generated by a package that
you don't own—the generated code works,
but produces warnings during static analysis.
Or a linter rule might cause a false positive
that you want to suppress.

有時候，部分程式碼可能允許包含分析出的警告和提示。
例如，你也許依賴於某個不屬於你 package 所產生的程式碼，
這些程式碼可以正常執行，但是在靜態檢查中會產生警告。
或者某個 linter 規則可能會出現你想關掉的誤報。

You have a few ways to exclude code from analysis:

有幾種方法可以從 analysis 中排除程式碼：

* Exclude entire files from analysis.

  從 analysis 中排除整個檔案。

* Stop specific non-error rules from being applied to individual files.

  在單個檔案中停止特定的非錯誤規則的生效。
  
* Stop specific non-error rules from being applied to individual lines of code.

  在單個檔案的某幾行中，停止特定的非錯誤規則的生效。

You can also [disable specific rules][disable individual rules]
for all files or
[change the severity of rules][].

你同樣可以對所有檔案 [停用特定的規則][disable individual rules]，
或者 [改變規則的警告等級][change the severity of rules]。

### Excluding files

### 排除檔案

To exclude files from static analysis, use the `exclude:` analyzer option. You
can list individual files, or use [glob]({{site.pub-pkg}}/glob) syntax:

使用分析器選項 `exclude:` 在靜態分析中排除檔案。
你可以列出單獨的檔案，或者用 [glob]({{site.pub-pkg}}/glob) 語法：

<?code-excerpt "analysis_alt/analysis_options.yaml (exclude)" plaster="none"?>
```yaml
analyzer:
  exclude:
    - lib/client.dart
    - lib/server/*.g.dart
    - test/_data/**
```


### Suppressing rules for a file

### 對單個檔案忽略規則

To ignore a specific non-error rule for a specific file,
add an `ignore_for_file` comment to the file:

透過在檔案中新增 `ignore_for_file` 註釋，
使得特定的非錯誤規則對該檔案忽略。

<?code-excerpt "analysis/lib/assignment.dart (ignore_for_file)" replace="/, \w+//g"?>
```dart
// ignore_for_file: unused_local_variable
```

This acts for the whole file, before or after the comment, and is
particularly useful for generated code.

該操作對整個檔案都生效，不論程式碼是在註釋之前還是之後，
對於產生的程式碼尤其有用。

To suppress more than one rule, use a comma-separated list:

使用逗號分隔的列表，可以忽略多條規則：

<?code-excerpt "analysis/lib/assignment.dart (ignore_for_file)"?>
```dart
// ignore_for_file: unused_local_variable, duplicate_ignore, dead_code
```

To suppress all linter rules, add a `type=lint` specifier:

新增 `type=lint` 以忽略所有的 linter 規則：

<?code-excerpt "analysis/lib/ignore_lints.dart (ignore_type_for_file)"?>
```dart
// ignore_for_file: type=lint
```

{{site.alert.version-note}}

  Support for the `type=lint` specifier was added in Dart 2.15.

  對 `type=lint` 的支援從 Dart 2.15 起被加入。

{{site.alert.end}}


### Suppressing rules for a line of code

### 對一行程式碼忽略規則

To suppress a specific non-error rule on a specific line of code,
put an `ignore` comment
above the line of code. 
Here's an example of ignoring code that causes a runtime error, 
as you might do in a language test:

透過在單行程式碼的上方新增 `ignore` 註釋，
使得特定的非錯誤規則對該行程式碼忽略。
這是一個忽略程式碼導致執行時錯誤的例子，
你可能會在一個開發語言的測試上使用。

<?code-excerpt "analysis/lib/assignment.dart (invalid_assignment)"?>
```dart
// ignore: invalid_assignment
int x = '';
```

To suppress more than one rule, supply a comma-separated list:

使用逗號分隔的列表，可以忽略多條規則：

<?code-excerpt "analysis/lib/assignment.dart (ignore more)"?>
```dart
// ignore: invalid_assignment, const_initialized_with_non_constant_value
const x = y;
```

Alternatively, append the ignore rule to the line that it applies to:

或者，將需要忽略的規則追加到相應的行後：

<?code-excerpt "analysis/lib/assignment.dart (single-line)"?>
```dart
int x = ''; // ignore: invalid_assignment
```


## Customizing analysis rules

## 自訂 analysis 規則

Each [analyzer error code][analyzer error codes] and
[linter rule][linter rules] has a default severity.
You can use the analysis options file to change
the severity of individual rules, or to always ignore some rules.

每個 [分析器錯誤碼][analyzer error codes] 和 [linter 規則][linter rules] 
都有一個預設的警告等級。
你可以使用分析配置檔案來改變單個規則的警告等級，
或者總是忽略某些規則。

The analyzer supports three severity levels:

分析器支援三種警告等級：

`info`
<br> An informational message that doesn't cause analysis to fail.
  Example: [`dead_code`][dead_code]

`info`
<br> 訊息，不會造成 analysis 驗證失敗。
  例如：[`dead_code`][dead_code]

`warning`
<br> A warning that doesn't cause analysis to fail unless
  the analyzer is configured to treat warnings as errors.
  Example: [`invalid_null_aware_operator`][invalid_null_aware_operator]

`warning`
<br> 警告，一般不會造成 analysis 驗證失敗，
  除非分析器被配置了對待警告與錯誤一致。
  例如：[`invalid_null_aware_operator`][invalid_null_aware_operator]

`error`
<br> An error that causes analysis to fail.
  Example: [`invalid_assignment`][invalid_assignment]

`error`
<br> 錯誤，會造成 analysis 驗證失敗。
  例如：[`invalid_assignment`][invalid_assignment]

### Ignoring rules

### 忽略規則

You can ignore specific [analyzer error codes][] and [linter rules][]
by using the `errors:` field.
List the rule, followed by <code>:&nbsp;ignore</code>. For example, the following
analysis options file instructs the analysis tools to ignore the TODO rule:

你可以透過使用 `errors:` 欄位，
來忽略特定的 [分析器錯誤碼][analyzer error codes] and [linter 規則][linter rules]。
列出規則，在後面加上 <code>:&nbsp;ignore</code>。
例如下方的分析配置檔案，指示分析器工具忽略了 TODO 規則：

<?code-excerpt "analysis_alt/analysis_options.yaml (errors)" to="ignore"?>
```yaml
analyzer:
  errors:
    todo: ignore
```


### Changing the severity of rules

### 修改規則的警告等級

You can globally change the severity of a particular rule.
This technique works for regular analysis issues as well as for lints.
For example, the following analysis options file instructs the analysis tools to
treat invalid assignments as warnings and missing returns as errors,
and to provide information (but not a warning or error) about dead code:

你可以全域修改單個規則的警告等級。
這項技術對於常規的 analysis 問題和 lints 問題都有效。
例如下方的分析配置檔案，指示分析器工具把無效的賦值配置為警告，
把缺少返回值配置為錯誤，而對於無法執行到的程式碼，僅提供並非警告和錯誤的資訊通知。

<?code-excerpt "analysis_alt/analysis_options.yaml (errors)" remove="ignore"?>
```yaml
analyzer:
  errors:
    invalid_assignment: warning
    missing_return: error
    dead_code: info
```


## Resources

## 更多資源

Use the following resources to learn more about static analysis in Dart:

你還可以透過以下資源來深入瞭解 Dart 的靜態分析：

* [Dart's type system][type-system]
* [Dart linter](https://github.com/dart-lang/linter#linter-for-dart)
* [Dart linter rules][linter rules]
* [analyzer package]({{site.pub-pkg}}/analyzer)

[invalid_null_aware_operator]: /tools/diagnostic-messages#invalid_null_aware_operator
[analyzer error codes]: https://github.com/dart-lang/sdk/blob/main/pkg/analyzer/lib/error/error.dart
[change the severity of rules]: #changing-the-severity-of-rules
[diagnostics]: /tools/diagnostic-messages
[invalid_assignment]: /tools/diagnostic-messages#invalid_assignment
[language version]: /guides/language/evolution#language-versioning
[linter rules]: /tools/linter-rules
[type-system]: /guides/language/type-system
[dead_code]: /tools/diagnostic-messages#dead_code
[disable individual rules]: #disabling-individual-rules
[Effective Dart]: /guides/language/effective-dart
