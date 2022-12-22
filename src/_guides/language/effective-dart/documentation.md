---
title: "Effective Dart: Documentation"
title: 高效 Dart 語言指南：文件
description: Clear, helpful comments and documentation.
description: 簡潔實用的註釋以及文件。
nextpage:
  url: /guides/language/effective-dart/usage
  title: Usage
  title: 用法範例
prevpage:
  url: /guides/language/effective-dart/style
  title: Style
  title: 程式碼風格
---

<?code-excerpt path-base="misc/lib/effective_dart"?>

It's easy to think your code is obvious today without realizing how much you
rely on context already in your head. People new to your code, and
even your forgetful future self won't have that context. A concise, accurate
comment only takes a few seconds to write but can save one of those people
hours of time.

你可能沒有意識到，今天你很容易想出來的程式碼，有多麼依賴你當時思路。
人們不熟悉你的程式碼，甚至你也忘記了當時程式碼功能中有這樣的思路。
寫下簡明扼要的註釋只需要幾秒鐘，但可以讓看這段程式碼的每個人節約幾個小時。

We all know code should be self-documenting and not all comments are helpful.
But the reality is that most of us don't write as many comments as we should.
It's like exercise: you technically *can* do too much, but it's a lot more
likely that you're doing too little. Try to step it up.

我們知道程式碼應該自文件（self-documenting），並不是所有的註釋都是有用的。
但實際情況是，我們大多數人都沒有寫出儘可能多的註釋。
和練習一樣：從技術上你*可以*做很多，但是實際上你
只做了一點點。嘗試著逐步提高。

## Comments

## 註釋

The following tips apply to comments that you don't want included in the
generated documentation.

下面的提示適用於不被產生在文件中的註釋。

### DO format comments like sentences.

### **要** 像句子一樣來格式化註釋。

{:.good}
<?code-excerpt "docs_good.dart (comments-like-sentences)"?>
{% prettify dart tag=pre+code %}
// Not if there is nothing before it.
if (_chunks.isEmpty) return false;
{% endprettify %}

Capitalize the first word unless it's a case-sensitive identifier. End it with a
period (or "!" or "?", I suppose). This is true for all comments: doc comments,
inline stuff, even TODOs. Even if it's a sentence fragment.

如果第一個單詞不是大小寫相關的識別符號，則首字母要大寫。
使用句號，歎號或者問號結尾。所有的註釋都應該這樣：
文件註釋，單行註釋，甚至 TODO。即使它是一個句子的片段。

### DON'T use block comments for documentation.

### **不要** 使用塊註釋作用作解釋說明。

{:.good}
<?code-excerpt "docs_good.dart (block-comments)"?>
{% prettify dart tag=pre+code %}
void greet(String name) {
  // Assume we have a valid name.
  print('Hi, $name!');
}
{% endprettify %}

{:.bad}
<?code-excerpt "docs_bad.dart (block-comments)"?>
{% prettify dart tag=pre+code %}
void greet(String name) {
  /* Assume we have a valid name. */
  print('Hi, $name!');
}
{% endprettify %}

You can use a block comment (`/* ... */`) to temporarily comment out a section
of code, but all other comments should use `//`.

可以使用塊註釋 (`/* ... */`) 來臨時的註釋掉一段程式碼，
但是其他的所有註釋都應該使用 `//`。

## Doc comments

## 文件註釋

Doc comments are especially handy because [`dart doc`][] parses them
and generates [beautiful doc pages][docs] from them.
A doc comment is any comment that appears before a declaration
and uses the special `///` syntax that `dart doc` looks for.

文件註釋特別有用，應為透過 [`dart doc`][] 解析這些註釋可以產生 [漂亮的文件網頁][docs]。
文件註釋包括所有出現在宣告之前並使用 `///` 語法的註釋，這些註釋使用使用 dartdoc 檢索。

[`dart doc`]: /tools/dart-doc
[docs]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}

### DO use `///` doc comments to document members and types.

### **要** 使用 `///` 文件註釋來註釋成員和型別。

{% include linter-rule-mention.md rule="slash_for_doc_comments" %}

Using a doc comment instead of a regular comment enables 
[`dart doc`][] to find it
and generate documentation for it.

使用文件註釋可以讓 [dartdoc][] 來為你產生程式碼 API 文件。

{:.good}
<?code-excerpt "docs_good.dart (use-doc-comments)"?>
{% prettify dart tag=pre+code %}
/// The number of characters in this chunk when unsplit.
int get length => ...
{% endprettify %}

