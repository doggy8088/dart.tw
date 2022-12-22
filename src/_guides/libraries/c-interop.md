---
title: "C interop using dart:ffi"
title: "使用 dart:ffi 與 C 進行互動"
description: "To use C code in your Dart program, use the dart:ffi library."
description: "在你的 Dart 程式中使用 dart:ffi 庫呼叫 C 語言的程式碼。"
hw: "https://github.com/dart-lang/samples/tree/master/ffi/hello_world"
samples: "https://github.com/dart-lang/samples/tree/master/ffi"
---

Dart mobile, command-line, and server apps 
running on the [Dart Native platform](/overview#platform) 
can use the `dart:ffi` library to call native C APIs,
and to read, write, allocate, and deallocate native memory.
_FFI_ stands for [_foreign function interface._][FFI]
Other terms for similar functionality include
_native interface_ and _language bindings._

Dart 的移動端、命令列和伺服器端應用所執行的 [Dart 原生平台](/overview#platform)，
均可以使用 `dart:ffi` 庫呼叫原生的 C 語言 API，用於讀、寫、分配和銷燬原生記憶體。
**FFI** 指的是 [**外部函式介面**][FFI]。
類似的術語包括 **原生介面** 和 **語言繫結**。

API documentation is available in the
[`dart:ffi` API reference.]({{site.dart-api}}/dart-ffi/dart-ffi-library.html)

相關的 API 文件可在
[`dart:ffi` API 文件]({{site.dart_api}}/dart-ffi/dart-ffi-library.html)
檢視。

## Examples

## 範例

The following examples show how to use the `dart:ffi` library:

以下的範例將展示如何使用 `dart:ffi` 庫：

| **Example**     | **Description**                                                                                                             |
| **範例**        | **描述**                                                                                                                     |
| [hello_world][] | How to call a C function with no arguments and no return value.                                                             |
| [hello_world][] | 如何呼叫無引數和返回值的 C 語言函式。                                                                                           |
| [primitives][]  | How to call C functions that have arguments and return values that are **ints or pointers**. Also demonstrates **varargs**. |
| [primitives][]  | 如何呼叫引數和返回值為 **整型和指標** 的 C 語言函式。同時示範 **varargs**。                                                        |
| [structs][]     | How to use structs to pass **strings** to and from C and to handle **simple and complex C structures**.                     |
| [structs][]     | 如何與 C 語言互相傳遞字串，以及如何處理 **C 語言定義的結構**。                                                                   |
| [sqlite][]      | An example in the Dart SDK repo that comes with a [mini tutorial.][]                                                        |
| [sqlite][]      | Dart SDK 儲存庫中包含的 [小型範例][mini tutorial.]。                                                                             |

## Walkthrough of hello_world

## 快速上手的 hello_world

The [hello_world example][hello_world] has the minimum necessary code
for calling a C library.

[hello_world 範例][hello_world] 展示瞭如何用最少的程式碼呼叫 C 語言庫。

### Files

### 檔案

The hello_world example has the following files:

hello_world 範例包含了以下檔案：

| **Source file** | **Description** |
| **原始檔** | **描述** |
| [hello.dart]({{page.hw}}/hello.dart) | A Dart file that uses the `hello_world()` function from a C library. |
| [hello.dart]({{page.hw}}/hello.dart) | 使用了 C 語言庫中的 `hello_world()` 函式的檔案。 |
| [pubspec.yaml]({{page.hw}}/pubspec.yaml) | The usual Dart [pubspec](/tools/pub/pubspec), with a lower bounds on the SDK that's at least 2.6. |
| [pubspec.yaml]({{page.hw}}/pubspec.yaml) | Dart 裡常見的 [pubspec 檔案](/tools/pub/pubspec)，最低 SDK 限制為 2.6。 |
| [hello_library/hello.h]({{page.hw}}/hello_library/hello.h) | Declares the `hello_world()` function. |
| [hello_library/hello.h]({{page.hw}}/hello_library/hello.h) | 聲明瞭 `hello_world()` 函式。 |
| [hello_library/hello.c]({{page.hw}}/hello_library/hello.c) | A C file that imports `hello.h` and defines the `hello_world()` function. |
| [hello_library/hello.c]({{page.hw}}/hello_library/hello.c) | 該 C 檔案匯入了 `hello.h` 並實現了 `hello_world()` 函式。 |
| [hello_library/hello.def]({{page.hw}}/hello_library/hello.def) | A module-definition file which specifies information used when building a DLL. |
| [hello_library/hello.def]({{page.hw}}/hello_library/hello.def) | 包含 DLL 建構資訊的模組定義。 |
| [hello_library/CMakeLists.txt]({{page.hw}}/hello_library/CMakeLists.txt) | A CMake build file for compiling the C code into a dynamic library. |
| [hello_library/CMakeLists.txt]({{page.hw}}/hello_library/CMakeLists.txt) | 將 C 檔案程式碼編譯為動態庫的 CMake 檔案。 |
{:.table .table-striped }

{% comment %}
[PENDING: say something about other files, like setup.sh?]
[TODO (https://github.com/dart-lang/site-www/issues/2219): Fix build instructions.]
{% endcomment %}

Building the C library creates several files,
including a dynamic library file named
`libhello.dylib` (macOS), 
`libhello.dll` (Windows), or
`libhello.so` (Linux).

建構 C 程式碼庫時將建立幾個檔案，包括動態庫
`libhello.dylib`（僅 macOS）、
`libhello.dll`（僅 Windows）
或 `libhello.so`（僅 Linux）。

### Building and running

### 建構並執行

Here's an example of building the dynamic library and executing the Dart app:

以下是建構動態庫並執行 Dart 應用的範例：

```terminal
$ cd hello_library
$ cmake .
...
$ make
...
$ cd ..
$ dart pub get
$ dart run hello.dart
Hello World
```

{{site.alert.info}}

  **On macOS,** executables, including the Dart VM (`dart`),
  can load only **signed libraries.**
  For more information on signing libraries, 
  see Apple's [Code Signing Guide.][codesign]

  **在 macOS 上，** 包括 Dart VM (`dart`)
  在內的可執行檔案只能載入 **已簽名的函式庫。**
  若想了解更多細節和解決方案，請檢視 [簽名指南][codesign]。

{{site.alert.end}}

[codesign]: https://developer.apple.com/library/content/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html
  

### Using dart:ffi

### 使用 dart:ffi

The [`hello.dart` file]({{page.hw}}/hello.dart)
illustrates the steps for using `dart:ffi` to call a C function:

[`hello.dart` 檔案]({{page.hw}}/hello.dart)
闡述了使用 dart:ffi 呼叫 C 函式的步驟：

1. Import `dart:ffi`.

   匯入 `dart:ffi`。

2. Import the path library that you'll use to store the path of dynamic library.

   匯入 `path` 庫用於合成動態庫的路徑。

3. Create a typedef with the FFI type signature of the C function.

   為 C 函式的 FFI 型別簽名的定義一個類別型。

4. Create a typedef for the variable that you'll use when calling the C function.

   為呼叫 C 函式的變數定義一個類別型。

5. Create a variable to store the path of the dynamic library.

   利用一個變數儲存動態庫的路徑。

6. Open the dynamic library that contains the C function.

   載入包含 C 函式的動態庫。

7. Get a reference to the C function, and put it into a variable.

   建立該 C 函式的參考，接著將其賦予變數。

8. Call the C function.

   呼叫 C 函式。

Here's the code for each step.

以下是每一個步驟對應的程式碼。

1. Import `dart:ffi`.

   匯入 dart:ffi。

```dart
import 'dart:ffi' as ffi;
```

2. Import the path library that you'll use to store the path of dynamic library.

   匯入 `path` 庫用於合成動態庫的路徑。

```dart
import 'dart:io' show Platform, Directory;
import 'package:path/path.dart' as path;
```

3. Create a typedef with the FFI type signature of the C function. <br>
   See [Interfacing with native types](#interfacing-with-native-types)
   for commonly used types defined by `dart:ffi` library.

   為 C 函式的 FFI 型別簽名的定義一個類別型。<br>
   參閱 [定義原生型別的介面](#interfacing-with-native-types)
   瞭解 `dart:ffi` 庫中定義的常用型別。

```dart
typedef hello_world_func = ffi.Void Function();
```

4. Create a typedef for the variable that you'll use
   when calling the C function.

   為呼叫 C 函式的變數定義一個類別型。

```dart
typedef HelloWorld = void Function();
```

5. Create a variable to store the path of the dynamic library.

   利用一個變數儲存動態庫的路徑。

```dart
var libraryPath = path.join(Directory.current.path, 'hello_library',
    'libhello.so');
if (Platform.isMacOS) { 
  libraryPath = path.join(Directory.current.path, 'hello_library', 
      'libhello.dylib');
} else if (Platform.isWindows) { 
  libraryPath = path.join(Directory.current.path, 'hello_library', 
      'Debug', 'hello.dll');
} 
```

6. Open the dynamic library that contains the C function.

   載入包含 C 函式的動態庫。

```dart
  final dylib = ffi.DynamicLibrary.open(libraryPath);
```

7. Get a reference to the C function, 
   and put it into a variable.
   This code uses the typedefs defined in steps 2 and 3, 
   along with the dynamic library variable from step 4.

   建立該 C 函式的參考，接著將其賦予變數。
   這段程式碼使用了步驟 2 和 3 定義的型別，以及步驟 4 建立的動態庫變數。

```dart
  final HelloWorld hello = dylib
      .lookup<ffi.NativeFunction<hello_world_func>>('hello_world')
      .asFunction();
```

8. Call the C function.

   呼叫 C 函式。

```dart
  hello();
```

Once you understand the hello_world example, 
you should be ready to look at the
[other `dart:ffi` examples](#examples).

當你理解 hello_world 範例的內容後，
可以進一步學習 [其他的 `dart:ffi` 範例](#examples)。

## Bundling and loading C libraries

## 整合並載入 C 庫

How you bundle (or _package_ or _distribute_)
a C library with your package or app
and then load that library
depends on your platform and the type of library.
For details, see the following:

根據平臺和庫的型別的不同，捆綁（或 **打包** 和 **分發**）
C 庫到 package 或應用並進行載入的方式，有所不同。

* Flutter `dart:ffi` pages: [Android][android], [iOS][ios], and [macOS][macos] 

  Flutter 的 `dart:ffi` 頁面: [Android][android]、[iOS][ios] 和 [macOS][macos]

* [`dart:ffi` examples]({{page.samples}})

  [`dart:ffi` 範例]({{page.samples}})

## Interfacing with native types

The `dart:ffi` library provides multiple types
that implement [`NativeType`][]
and represent native types in C.

Some native types are only used as markers in type signatures
while others (or their subtypes) can be instantiated.

#### Instantiable native types

The following native types can be used as markers in type signatures
and they (or their subtypes) can be instantiated in Dart code:

| **Dart type**                                                                               | **Description**                                                  |
|---------------------------------------------------------------------------------------------|------------------------------------------------------------------|
| [Array]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/Array-class.html)     | A fixed-sized array of items. Supertype of type specific arrays. |
| [Pointer]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/Pointer-class.html) | Represents a pointer into native C memory.                       |
| [Struct]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/Struct-class.html)   | The supertype of all FFI struct types.                           |
| [Union]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/Union-class.html)     | The supertype of all FFI union types.                            |
{:.table .table-striped }

#### Purely marker native types

The following are platform-agnostic native types
that are used only as markers in type signatures,
and can't be instantiated in Dart code:

| **Dart type**                                                                                             | **Description**                                   |
|-----------------------------------------------------------------------------------------------------------|---------------------------------------------------|
| [Bool]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/Bool-class.html)                     | Represents a native bool in C.                    |
| [Double]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/Double-class.html)                 | Represents a native 64 bit double in C.           |
| [Float]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/Float-class.html)                   | Represents a native 32 bit float in C.            |
| [Int8]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/Int8-class.html)                     | Represents a native signed 8 bit integer in C.    |
| [Int16]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/Int16-class.html)                   | Represents a native signed 16 bit integer in C.   |
| [Int32]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/Int32-class.html)                   | Represents a native signed 32 bit integer in C.   |
| [Int64]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/Int64-class.html)                   | Represents a native signed 64 bit integer in C.   |
| [NativeFunction]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/NativeFunction-class.html) | Represents a function type in C.                  |
| [Opaque]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/Opaque-class.html)                 | The supertype of all opaque types in C.           |
| [Uint8]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/Uint8-class.html)                   | Represents a native unsigned 8 bit integer in C.  |
| [Uint16]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/Uint16-class.html)                 | Represents a native unsigned 16 bit integer in C. |
| [Uint32]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/Uint32-class.html)                 | Represents a native unsigned 32 bit integer in C. |
| [Uint64]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/Uint64-class.html)                 | Represents a native unsigned 64 bit integer in C. |
| [Void]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/Void-class.html)                     | Represents the `void` type in C.                  |
{:.table .table-striped }

