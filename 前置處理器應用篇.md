---
title: 「你所不知道的 C 語言：前置處理器應用篇」 筆記
tags: Computer Science,System Software
description: 2019.08.08
---
「你所不知道的 C 語言：前置處理器應用篇」 筆記
===
###### 2019.08.08 *Copyright © 2019 by srhuang*

![](https://i.imgur.com/qcoZUWU.png)

[影片](https://youtu.be/7gadhJcIq_o)
[講義](http://hackfoldr.org/dykc/https%253A%252F%252Fhackmd.io%252Fs%252FS1maxCXMl)
Original by [jserv](http://wiki.csie.ncku.edu.tw/User/jserv)

---

## 前情提要
* C11 _Generic

## 不要小看 preprocessor
* 用希臘問號取代分號：https://youtu.be/7gadhJcIq_o?t=73
* C++，叫我如何接納你？：https://youtu.be/7gadhJcIq_o?t=164
    * 如何利用 C語言實作 template 機制。

## 開發物件導向程式時，善用 preprocessor 可大幅簡化開發
* 延續「物件導向程式設計篇」的思考：https://youtu.be/7gadhJcIq_o?t=209
    * 把介面和實作抽離開來。
* Stringification/Stringizing(字串化)：https://youtu.be/7gadhJcIq_o?t=268
    * 使用 “#”。
    * 讓一個表示式變成一個字串。
    * assert。
* concatenation(連結，接續)：https://youtu.be/7gadhJcIq_o?t=424
    * 使用“##"。
* 複習編譯流程：https://youtu.be/7gadhJcIq_o?t=571
* 以 raytracing 程式為例：https://youtu.be/7gadhJcIq_o?t=670
    * 光影追蹤程式。
    * 可用 gcc -E -P 觀察輸出。
* _POSIX_SOURCE：https://youtu.be/7gadhJcIq_o?t=1254
    * 因為 Macro 定義的不同，會導致程式行為的不同。
    * Feature Test Macros：The exact set of features available when you compile a source file is controlled by which feature test macros you define.
* 物件導向案例：https://youtu.be/7gadhJcIq_o?t=1529
    * 主要分成兩大流派：Class and Prototype。
    * Class Based：依照固定模板造出 object。
    * Prototype Based：一開始沒有一定的模板，而是不同的擴充/縮減。
    * 圖解 JavaScript 的物件模型 (Prototype Based)
    * The Prototype Object System

## _Generic [C11]
* C11 Standard：https://youtu.be/7gadhJcIq_o?t=1949
    * alignment
    * type-generic
    * multi-threading
* 求開立方根 (cube root) 的程式：https://youtu.be/7gadhJcIq_o?t=2224
    * Complex Numbers：https://www.gnu.org/software/libc/manual/html_node/Complex-Numbers.html
    * sqrt, sqrtf, sqrtl：https://youtu.be/7gadhJcIq_o?t=2413
    * cbrt, cbrtf, cbrtl.
* <tgmath.h> - type-generic macros：https://youtu.be/7gadhJcIq_o?t=2601
    * include the headers <math.h> and <complex.h>.
* C _Generic / C++ Template 範例：https://youtu.be/7gadhJcIq_o?t=2690

## 應用: Unit Test
* 職場厚黑談：https://youtu.be/7gadhJcIq_o?t=2852
* unity_fixture.h：https://youtu.be/7gadhJcIq_o?t=3029
    * unity：Unit Test for C
* Google Test：https://youtu.be/7gadhJcIq_o?t=3528
    * Google Mock：an extension to Google Test for writing and using C++ mock classes.
    * [metaclass](https://en.wikipedia.org/wiki/Metaclass)：In object-oriented programming, a metaclass is a class whose instances are classes.產生模板的模板。
    * Code Generator
    * Mock Class：給定一個表示式，產生對應的 class 和程式碼。
* C++ 的老爸不喜歡用 macro：https://youtu.be/7gadhJcIq_o?t=4277
    * cern c standard：歐洲核子研究組織
        * WWW 在這裡誕生的。
        * 有規範要減少使用 Macro。
* Macro and Function：
    * Macro 主要目的是 code generator。

## 應用: Object Model
* Object C：https://youtu.be/7gadhJcIq_o?t=4685
    * Exceptions handling (try catch finally)
    * Classes, interfaces with Java like inheritance
    * [GObject Introspection](https://en.wikipedia.org/wiki/GObject)：不同的程式語言可以包裝成一致的介面。
    * VA_ARGS：
        * stdarg.h
    * __typeof__：GNU(gcc) extension

## 應用: Exception Handling
* Extended C：https://youtu.be/7gadhJcIq_o?t=5319
    * setjmp, longjmp：跨越 stack 的 goto，但不能跨越 process。

## 應用: ADT
* pearldb：https://youtu.be/7gadhJcIq_o?t=5598
    * klib：a generic library in C
    * Ksort：function 的實作在 header 檔中直接展開，省去 function call 的成本，加速 database 的效能。

## 總結：https://youtu.be/7gadhJcIq_o?t=6524