{:.bad}
<?code-excerpt "docs_good.dart (use-doc-comments)" replace="/^\///g"?>
{% prettify dart tag=pre+code %}
// The number of characters in this chunk when unsplit.
int get length => ...
{% endprettify %}

For historical reasons, `dart doc` supports two syntaxes of doc comments: `///`
("C# style") and `/** ... */` ("JavaDoc style"). We prefer `///` because it's
more compact. `/**` and `*/` add two content-free lines to a multiline doc
comment. The `///` syntax is also easier to read in some situations, such as
when a doc comment contains a bulleted list that uses `*` to mark list items.

由於歷史原因，dartdoc 支援兩種格式的文件註釋：`///` ("C# 格式") 和 `/** ... */` ("JavaDoc 格式")。
我們推薦使用 `///` 是因為其更加簡潔。
`/**` 和 `*/` 在多行註釋中間添加了開頭和結尾的兩行多餘內容。
`///` 在一些情況下也更加易於閱讀，例如，當文件註釋中包含有使用 `*` 標記的列表內容的時候。

If you stumble onto code that still uses the JavaDoc style, consider cleaning it
up.

如果你現在還在使用 JavaDoc 風格格式，請考慮使用新的格式。

### PREFER writing doc comments for public APIs.

### **推薦** 為公開發布的 API 編寫文件註釋。

{% include linter-rule-mention.md rule1="package_api_docs" rule2="public_member_api_docs"%}

You don't have to document every single library, top-level variable, type, and
member, but you should document most of them.

不必為所有獨立的函式庫，最上層變數，型別以及成員編寫文件註釋。
但是它們大多數應該有文件註釋。

### CONSIDER writing a library-level doc comment.

### **考慮** 編寫庫級別（library-level）的文件註釋。

Unlike languages like Java where the class is the only unit of program
organization, in Dart, a library is itself an entity that users work with
directly, import, and think about. That makes the `library` directive a great
place for documentation that introduces the reader to the main concepts and
functionality provided within. Consider including:

與Java等類似的語言不同，Java 中類是程式組織的唯一單元。
在 Dart 中，庫本身就是一個實體，使用者可以直接使用，匯入及構思它。
這使得`庫`成為一個展示文件的好地方。
這樣可以向讀者介紹其中提供的主要概念和功能。
其中可以考慮包括下列內容：

* A single-sentence summary of what the library is for.

  關於庫用途的單句摘要。

* Explanations of terminology used throughout the library.
  
  解釋庫中用到的術語。

* A couple of complete code samples that walk through using the API.

  一些配合 API 的範例程式碼。

* Links to the most important or most commonly used classes and functions.

  最重要和最常用的類和函式的連結。

* Links to external references on the domain the library is concerned with.

  和庫相關領域的外部連結。

You document a library by placing a doc comment right above the `library`
directive at the start of the file. If the library doesn't have a `library`
directive, you can add one just to hang the doc comment off of it.

你可以透過在檔案開頭的 `library` 的上方放置 doc 註釋來文件註釋一個函式庫。
如果庫沒有 `library` 指令，您可以新增一個，只是掛起 doc 註釋。

### CONSIDER writing doc comments for private APIs.

### **推薦** 為私有API 編寫文件註釋。

Doc comments aren't just for external consumers of your library's public API.
They can also be helpful for understanding private members that are called from
other parts of the library.

文件註釋不僅僅適用於外部使用者使用你庫的公開 API.
它也同樣有助於理解那些私有成員，這些私有成員會被庫的其他部分呼叫。

### DO start doc comments with a single-sentence summary.

### **要** 要在文件註釋開頭有一個單句總結。

Start your doc comment with a brief, user-centric description ending with a
period. A sentence fragment is often sufficient. Provide just enough context for
the reader to orient themselves and decide if they should keep reading or look
elsewhere for the solution to their problem.

註釋文件要以一個以使用者為中心，簡要的描述作為開始。
通常句子片段就足夠了。為讀者提供足夠的上下文來定位自己，
並決定是否應該繼續閱讀，或尋找其他解決問題的方法。

{:.good}
<?code-excerpt "docs_good.dart (first-sentence)"?>
{% prettify dart tag=pre+code %}
/// Deletes the file at [path] from the file system.
void delete(String path) {
  ...
}
{% endprettify %}

