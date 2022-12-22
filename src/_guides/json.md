---
title: Using JSON
title: 在 Dart 裡使用 JSON
description: Dart solutions for reading and writing JSON.
description: Dart 裡讀寫 JSON。
---

Most mobile and web apps use JSON for tasks such as
exchanging data with a web server.
This page discusses Dart support for JSON _serialization_ and _deserialization_:
converting Dart objects to and from JSON.

許多移動或網頁應用都使用 JSON 來處理類似與伺服器交換資料的任務。本文將討論如果使用 Dart 對 JSON 資料進行**序列化**和**反序列化**：即 Dart 物件與 JSON 資料之間的相互轉換。

## Libraries

## 需要用到的函式庫

The following libraries and packages are useful across Dart platforms:

下述的函式庫和套件可以用作於所有的 Dart 平台：

* [dart:convert](/guides/libraries/library-tour#dartconvert---decoding-and-encoding-json-utf-8-and-more)<br>
  Converters for both JSON and UTF-8
  (the character encoding that JSON requires).

  [dart:convert](/guides/libraries/library-tour#dartconvert---decoding-and-encoding-json-utf-8-and-more)<br>
  包含 JSON 資料和 UTF-8（JSON 資料需要的字元編碼）的轉換器。

* [package:json_serializable]({{site.pub-pkg}}/json_serializable)<br>
  An easy-to-use code generation package.
  When you add some metadata annotations
  and use the builder provided by this package,
  the Dart build system generates serialization and deserialization code for you.

  [package:json_serializable]({{site.pub}}/packages/json_serializable)<br>
  一個易於使用的程式碼產生器套件。當你新增一些元註解以及使用該套件提供的建構器時，
  Dart 編譯器可以為你產生序列化和反序列化的程式碼。

* [package:built_value]({{site.pub-pkg}}/built_value)<br>
  A powerful, opinionated alternative to json_serializable.

  [package:built_value]({{site.pub}}/packages/built_value)<br>
  一個強大的，可以作為 json_serializable 替代的 package。

## Flutter resources

## 相關的 Flutter 資源

[JSON and serialization]({{site.flutter-docs}}/development/data-and-backend/json)
<br> Shows how Flutter apps can serialize and deserialize both
  with dart:convert and with json_serializable.

[JSON 和序列化]({{site.flutter-docs}}/development/data-and-backend/json)
<br> 向你展示 Flutter 應用是如何使用 dart:convert 和
  json_serializable 進行序列化和反序列化的。

## Web app resources

## 相關的 Web 應用資源

[Using HTTP resources with HttpRequest](/guides/libraries/library-tour#using-http-resources-with-httprequest)
<br> Demonstrates how to use HttpRequest to exchange data with a web server.
  Part of the [dart:html library tour.](/guides/libraries/library-tour#darthtml)

[透過 HttpRequest 使用 HTTP 資源](/guides/libraries/library-tour#using-http-resources-with-httprequest)
<br> 向你示範如何使用 HttpRequest 與伺服器交換資料。
  該資源是 [dart:html 函式庫概覽](/guides/libraries/library-tour#darthtml) 的一部分。

{% comment %}
## VM resources

## 相關的 VM 資源

[Write HTTP servers](/tutorials/server/httpserver)
<br> Walks through how to implement command-line clients and servers
  that exchange JSON data.

[開發 HTTP 伺服器端](/tutorials/server/httpserver)
<br> 手把手教你如何實現一個命令列式的客戶端和伺服器端並使用 JSON 交換資料。

## Other tools and resources
{% endcomment %}
