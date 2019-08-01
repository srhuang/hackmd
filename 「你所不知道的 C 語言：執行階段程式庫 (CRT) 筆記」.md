---
title: 「你所不知道的 C 語言：執行階段程式庫 (CRT)」 筆記
tags: Computer Science,System Software
description: 2019.08.02
---
你所不知道的 C 語言：執行階段程式庫 (CRT) 筆記
===
Copyright © 2019 by srhuang

![](https://i.imgur.com/3bLGtSB.png)

[影片](https://youtu.be/qAVdTrC7YyU)
[講義](http://hackfoldr.org/dykc/https%253A%252F%252Fhackmd.io%252Fs%252FHkcr5cn97)
Original by [jserv](http://wiki.csie.ncku.edu.tw/User/jserv)

---

## 動機

* Visual C++ runtime error：https://youtu.be/qAVdTrC7YyU?t=1
* man atexit：https://youtu.be/qAVdTrC7YyU?t=218
* C 語言歷史：https://youtu.be/qAVdTrC7YyU?t=346
    * 1972-1973 C語言及其編譯器發展完成
    * ANSI 1989 Standardization

## C 語言關鍵字獨到之處

* multics PL/I [1964]：https://youtu.be/qAVdTrC7YyU?t=758
* 關鍵字：https://youtu.be/qAVdTrC7YyU?t=962
    * COBOL and Fortran 的關鍵字和保留字 都和 I/O有關，但 C 語言的關鍵字與 I/O 無關
* C語言之前的歷史：https://youtu.be/qAVdTrC7YyU?t=1103
    * [The First Programming Languages: Crash Course Computer Science #11](https://www.youtube.com/watch?v=RU1u-js7db8)

## 先看 Microsoft 的文件怎麼說

* DLL 和 Visual C++ 執行階段程式庫行為：https://youtu.be/qAVdTrC7YyU?t=1315
* How To Use the C Run-Time：https://youtu.be/qAVdTrC7YyU?t=1658
* NaN：https://youtu.be/qAVdTrC7YyU?t=1888
* perror：https://youtu.be/qAVdTrC7YyU?t=2137
* 為什麼要了解 CRT？ 為什麼要了解系統？：https://youtu.be/qAVdTrC7YyU?t=2437
* MSVC 與 CRT 的恩怨情仇：https://youtu.be/qAVdTrC7YyU?t=2574

## 回到編譯流程

* 複習 你所不知道的 C 語言：編譯器和最佳化原理篇：https://youtu.be/qAVdTrC7YyU?t=2811
* int main() { return 1; }：https://youtu.be/qAVdTrC7YyU?t=3089
* _exit() / exit() 的介紹：https://youtu.be/qAVdTrC7YyU?t=3195
    * The function _exit() is like exit(3), but does not call any functions registered with atexit(3) or on_exit(3). 
* FPU：https://youtu.be/qAVdTrC7YyU?t=3637
    * 特別在硬體沒有 FPU 時，需要 libgcc 來提供浮點運算協助
    * [Hard / Soft floating](https://stackoverflow.com/questions/3321468/whats-the-difference-between-hard-and-soft-floating-point-numbers)
* 驗證執行檔：https://youtu.be/qAVdTrC7YyU?t=4112
    * uefi signature

## int main(int argc, char *argv[]) 背後的學問

* void main 的錯誤：https://youtu.be/qAVdTrC7YyU?t=4444
* argument expression (caller) / parameter value (callee)：https://youtu.be/qAVdTrC7YyU?t=4536
    * parameterized type：C++ STL template 大量使用.
* envp 的宣告：https://youtu.be/qAVdTrC7YyU?t=4923
    * int main(int argc, char *argv[], char *envp[])
    * APUE 經典書。
    * 由 argv 的擺放方式可以發現，後面是接著 envp。 
    * CRT 也要負責傳遞環境變數。
* 觀察執行時期：https://youtu.be/qAVdTrC7YyU?t=5732
    * cmdline
    * environ
* 深度剖析 C 語言 main 函式：https://youtu.be/qAVdTrC7YyU?t=6001

## GNU Toolchain

* 簡介：https://youtu.be/qAVdTrC7YyU?t=6251
* Computer Science from the Bottom Up：https://youtu.be/qAVdTrC7YyU?t=6341
    * GNU Toolchain
* Linking：https://youtu.be/qAVdTrC7YyU?t=6632
    * crti.o, crtbeginS.o, crtendS.o, crtn.o
* Linker Script：https://youtu.be/qAVdTrC7YyU?t=7334
    * init / fini
* crt0：https://youtu.be/qAVdTrC7YyU?t=7609
    * argc, argv, envp get from stack。
    * call main
    * call exit
* newlib/i386 的實作程式碼：https://youtu.be/qAVdTrC7YyU?t=7919
    * GNU Hurd OS 的歷史奇耙。
    * Newlib for embedded systems.
* Creating a C Library：https://youtu.be/qAVdTrC7YyU?t=8643

## 總結：https://youtu.be/qAVdTrC7YyU?t=8761