{:.bad}
<?code-excerpt "docs_bad.dart (first-sentence)"?>
{% prettify dart tag=pre+code %}
/// Depending on the state of the file system and the user's permissions,
/// certain operations may or may not be possible. If there is no file at
/// [path] or it can't be accessed, this function throws either [IOError]
/// or [PermissionError], respectively. Otherwise, this deletes the file.
void delete(String path) {
  ...
}
{% endprettify %}

### DO separate the first sentence of a doc comment into its own paragraph.

### **要** 讓文件註釋的第一句從段落中分開。 

Add a blank line after the first sentence to split it out into its own
paragraph. If more than a single sentence of explanation is useful, put the
rest in later paragraphs.

在第一句之後新增一個空行，將其拆分為自己的段落。 
如果不止一個解釋句子有用，請將其餘部分放在後面的段落中。

This helps you write a tight first sentence that summarizes the documentation.
Also, tools like `dart doc` use the first paragraph as a short summary in places
like lists of classes and members.

這有助於您編寫一個緊湊的第一句話來總結文件。 
此外，像Dartdoc這樣的工具使用第一段作為類和類成員列表等地方的簡短摘要。

{:.good}
<?code-excerpt "docs_good.dart (first-sentence-a-paragraph)"?>
{% prettify dart tag=pre+code %}
/// Deletes the file at [path].
///
/// Throws an [IOError] if the file could not be found. Throws a
/// [PermissionError] if the file is present but could not be deleted.
void delete(String path) {
  ...
}
{% endprettify %}

{:.bad}
<?code-excerpt "docs_bad.dart (first-sentence-a-paragraph)"?>
{% prettify dart tag=pre+code %}
/// Deletes the file at [path]. Throws an [IOError] if the file could not
/// be found. Throws a [PermissionError] if the file is present but could
/// not be deleted.
void delete(String path) {
  ...
}
{% endprettify %}

### AVOID redundancy with the surrounding context.

### **避免** 與周圍上下文冗餘。

The reader of a class's doc comment can clearly see the name of the class, what
interfaces it implements, etc. When reading docs for a member, the signature is
right there, and the enclosing class is obvious. None of that needs to be
spelled out in the doc comment. Instead, focus on explaining what the reader
*doesn't* already know.

閱讀類別的文件註釋的可以清楚地看到類別的名稱，它實現的介面等等。
當讀取成員的文件時，命名和封裝它的類是顯而易見的。 
這些都不需要寫在文件註釋中。 
相反，應該專注於解釋讀者所*不知道*的內容。

{:.good}
<?code-excerpt "docs_good.dart (redundant)"?>
{% prettify dart tag=pre+code %}
class RadioButtonWidget extends Widget {
  /// Sets the tooltip to [lines], which should have been word wrapped using
  /// the current font.
  void tooltip(List<String> lines) {
    ...
  }
}
{% endprettify %}

{:.bad}
<?code-excerpt "docs_bad.dart (redundant)"?>
{% prettify dart tag=pre+code %}
class RadioButtonWidget extends Widget {
  /// Sets the tooltip for this radio button widget to the list of strings in
  /// [lines].
  void tooltip(List<String> lines) {
    ...
  }
}
{% endprettify %}

If you really don't have anything interesting to say
that can't be inferred from the declaration itself,
then omit the doc comment.
It's better to say nothing
than waste a reader's time telling them something they already know.


### PREFER starting function or method comments with third-person verbs.

### **推薦** 用第三人稱來開始函式或者方法的文件註釋。

The doc comment should focus on what the code *does*.

註釋應該關注於程式碼 *所實現的* 功能。

{:.good}
<?code-excerpt "docs_good.dart (third-person)"?>
{% prettify dart tag=pre+code %}
/// Returns `true` if every element satisfies the [predicate].
bool all(bool predicate(T element)) => ...

/// Starts the stopwatch if not already running.
void start() {
  ...
}
{% endprettify %}

### PREFER starting a non-boolean variable or property comment with a noun phrase.

### **推薦** 使用名詞短語來為非布林值變數或屬性註釋。

The doc comment should stress what the property *is*. This is true even for
getters which may do calculation or other work. What the caller cares about is
the *result* of that work, not the work itself.

註釋文件應該表述這個屬性*是*什麼。雖然 getter 函式會做些計算，
但是也要求這樣，呼叫者關心的是其*結果*而不是如何計算的。

{:.good}
<?code-excerpt "docs_good.dart (noun-phrases-for-non-boolean-var-etc)"?>
{% prettify dart tag=pre+code %}
/// The current day of the week, where `0` is Sunday.
int weekday;