There are also many [ABI][] specific marker native types
that extend [AbiSpecificInteger][].
Refer to their linked API documentation for more information and
a guideline on what types they map to on specific platforms:

| **Dart type**                                                                                                 | **Description**                                                 |
|---------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| [AbiSpecificInteger][]                                                                                        | The supertype of all ABI-specific integer types.                |
| [Int]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/Int-class.html)                           | Represents the `int` type in C.                                 |
| [IntPtr]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/IntPtr-class.html)                     | Represents the `intptr_t` type in C.                            |
| [Long]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/Long-class.html)                         | Represents the `long int` (`long`) type in C.                   |
| [LongLong]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/LongLong-class.html)                 | Represents the `long long` type in C.                           |
| [Short]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/Short-class.html)                       | Represents the `short` type in C.                               |
| [SignedChar]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/SignedChar-class.html)             | Represents the `signed char` type in C.                         |
| [Size]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/Size-class.html)                         | Represents the `size_t` type in C.                              |
| [UintPtr]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/UintPtr-class.html)                   | Represents the `uintptr_t` type in C.                           |
| [UnsignedChar]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/UnsignedChar-class.html)         | Represents the `unsigned char` type in C.                       |
| [UnsignedInt]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/UnsignedInt-class.html)           | Represents the `unsigned int` type in C.                        |
| [UnsignedLong]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/UnsignedLong-class.html)         | Represents the `unsigned long int` (`unsigned long`) type in C. |
| [UnsignedLongLong]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/UnsignedLongLong-class.html) | Represents the `unsigned long long` type in C.                  |
| [UnsignedShort]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/UnsignedShort-class.html)       | Represents the `unsigned short` type in C.                      |
| [WChar]({{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/WChar-class.html)                       | Represents the `wchar_t` type in C.                             |
{:.table .table-striped }

## Generating FFI bindings with `package:ffigen`

## 使用 `package:ffigen` 產生 FFI 的繫結

For large API surfaces it can be time-consuming
to write the Dart bindings that integrate with the C code.
To reduce this burden,
you can use the [`package:ffigen`][ffigen] binding generator
to automatically create FFI wrappers from C header files.

為大量的 API 編寫繫結可能要花費你的大量時間。你可以使用 [`package:ffigen`][ffigen]
繫結產生器，自動地從 C 標頭檔案產生 FFI 包裝，從而減少時間消耗。

[ABI]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/Abi-class.html
[AbiSpecificInteger]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/AbiSpecificInteger-class.html
[ios]: {{site.flutter-docs}}/development/platform-integration/ios/c-interop
[android]: {{site.flutter-docs}}/development/platform-integration/android/c-interop
[macos]: {{site.flutter-docs}}/development/platform-integration/macos/c-interop
[FFI]: https://en.wikipedia.org/wiki/Foreign_function_interface
[hello_world]: {{page.hw}}
[primitives]: {{page.samples}}/primitives
[structs]: {{page.samples}}/structs
[sqlite]: https://github.com/dart-lang/sdk/tree/main/samples/ffi/sqlite
[mini tutorial.]: https://github.com/dart-lang/sdk/blob/main/samples/ffi/sqlite/docs/sqlite-tutorial.md
[`NativeType`]: {{site.dart-api}}/{{site.data.pkg-vers.SDK.channel}}/dart-ffi/NativeType-class.html
[ffigen]: {{site.pub-pkg}}/ffigen