/// The number of checked buttons on the page.
int get checkedCount => ...
{% endprettify %}

### PREFER starting a boolean variable or property comment with "Whether" followed by a noun or gerund phrase.

The doc comment should clarify the states this variable represents. 
This is true even for getters which may do calculation or other work. 
What the caller cares about is the *result* of that work, not the work itself.

{:.good}
<?code-excerpt "docs_good.dart (noun-phrases-for-boolean-var-etc)"?>
{% prettify dart tag=pre+code %}
/// Whether the modal is currently displayed to the user.
bool isVisible;

/// Whether the modal should confirm the user's intent on navigation.
bool get shouldConfirm => ...

/// Whether resizing the current browser window will also resize the modal.
bool get canResize => ...
{% endprettify %}

{{site.alert.note}}
  This guideline intentionally doesn't include using "Whether or not". In many
  cases, usage of "or not" with "whether" is superfluous and can be omitted,
  especially when used in this context.
{{site.alert.end}}

### DON'T write documentation for both the getter and setter of a property.

If a property has both a getter and a setter, then create a doc comment for
only one of them. `dart doc` treats the getter and setter like a single field,
and if both the getter and the setter have doc comments, then
`dart doc` discards the setter's doc comment.

如果一個屬性同時包含 getter 和 setter，請只為其中一個新增文件。
`dart doc` 命令會將 getter 和 setter 作為同一個屬性進行處理，
而如果它們都包含文件註釋，`dart docs` 命令會將 setter 的文件忽略。

{:.good}
<?code-excerpt "docs_good.dart (getter-and-setter)"?>
{% prettify dart tag=pre+code %}
/// The pH level of the water in the pool.
///
/// Ranges from 0-14, representing acidic to basic, with 7 being neutral.
int get phLevel => ...
set phLevel(int level) => ...
{% endprettify %}

{:.bad}
<?code-excerpt "docs_bad.dart (getter-and-setter)"?>
{% prettify dart tag=pre+code %}
/// The depth of the water in the pool, in meters.
int get waterDepth => ...

/// Updates the water depth to a total of [meters] in height.
set waterDepth(int meters) => ...
{% endprettify %}

### PREFER starting library or type comments with noun phrases.

### **推薦** 使用名詞短語來開始庫和型別註釋。

Doc comments for classes are often the most important documentation in your
program. They describe the type's invariants, establish the terminology it uses,
and provide context to the other doc comments for the class's members. A little
extra effort here can make all of the other members simpler to document.

在程式中，類別的註釋通常是最重要的文件。
類別的註釋描述了型別的不變性、介紹其使用的術語、
提供類成員使用的上下文資訊。為類提供一些註釋可以讓
其他類成員的註釋更易於理解和編寫。

{:.good}
<?code-excerpt "docs_good.dart (noun-phrases-for-type-or-lib)"?>
{% prettify dart tag=pre+code %}
/// A chunk of non-breaking output text terminated by a hard or soft newline.
///
/// ...
class Chunk { ... }
{% endprettify %}

### CONSIDER including code samples in doc comments.

### **考慮** 在文件註釋中新增範例程式碼。

{:.good}
<?code-excerpt "docs_good.dart (code-sample)"?>
{% prettify dart tag=pre+code %}
/// Returns the lesser of two numbers.
///
/// ```dart
/// min(5, 3) == 3
/// ```
num min(num a, num b) => ...
{% endprettify %}

Humans are great at generalizing from examples, so even a single code sample
makes an API easier to learn.

人類非常擅長從範例中抽象出實質內容，所以即使提供
一行最簡單的範例程式碼都可以讓 API 更易於理解。

### DO use square brackets in doc comments to refer to in-scope identifiers.

### **要** 使用方括號在文件註釋中參考作用域內的識別符號

{% include linter-rule-mention.md rule="comment_references" %}

If you surround things like variable, method, or type names in square brackets,
then `dart doc` looks up the name and links to the relevant API docs.
Parentheses are optional, 
but can make it clearer when you're referring to a method or constructor.

如果給變數，方法，或型別等名稱加上方括號，則 dartdoc 會查詢名稱並連結到相關的 API 文件。
括號是可選的，但是當你在參考一個方法或者建構函式時，可以讓註釋更清晰。

{:.good}
<?code-excerpt "docs_good.dart (identifiers)"?>
{% prettify dart tag=pre+code %}
/// Throws a [StateError] if ...
/// similar to [anotherMethod()], but ...
{% endprettify %}

To link to a member of a specific class, use the class name and member name,
separated by a dot:

要連結到特定類別的成員，請使用以點號分割的類別名稱和成員名：

{:.good}
<?code-excerpt "docs_good.dart (member)"?>
{% prettify dart tag=pre+code %}
/// Similar to [Duration.inDays], but handles fractional days.
{% endprettify %}

The dot syntax can also be used to refer to named constructors. For the unnamed
constructor, use `.new` after the class name:

點語法也可用於參考命名建構函式。
對於未命名的建構函式，在類別名稱後面加上 `.new` 來使用預設構造：

{:.good}
<?code-excerpt "docs_good.dart (ctor)"?>
{% prettify dart tag=pre+code %}
/// To create a point, call [Point.new] or use [Point.polar] to ...
{% endprettify %}

### DO use prose to explain parameters, return values, and exceptions.

### **要** 使用平白簡單的陳述式來描述引數、返回值以及例外資訊。

Other languages use verbose tags and sections to describe what the parameters
and returns of a method are.

其他語言使用各種標籤和額外的註釋來描述引數和返回值。

{:.bad}
<?code-excerpt "docs_bad.dart (no-annotations)"?>
{% prettify dart tag=pre+code %}
/// Defines a flag with the given name and abbreviation.
///
/// @param name The name of the flag.
/// @param abbr The abbreviation for the flag.
/// @returns The new flag.
/// @throws ArgumentError If there is already an option with
///     the given name or abbreviation.
Flag addFlag(String name, String abbr) => ...
{% endprettify %}

The convention in Dart is to integrate that into the description of the method
and highlight parameters using square brackets.

Dart 的慣例是將其整合到方法的描述中，使用方括號高亮並標記引數。

{:.good}
<?code-excerpt "docs_good.dart (no-annotations)"?>
{% prettify dart tag=pre+code %}
/// Defines a flag.
///
/// Throws an [ArgumentError] if there is already an option named [name] or
/// there is already an option using abbreviation [abbr]. Returns the new flag.
Flag addFlag(String name, String abbr) => ...
{% endprettify %}

### DO put doc comments before metadata annotations.

### **要** 把註釋文件放到註解之前。

{:.good}
<?code-excerpt "docs_good.dart (doc-before-meta)"?>
{% prettify dart tag=pre+code %}
/// A button that can be flipped on and off.
@Component(selector: 'toggle')
class ToggleComponent {}
{% endprettify %}

{:.bad}
<?code-excerpt "docs_bad.dart (doc-before-meta)" replace="/\n\n/\n/g"?>
{% prettify dart tag=pre+code %}
@Component(selector: 'toggle')
/// A button that can be flipped on and off.
class ToggleComponent {}
{% endprettify %}


## Markdown

You are allowed to use most [markdown][] formatting in your doc comments and
`dart doc` will process it accordingly using the [markdown package.][]

文件註釋中允許使用大多數 [markdown][] 格式，
並且 dartdoc 會根據 [markdown package.][] 進行解析。

[markdown]: https://daringfireball.net/projects/markdown/
[markdown package.]: {{site.pub-pkg}}/markdown

There are tons of guides out there already to introduce you to Markdown. Its
universal popularity is why we chose it. Here's just a quick example to give you
a flavor of what's supported:

有很多指南已經向您介紹Markdown。 它普遍受歡迎是我們選擇它的原因。 這裡只是一個簡單的例子，讓您瞭解所支援的內容：

<?code-excerpt "docs_good.dart (markdown)"?>
{% prettify dart tag=pre+code %}
/// This is a paragraph of regular text.
///
/// This sentence has *two* _emphasized_ words (italics) and **two**
/// __strong__ ones (bold).
///
/// A blank line creates a separate paragraph. It has some `inline code`
/// delimited using backticks.
///
/// * Unordered lists.
/// * Look like ASCII bullet lists.
/// * You can also use `-` or `+`.
///
/// 1. Numbered lists.
/// 2. Are, well, numbered.
/// 1. But the values don't matter.
///
///     * You can nest lists too.
///     * They must be indented at least 4 spaces.
///     * (Well, 5 including the space after `///`.)
///
/// Code blocks are fenced in triple backticks:
///
/// ```dart
/// this.code
///     .will
///     .retain(its, formatting);
/// ```
///
/// The code language (for syntax highlighting) defaults to Dart. You can
/// specify it by putting the name of the language after the opening backticks:
///
/// ```html
/// <h1>HTML is magical!</h1>
/// ```
///
/// Links can be:
///
/// * https://www.just-a-bare-url.com
/// * [with the URL inline](https://google.com)
/// * [or separated out][ref link]
///
/// [ref link]: https://google.com
///
/// # A Header
///
/// ## A subheader
///
/// ### A subsubheader
///
/// #### If you need this many levels of headers, you're doing it wrong
{% endprettify %}

### AVOID using markdown excessively.

### **避免** 過度使用 markdown。

When in doubt, format less. Formatting exists to illuminate your content, not
replace it. Words are what matter.

如果有格式缺少的問題，格式化已有的內容來闡明你的想法，而不是替換它，
內容才是最重要的。

### AVOID using HTML for formatting.

### **避免** 使用 HTML 來格式化文字。

It *may* be useful to use it in rare cases for things like tables, but in almost
all cases, if it's too complex to express in Markdown, you're better off not
expressing it.

例如表格，在極少數情況下它*可能*很有用。
但幾乎所有的情況下，在 Markdown 中表格的表示都非常複雜。
這種情況下最好不要使用表格。

### PREFER backtick fences for code blocks.

### **推薦** 使用反引號標註程式碼。

Markdown has two ways to indicate a block of code: indenting the code four
spaces on each line, or surrounding it in a pair of triple-backtick "fence"
lines. The former syntax is brittle when used inside things like Markdown lists
where indentation is already meaningful or when the code block itself contains
indented code.

Markdown 有兩種方式來標註一塊程式碼：
每行程式碼縮排4個空格，或者在程式碼上下各標註三個反引號。
當縮排已經包含其他意義，或者程式碼段自身就包含縮排時，
在 Markdown 中使用前一種語法就顯得很脆弱。

The backtick syntax avoids those indentation woes, lets you indicate the code's
language, and is consistent with using backticks for inline code.

反引號語法避免了那些縮排的問題，
而且能夠指出程式碼的語言型別，
內聯程式碼同樣可以使用反引號標註。

{:.good}
{% prettify dart tag=pre+code %}
/// You can use [CodeBlockExample] like this:
///
/// ```dart
/// var example = CodeBlockExample();
/// print(example.isItGreat); // "Yes."
/// ```
{% endprettify %}

{:.bad}
{% prettify dart tag=pre+code %}
/// You can use [CodeBlockExample] like this:
///
///     var example = CodeBlockExample();
///     print(example.isItGreat); // "Yes."
{% endprettify %}

## Writing

## 如何寫註釋

We think of ourselves as programmers, but most of the characters in a source
file are intended primarily for humans to read. English is the language we code
in to modify the brains of our coworkers. As for any programming language, it's
worth putting effort into improving your proficiency.

雖然我們認為我們是程式設計師，但是大部分情況下原始碼中的內容都是為了讓人類更易於閱讀和理解程式碼。
對於任何程式語言，都值得努力練習來提高熟練程度。

This section lists a few guidelines for our docs. You can learn more about
best practices for technical writing, in general, from articles such as
[Technical writing style](https://en.wikiversity.org/wiki/Technical_writing_style).

### PREFER brevity.

### **推薦** 簡潔.

Be clear and precise, but also terse.

要清晰和準確，並且簡潔。

### AVOID abbreviations and acronyms unless they are obvious.

### **避免** 縮寫和首字母縮寫詞，除非很常見。

Many people don't know what "i.e.", "e.g." and "et al." mean. That acronym
that you're sure everyone in your field knows may not be as widely known as you
think.

很多人都不知道 "i.e."、 "e.g." 和 "et. al." 的意思。
你所在領域的首字母縮略詞對於其他人可能並不瞭解。

### PREFER using "this" instead of "the" to refer to a member's instance.

### **推薦** 使用 "this" 而不是 "the" 來參考例項成員。

When documenting a member for a class, you often need to refer back to the
object the member is being called on. Using "the" can be ambiguous.

註釋中提及到類別的成員，通常是指被呼叫的物件例項的成員。
使用 "the" 可能會導致混淆。

{:.good}
<?code-excerpt "docs_good.dart (this)"?>
{% prettify dart tag=pre+code %}
class Box {
  /// The value this wraps.
  Object? _value;

  /// True if this box contains a value.
  bool get hasValue => _value != null;
}
{% endprettify %}
