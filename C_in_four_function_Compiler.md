---
title: Code Tracing：C in four function (c4) Compiler
tags: Computer Science,System Software
description: 2019.08.05
---
Code Tracing：C in four function (c4) Compiler
===
###### 2019.08.21 *Copyright © 2019 by srhuang*

在閱讀 Compiler 相關材料，到真正自己實作 Compiler 之間，還有一個重要的步驟就是 Code Tracing. 挑選一個經典且重要的 source code 來 trace 會幫助我們從理論層面下到實作層面，但我還是會建議在 Code Tracing 之前還是要有點理論基礎，否則在茫茫程式碼海中，很容易迷失自我的。

c4 Compiler 不僅是個 C self-host compiler，它也啟發了許多人去撰寫自己的 Compiler，甚至是個了解 Compiler 很好的入門磚。關於如何 From Source code to Binary，你將會有著具體的實作方法的脈絡，會輔助我們去了解更多編譯器相關的理論，以及為何要學習數學。讓我們用欣賞程式碼的角度，來把玩一下 c4，這也將是我們之後自幹編譯器的基礎。

## Source Code
[c4](https://github.com/rswier/c4)

## 網友註解
* 原始碼加上註解：https://github.com/comzyh/c4/blob/comment/c4.c
* 目前我覺得寫得最好的分析文章：https://www.zhihu.com/question/28249756
* 中間代碼的解釋：https://blog.csdn.net/fishmei2/article/details/50777701

## 背景知識
* [Operator-precedence parser](https://en.wikipedia.org/wiki/Operator-precedence_parser)
* [Recursive descent parser](https://en.wikipedia.org/wiki/Recursive_descent_parser)
* [BNF/EBNF](https://en.wikipedia.org/wiki/Backus%E2%80%93Naur_form)
* Compilation：
    * Lexical Analyzer. 
    * Syntax Analyzer.
    * Semantic Analyzer.
    * Code Genarator.

## 擴充
* 利用 c4 實作 JIT Compiler：https://github.com/EarlGray/c4
* AST + Codegen —> c5：https://github.com/rswier/c4/commit/d8e61a829c031d887c203515d0ceb5d54dd7318e
* 手把手教你建構 C 語言編譯器：https://github.com/lotabout/write-a-C-interpreter
* Jserv AMaCC：https://github.com/jserv/amacc

## 延伸閱讀
* Tiny C Compiler (TCC)：https://bellard.org/tcc/
* Human Resource Machine - Tomorrow Corporation：https://tomorrowcorporation.com/humanresourcemachine

## c4 特色
* Self-Host Compiler.
* Support three types: INT, CHAR, PTR, pointer to INT or CHAR.
* Support if-else and while.
* Support enum.
* Variable Declarations should NOT be assigned.
* Local Variables Declaration 必須在 Function 裡面的最開頭位置。
* single-pass compiler.
* 最後生成虛擬機的代碼。

## c4 不支援項目
* 前置處理器。
* do-while, for, switch.
* forward declaration.
* struct.
* heap allocation.

## Code Analyze
### Tokens

```clike=30
// tokens and classes (operators last and in precedence order)
enum {
  Num = 128, Fun, Sys, Glo, Loc, Id,
  Char, Else, Enum, If, Int, Return, Sizeof, While,
  Assign, Cond, Lor, Lan, Or, Xor, And, Eq, Ne, Lt, Gt, Le, Ge, Shl, Shr, Add, Sub, Mul, Div, Mod, Inc, Dec, Brak
};
```
* 為何 enum 是從 128 開始的呢？
主要是因為 [ASCII Code ](https://zh.wikipedia.org/wiki/ASCII) 定義的範圍是 0~127 共 128 字元，所以我們自訂的 token number 必須從 128 開始編號。

### Virtual Machine (Opcode)

```clike=37
// opcodes
enum { LEA ,IMM ,JMP ,JSR ,BZ  ,BNZ ,ENT ,ADJ ,LEV ,LI  ,LC  ,SI  ,SC  ,PSH ,
       OR  ,XOR ,AND ,EQ  ,NE  ,LT  ,GT  ,LE  ,GE  ,SHL ,SHR ,ADD ,SUB ,MUL ,DIV ,MOD ,
       OPEN,READ,CLOS,PRTF,MALC,FREE,MSET,MCMP,EXIT };
```
Generally, LDR is used to load something from ==memory== into a ==register==, and STR is used to store something from a ==register== to a ==memory== address.
參考資料：[MEMORY INSTRUCTIONS: LOAD AND STORE](https://azeria-labs.com/memory-instructions-load-and-store-part-4/)

* sp：stack frame。
* a：register 暫存器。
* bp：概念跟 rbp(32 bits)/ebp(64 bits) 相同。
* pc：program counter，指向當前指令。

![](https://i.imgur.com/pcp5DfL.png)

Opcode         | Fully Name  | Description
:-:|:-:|:-:|
LEA |Load Effective Address| 利用 bp 將相對位址存入暫存器 a
IMM | Immediate Value | 將 immediate value or global address 存入暫存器 a
JMP | Jump | 跳轉至目標指令的絕對位址
JSR | Jump to SubRoutine | Push return address into sp and  JMP
BZ | Branch if Zero | 後面接的是目標指令的絕對位址
BNZ | Branch if NOT Zero | 後面接的是目標指令的絕對位址
ENT | Enter SubRoutine | Push bp and bp points to sp，sp 會預留 local variables 的位置
ADJ | Stack Adjust | 根據目前 sp 的相對位置
LEV | Leave SubRoutine | sp=bp, bp points to old bp, 讀取 return address to pc, sp++釋放空間
LI | Load INT | 將暫存器 a 所指的位址取值，存入暫存器 a
LC | Load CHAR | 將暫存器 a 所指的位址取值，存入暫存器 a
SI | Store INT | 將暫存器 a 取值，存入 stack frame sp 所指的位址，然後 sp++
SC | Store CHAR | 將暫存器 a 取值，存入 stack frame sp 所指的位址，然後 sp++，最後再存入暫存器 a
PSH | Push into Stack Frame | 將暫存器 a 的值 push into Stack Frame
OR-MODE | Arithmetic operation | pop from Stack Frame and calculate with register value, and then restore into regrister.
OPEN-MEMCMP | System Call | 使用 Stack Frame sp 當作參數傳遞，並且 pop Stack
EXIT | exit program | 利用 Stack Frame sp 當作回傳值

### Symbol Table
由於 c4 不支援 struct，因此使用 enum 來定義 symbol table(called id for c4)。

```clike=45
// identifier offsets (since we can't create an ident struct)
enum { Tk, Hash, Name, Class, Type, Val, HClass, HType, HVal, Idsz };
```
### Initialization
Symbol Table and Stack Frame 的大小都是 256KB。
```clike=346
poolsz = 256*1024; // arbitrary size
  if (!(sym = malloc(poolsz))) { printf("could not malloc(%d) symbol area\n", poolsz); return -1; }
  if (!(le = e = malloc(poolsz))) { printf("could not malloc(%d) text area\n", poolsz); return -1; }
  if (!(data = malloc(poolsz))) { printf("could not malloc(%d) data area\n", poolsz); return -1; }
  if (!(sp = malloc(poolsz))) { printf("could not malloc(%d) stack area\n", poolsz); return -1; }

  memset(sym,  0, poolsz);
  memset(e,    0, poolsz);
  memset(data, 0, poolsz);
```
* sym：symbol table.
* e：Assembly Code(IR) output.
* le：last print IR.
* sp：Stack Frame.
* data：data section.

### Keywords

```clike=356
p = "char else enum if int return sizeof while "
      "open read close printf malloc free memset memcmp exit void main";
  i = Char; while (i <= While) { next(); id[Tk] = i++; } // add keywords to symbol table
```
根據 enum 從 Char 到 While 一一加入 symbol table 中。在 `next()` function 中 `id` 將會指到 `sym`.

```clike=75
id = sym;
```
另外，`next()` function 是根據 `p` 所指的 String 做 parsing token 的動作。

```clike=52
while (tk = *p) {
```

### next() : Lexical Analyzer
順著 code flow，接下來我們來深入分析 c4 是如何 parsing tokens。

```clike=48
void next()
{
  char *pp;

  while (tk = *p) {
```
最外層的 while 大部分情況都不會是終止條件，主要是由 while 內部直接 return，因此 `next()` 的邏輯是處理完一個 token 後就會直接 return。

```clike=70
else if ((tk >= 'a' && tk <= 'z') || (tk >= 'A' && tk <= 'Z') || tk == '_') {
  pp = p - 1;
  while ((*p >= 'a' && *p <= 'z') || (*p >= 'A' && *p <= 'Z') || (*p >= '0' && *p <= '9') || *p == '_')
    tk = tk * 147 + *p++;
  tk = (tk << 6) + (p - pp);
  id = sym;
  while (id[Tk]) {
    if (tk == id[Hash] && !memcmp((char *)id[Name], pp, p - pp)) { tk = id[Tk]; return; }
    id = id + Idsz;
  }
  id[Name] = (int)pp;
  id[Hash] = tk;
  tk = id[Tk] = Id;
  return;
}
```
這段是主要處理 Keywords。第一步先算出該 token 的 hash value，然後利用 hash value and name 找尋 symbol table，如果有找到就回傳該 token enum value；如果找不到就新增 id(symbol)，並且設定 token enum value 為 Id(133)。這邊最容易混淆的是 `tk` 這個 variable：在進入此 code block 之前，`tk=*p`；然後在算 hash value 的時候，`tk` 代表的是 hash value；最後要 return 前，`tk` 紀錄的是 token enum value。

```clike=356
p = "char else enum if int return sizeof while "
      "open read close printf malloc free memset memcmp exit void main";
  i = Char; while (i <= While) { next(); id[Tk] = i++; } // add keywords to symbol table
```
從 `next()` 新增 id(symbol) 之後，會再重新設定 token enum value。

```clike=359
i = OPEN; while (i <= EXIT) { next(); id[Class] = Sys; id[Type] = INT; id[Val] = i++; } // add library to symbol table
```
接下來處理 system call library，從 `next()` 新增 id(symbol) 之後，會再設定 token class, type, and value。

```clike=360
next(); id[Tk] = Char; // handle void type
next(); idmain = id; // keep track of main
```
處理 `void` and `main` keyword，另外使用 `idmain` 紀錄 `main` `在 symbol table 的位置。

```clike=363
if (!(lp = p = malloc(poolsz))) { printf("could not malloc(%d) source area\n", poolsz); return -1; }
if ((i = read(fd, p, poolsz-1)) <= 0) { printf("read() returned %d\n", i); return -1; }
p[i] = 0;
close(fd);
```
讀取 source code 到 `p(lp=p)` 所指的位址，並且關閉 FD。
```clike=368
// parse declarations
line = 1;
next();
while (tk) {
```
在 `main()` 中，以上面這個 while loop 來 parsing tokens，直到 `tk`=‘\0’。

到目前為止，我們已經掌握 Lexical Analyzer 的前置作業，以及載入 source code，和 Parsing 流程。雖然 `next()` 並沒有參數和回傳值，但其實是利用 pointer `p` 當作 input，global variable `tk` 當作 output。接下來要進入 Lexical Anlayzer 的核心探討。

除了前面已經討論過的 keywords parsing 部分，接下來來看看處理 Number 的部分。

```clike=85
else if (tk >= '0' && tk <= '9') {
  if (ival = tk - '0') { while (*p >= '0' && *p <= '9') ival = ival * 10 + *p++ - '0'; }
  else if (*p == 'x' || *p == 'X') {
    while ((tk = *++p) && ((tk >= '0' && tk <= '9') || (tk >= 'a' && tk <= 'f') || (tk >= 'A' && tk <= 'F')))
      ival = ival * 16 + (tk & 15) + (tk >= 'A' ? 9 : 0);
  }
  else { while (*p >= '0' && *p <= '7') ival = ival * 8 + *p++ - '0'; }
  tk = Num;
  return;
}
```
這三個條件分別處理的是十進位、十六進位、八進位。回傳計算後的 `ival` and `tk=Num`。

這邊處理 16 進位的方式相當有趣，主要是如何把 A~F 轉成 10~15，為何只需要 +9 就可以將 16 進位英文的部份轉成數字？關鍵在於 'A' 的 ASCII code = 65 = '0100 0001'，而數字 10 的 2 進位是 '1010'，由於 16 進位並不會使用的 MSB 的 4 bits，因此 `(tk & 15)` 濾掉前面 4 bits。觀察從 A 轉成數字 10 LSB 的差距：'1010' - '0001' = '1001' = 9。

```clike=95
else if (tk == '/') {
  if (*p == '/') {
    ++p;
    while (*p != 0 && *p != '\n') ++p;
  }
  else {
    tk = Div;
    return;
  }
}
```
這段是處理 Div(/) or comment(//)，回傳`tk=Div`。

```clike=105
else if (tk == '\'' || tk == '"') {
  pp = data;
  while (*p != 0 && *p != tk) {
    if ((ival = *p++) == '\\') {
      if ((ival = *p++) == 'n') ival = '\n';
    }
    if (tk == '"') *data++ = ival;
  }
  ++p;
  if (tk == '"') ival = (int)pp; else tk = Num;
  return;
}
```
這段主要是處理 String，並且將字串存入 data section 中。回傳 data 起始位置 `ival = (int)pp;`，如果 ==tk\=\`== 將 token 設為 Num，`tk=Num`。

值得一提的是，'\n' 需要特別處理，因為在字串處理中，'\n' 會被當成兩個字元，但其實在 ASCII 中他是 LF(換行字元)，ASCII code = 10。

```clike=117
else if (tk == '=') { if (*p == '=') { ++p; tk = Eq; } else tk = Assign; return; }
else if (tk == '+') { if (*p == '+') { ++p; tk = Inc; } else tk = Add; return; }
else if (tk == '-') { if (*p == '-') { ++p; tk = Dec; } else tk = Sub; return; }
else if (tk == '!') { if (*p == '=') { ++p; tk = Ne; } return; }
else if (tk == '<') { if (*p == '=') { ++p; tk = Le; } else if (*p == '<') { ++p; tk = Shl; } else tk = Lt; return; }
else if (tk == '>') { if (*p == '=') { ++p; tk = Ge; } else if (*p == '>') { ++p; tk = Shr; } else tk = Gt; return; }
else if (tk == '|') { if (*p == '|') { ++p; tk = Lor; } else tk = Or; return; }
else if (tk == '&') { if (*p == '&') { ++p; tk = Lan; } else tk = And; return; }
else if (tk == '^') { tk = Xor; return; }
else if (tk == '%') { tk = Mod; return; }
else if (tk == '*') { tk = Mul; return; }
else if (tk == '[') { tk = Brak; return; }
else if (tk == '?') { tk = Cond; return; }
else if (tk == '~' || tk == ';' || tk == '{' || tk == '}' || tk == '(' || tk == ')' || tk == ']' || tk == ',' || tk == ':') return;
```
Operator 的部分會轉成 token enum value，其他符號的部分就直接依照 ASCII Code 回傳，這就是為何 token enum value 要從 128 開始，因為不能和 ASCII Code 產生衝突啊！

關於 c4 Lexical Analyzer 的程式碼分析就告一段落了，經過 `next()` 後就完成 token parser，並且取得 token enum value(`tk`)(and`ival` if need)。

### expr(), stmt() : Syntax Analyzer + Semantic Analyzer + CodeGen

#### Background
* Syntax Analyzer (Parser)
    * is the process of analysing a string of symbols, conforming to the rules of a formal grammar(e.g. CFG).
    * 這一步我們稱為語法分析（==Syntactic analysis==），而負責做這項工作的程式我們稱之為語法分析器（==Parser==）。
    * ==Parser== 要做的工作就是，讀取 Scanner 分析出來的 Token，然後建立並返回一棵 ==Parse tree==。
    * A ==parse tree== or parsing tree or derivation tree or concrete syntax tree is an ordered, rooted tree that represents the syntactic structure of a string according to some context-free grammar.
    * Types of Parser
        * [Top-down parsing](https://en.wikipedia.org/wiki/Top-down_parsing) : LL parsers and ==recursive-descent parser== are examples of top-down parsers.
        * [Bottom-up parsing](https://en.wikipedia.org/wiki/Bottom-up_parsing) : ==Operator-precedence parser==.
    * c4 主要是採用 recursive-descent parser (`stmt()` and recursion call in `expr()`)，但是在運算式的部分採用 Operator-precedence parser (which is in the `expr()`)。
    * More info for Operator-precedence parser : https://en.wikipedia.org/wiki/Operator-precedence_parser

* Semantic Analyzer
    * It usually includes ==type checking==, or makes sure a ==variable is declared== before use which is impossible to describe in the extended Backus–Naur form and thus not easily detected during parsing.
    * Semantic analysis adds semantic information to the parse tree and ==builds the symbol table==.
    * This phase performs semantic checks such as ==type checking== (checking for type errors), or ==object binding== (associating variable and function references with their definitions), or ==definite assignment== (requiring all local variables to be initialized before use), rejecting incorrect programs or issuing warnings. [Wiki](https://en.wikipedia.org/wiki/Compiler#Front_end)
    * c4 在 `next()` Lexical analyzer 的時候就有包含 semantic analyzer，這時候會建立 symbol table；在 `main()` 會檢查是否重複定義；在 `expr()` 會做 ==type checking== and ==undefined variable checking==，這些部分也是屬於 semantic analyzer。

* Virtual Machine (c4)
    * sp：stack frame。
    * a：register 暫存器。
    * bp：概念跟 rbp(32 bits)/ebp(64 bits) 相同。
    * pc：program counter，指向當前指令。
    * instuction：OPCODE。

#### expr()
分成兩個部分：第一部分利用 recursive descent (parsing)，檢查 symbol table (semantic Analyzer)，直接產生 IR (Code Generator)；如果有需要就會進行第二部分，Operator precedence (parsing)，主要處理四則運算。

```clike=137
//第一部分
if (!tk) { printf("%d: unexpected eof in expression\n", line); exit(-1); } 
```
其實更好作法是使用 switch，但是 c4 並不支援 switch，因此才會採用多個 if-else。

```clike=217
//第二部分
while (tk >= lev) {
```
##### Num 數字處理
```clike=139
else if (tk == Num) { *++e = IMM; *++e = ival; next(); ty = INT; }
```
直接 output IR。

##### String 字串處理
先來回顧一下 ==next()== 對於字串的處理：將字串存入 data section 中。回傳 data 起始位置 `ival = (int)pp;`。

```clike=140
else if (tk == '"') {
    *++e = IMM; *++e = ival; next();
    while (tk == '"') next();
    data = (char *)((int)data + sizeof(int) & -sizeof(int)); ty = PTR;
}
```
這邊的 data 有特別做 alignment [[Wiki](https://en.wikipedia.org/wiki/Data_structure_alignment)]，最後輸出 IR。

##### Sizeof Operator
```clike=145
else if (tk == Sizeof) {
    next(); if (tk == '(') next(); else { printf("%d: open paren expected in sizeof\n", line); exit(-1); }
    ty = INT; if (tk == Int) next(); else if (tk == Char) { next(); ty = CHAR; }
    while (tk == Mul) { next(); ty = ty + PTR; }
    if (tk == ')') next(); else { printf("%d: close paren expected in sizeof\n", line); exit(-1); }
    *++e = IMM; *++e = (ty == CHAR) ? sizeof(char) : sizeof(int);
    ty = INT;
}
```
直接計算 sizeof 的大小。

##### 變數處理

```clike=153
else if (tk == Id) {
    d = id; next();
    if (tk == '(') {
      next();
      t = 0;
      while (tk != ')') { expr(Assign); *++e = PSH; ++t; if (tk == ',') next(); }
      next();
      if (d[Class] == Sys) *++e = d[Val];
      else if (d[Class] == Fun) { *++e = JSR; *++e = d[Val]; }
      else { printf("%d: bad function call\n", line); exit(-1); }
      if (t) { *++e = ADJ; *++e = t; }
      ty = d[Type];
    }
    else if (d[Class] == Num) { *++e = IMM; *++e = d[Val]; ty = INT; }
    else {
      if (d[Class] == Loc) { *++e = LEA; *++e = loc - d[Val]; }
      else if (d[Class] == Glo) { *++e = IMM; *++e = d[Val]; }
      else { printf("%d: undefined variable\n", line); exit(-1); }
      *++e = ((ty = d[Type]) == CHAR) ? LC : LI;
    }
  }
```
第一個 `if` 處理 function call，第二個 `else if` 處理 enum，第三個 `else` 處理變數。

```clike=154
d = id; next();
```
lookahead 的技巧。

```clike=155
if (tk == '(') {
  next();
  t = 0;
  while (tk != ')') { expr(Assign); *++e = PSH; ++t; if (tk == ',') next(); }
  next();
  if (d[Class] == Sys) *++e = d[Val];
  else if (d[Class] == Fun) { *++e = JSR; *++e = d[Val]; }
  else { printf("%d: bad function call\n", line); exit(-1); }
  if (t) { *++e = ADJ; *++e = t; }
  ty = d[Type];
}
```
第一個 `while` 處理參數(argument)的部分，並且產生 push into stack 的 IR；接下來的兩個 `if` 分別處理system call and function call：system call 不做 binding 的動作，只保留 Symbol name；function call 則使用 JSR 指令來跳躍到該函式的真實位址。最後根據參數的數量調整 stack frame pointer。

```clike=167
else {
  if (d[Class] == Loc) { *++e = LEA; *++e = loc - d[Val]; }
  else if (d[Class] == Glo) { *++e = IMM; *++e = d[Val]; }
  else { printf("%d: undefined variable\n", line); exit(-1); }
  *++e = ((ty = d[Type]) == CHAR) ? LC : LI;
}
```
Local Variable 的部分利用 LEA 將 local variable load 到 register 中，位址是相對位址。Global Variable 放在 data section，value 紀錄的是 data 的位址。最後根據 type 來決定 LC or LI。

##### 括號處理
```clike=174
else if (tk == '(') {
    next();
    if (tk == Int || tk == Char) {
      t = (tk == Int) ? INT : CHAR; next();
      while (tk == Mul) { next(); t = t + PTR; }
      if (tk == ')') next(); else { printf("%d: bad cast\n", line); exit(-1); }
      expr(Inc);
      ty = t;
    }
    else {
      expr(Assign);
      if (tk == ')') next(); else { printf("%d: close paren expected\n", line); exit(-1); }
    }
}
```
第一個 `if` 處理 type casting，`else` 則是處理括號的優先權。 

##### dereference/address-of

```clike=188
else if (tk == Mul) {
    next(); expr(Inc);
    if (ty > INT) ty = ty - PTR; else { printf("%d: bad dereference\n", line); exit(-1); }
    *++e = (ty == CHAR) ? LC : LI;
}
else if (tk == And) {
    next(); expr(Inc);
    if (*e == LC || *e == LI) --e; else { printf("%d: bad address-of\n", line); exit(-1); }
    ty = ty + PTR;
}
```
recursive-descent call `expr()`，然後 check type 是否為一階 pointer type (semantic analyzer)，dereference semantic analyze 的過程為 `ty=ty-PTR`。

address-of 的部分直接把 LI/LC command 砍掉，register 所存的就會是位址；相較於 derefernece 而言，register 存的是真正的值。

##### Unary Operation

```clike=198
else if (tk == '!') { next(); expr(Inc); *++e = PSH; *++e = IMM; *++e = 0; *++e = EQ; ty = INT; }
else if (tk == '~') { next(); expr(Inc); *++e = PSH; *++e = IMM; *++e = -1; *++e = XOR; ty = INT; }
```
`expr()` 回傳的值會存在 register，因此這邊要產生的 IR 要先把結果 Push 回 stack，然後再設定 register value，最後做運算，結果會 restore into register。

```clike=200
else if (tk == Add) { next(); expr(Inc); ty = INT; }
else if (tk == Sub) {
    next(); *++e = IMM;
    if (tk == Num) { *++e = -ival; next(); } else { *++e = -1; *++e = PSH; expr(Inc); *++e = MUL; }
    ty = INT;
}
```
處理 expr 開頭是 +/- 的 case。

```clike=206
else if (tk == Inc || tk == Dec) {
    t = tk; next(); expr(Inc);
    if (*e == LC) { *e = PSH; *++e = LC; }
    else if (*e == LI) { *e = PSH; *++e = LI; }
    else { printf("%d: bad lvalue in pre-increment\n", line); exit(-1); }
    *++e = PSH;
    *++e = IMM; *++e = (ty > PTR) ? sizeof(int) : sizeof(char);
    *++e = (t == Inc) ? ADD : SUB;
    *++e = (ty == CHAR) ? SC : SI;
}
```
pre-increment/pre-decrement：透過插入 Push command 將 register 的值存入 stack 中。

`expr()` 第一部分處理的都是 expression 開頭的部分，接著第二部分將會展示如何使用 operator-precedence 處理 operator 優先權。

##### Assign

```clike=220
if (tk == Assign) {
    next();
    if (*e == LC || *e == LI) *e = PSH; else { printf("%d: bad lvalue in assignment\n", line); exit(-1); }
    expr(Assign); *++e = ((ty = t) == CHAR) ? SC : SI;
}
```
將暫存器的位址 push 到 stack 中，做完後面的運算後，結果通常都會在暫存器中，因此透過 SC/SI 將暫存器的值存到 stack 中的位址。

```clike=225
else if (tk == Cond) {
  next();
  *++e = BZ; d = ++e;
  expr(Assign);
  if (tk == ':') next(); else { printf("%d: conditional missing colon\n", line); exit(-1); }
  *d = (int)(e + 3); *++e = JMP; d = ++e;
  expr(Cond);
  *d = (int)(e + 1);
}
```
處理類似 `x=y?a:b` 判斷式，保留 BZ 指令後面要填位址的空間，然後進行 recursive descent parser for expression，然後塞入 JMP 指令，且一樣保留後面要填位址的空間，展開最後的 expression 後，再填入要 JMP 的位址。

```clike=234
else if (tk == Lor) { next(); *++e = BNZ; d = ++e; expr(Lan); *d = (int)(e + 1); ty = INT; }
else if (tk == Lan) { next(); *++e = BZ;  d = ++e; expr(Or);  *d = (int)(e + 1); ty = INT; }
```
通常牽扯到 Branch/Jump 指令的都會使用類似這種方法：先保留目標位址，等待後面的指令都確定後，才會知道目標位址。

```clike=236
else if (tk == Or)  { next(); *++e = PSH; expr(Xor); *++e = OR;  ty = INT; }
else if (tk == Xor) { next(); *++e = PSH; expr(And); *++e = XOR; ty = INT; }
else if (tk == And) { next(); *++e = PSH; expr(Eq);  *++e = AND; ty = INT; }
else if (tk == Eq)  { next(); *++e = PSH; expr(Lt);  *++e = EQ;  ty = INT; }
else if (tk == Ne)  { next(); *++e = PSH; expr(Lt);  *++e = NE;  ty = INT; }
else if (tk == Lt)  { next(); *++e = PSH; expr(Shl); *++e = LT;  ty = INT; }
else if (tk == Gt)  { next(); *++e = PSH; expr(Shl); *++e = GT;  ty = INT; }
else if (tk == Le)  { next(); *++e = PSH; expr(Shl); *++e = LE;  ty = INT; }
else if (tk == Ge)  { next(); *++e = PSH; expr(Shl); *++e = GE;  ty = INT; }
else if (tk == Shl) { next(); *++e = PSH; expr(Add); *++e = SHL; ty = INT; }
else if (tk == Shr) { next(); *++e = PSH; expr(Add); *++e = SHR; ty = INT; }
```
這邊運算子的概念就是先將數值 push into stack frame，然後利用 operator-precedence 展開後面的 expression，展開的結果通常會存在 register，運算相關的 command 通常是拿 register 和 stack 做運算，然後將結果存回 register。

```clike=247
else if (tk == Add) {
  next(); *++e = PSH; expr(Mul);
  if ((ty = t) > PTR) { *++e = PSH; *++e = IMM; *++e = sizeof(int); *++e = MUL;  }
  *++e = ADD;
}
```
這邊同時處理 pointer 的加法：因為一次 add 一個「單位」。
```clike=
int *a;
c=a+b;
```
接下來來看 SUB：
```clike=252
else if (tk == Sub) {
  next(); *++e = PSH; expr(Mul);
  if (t > PTR && t == ty) { *++e = SUB; *++e = PSH; *++e = IMM; *++e = sizeof(int); *++e = DIV; ty = INT; }
  else if ((ty = t) > PTR) { *++e = PSH; *++e = IMM; *++e = sizeof(int); *++e = MUL; *++e = SUB; }
  else *++e = SUB;
}
```
發現多了一個 `if` 判斷式，這是在處理指標相減計算中間的個數。

```clike=261
else if (tk == Inc || tk == Dec) {
  if (*e == LC) { *e = PSH; *++e = LC; }
  else if (*e == LI) { *e = PSH; *++e = LI; }
  else { printf("%d: bad lvalue in post-increment\n", line); exit(-1); }
  *++e = PSH; *++e = IMM; *++e = (ty > PTR) ? sizeof(int) : sizeof(char);
  *++e = (tk == Inc) ? ADD : SUB;
  *++e = (ty == CHAR) ? SC : SI;
  *++e = PSH; *++e = IMM; *++e = (ty > PTR) ? sizeof(int) : sizeof(char);
  *++e = (tk == Inc) ? SUB : ADD;
  next();
}
```
post-increment 的做法就是先改變 variable 在 stack 中的值，然後再回復成原來的值，並且存在 register 中，最後再進行運算。

```clike=272
else if (tk == Brak) {
  next(); *++e = PSH; expr(Assign);
  if (tk == ']') next(); else { printf("%d: close bracket expected\n", line); exit(-1); }
  if (t > PTR) { *++e = PSH; *++e = IMM; *++e = sizeof(int); *++e = MUL;  }
  else if (t < PTR) { printf("%d: pointer type expected\n", line); exit(-1); }
  *++e = ADD;
  *++e = ((ty = t - PTR) == CHAR) ? LC : LI;
}
```
這部分是處理 array index 的取值到 register 中，中間必須處理一個 array 單位乘以 index，以計算最終位址。

#### stmt()

利用 recursive-descent 處理 "if-else", "while", "return", "{}", ";"。這部分的 source code 非常直觀，所以就不做贅述了。

### main() : declaration
`main()` 的上半部前面已經討論過了，接下來研究下半部，while-loop 的內部。

```clike=372
bt = INT; // basetype
if (tk == Int) next();
else if (tk == Char) { next(); bt = CHAR; }
else if (tk == Enum) {
  next();
  if (tk != '{') next();
  if (tk == '{') {
    next();
    i = 0;
    while (tk != '}') {
      if (tk != Id) { printf("%d: bad enum identifier %d\n", line, tk); return -1; }
      next();
      if (tk == Assign) {
        next();
        if (tk != Num) { printf("%d: bad enum initializer\n", line); return -1; }
        i = ival;
        next();
      }
      id[Class] = Num; id[Type] = INT; id[Val] = i++;
      if (tk == ',') next();
    }
    next();
  }
}
```
前面兩個 `if` 分別處理 INT and CHAR，最後一個 `if` 處理 enum：使用 local variable `i` 來處理 value of enum id。

```clike=396
while (tk != ';' && tk != '}') {
```
處理 Global variables and Functions。

```clike=399
if (tk != Id) { printf("%d: bad global declaration\n", line); return -1; }
if (id[Class]) { printf("%d: duplicate global definition\n", line); return -1; }
```
check 是否有重複宣告，是屬於 semantic analyzer 的一部份。

```clike=403
  if (tk == '(') { // function
```
這邊開始處理 Function declaration。

```clike=404
id[Class] = Fun;
id[Val] = (int)(e + 1);
```
Symbol table 的 class 設定為 Fun 類型，然後將 value 設定為 IR command 的 下個指令(Function 第一個指令)的位址。

```clike=407
while (tk != ')') {
  ty = INT;
  if (tk == Int) next();
  else if (tk == Char) { next(); ty = CHAR; }
  while (tk == Mul) { next(); ty = ty + PTR; }
  if (tk != Id) { printf("%d: bad parameter declaration\n", line); return -1; }
  if (id[Class] == Loc) { printf("%d: duplicate parameter definition\n", line); return -1; }
  id[HClass] = id[Class]; id[Class] = Loc;
  id[HType]  = id[Type];  id[Type] = ty;
  id[HVal]   = id[Val];   id[Val] = i++;
  next();
  if (tk == ',') next();
}
```
這邊會檢查是否重複宣告 parameter。然後將 Symbol table 的資訊 (Class, type, Val) 備份到 HClass, HType, HVal，這邊是實作在 function 內部更改 local variable's value 將不會影響到 Function 以外的 scope，parameter 也算是 Function local variables。

```clike=422
loc = ++i;
```
紀錄 local variable 開始的 order，前面的 order 是 parameter 個數。

```clike=424
while (tk == Int || tk == Char) {
  bt = (tk == Int) ? INT : CHAR;
  next();
  while (tk != ';') {
    ty = bt;
    while (tk == Mul) { next(); ty = ty + PTR; }
    if (tk != Id) { printf("%d: bad local declaration\n", line); return -1; }
    if (id[Class] == Loc) { printf("%d: duplicate local definition\n", line); return -1; }
    id[HClass] = id[Class]; id[Class] = Loc;
    id[HType]  = id[Type];  id[Type] = ty;
    id[HVal]   = id[Val];   id[Val] = ++i;
    next();
    if (tk == ',') next();
  }
  next();
}
```
處理 Function Local Variables declaration，一樣會備份 Symbol table 資訊。

```clike=440
*++e = ENT; *++e = i - loc;
while (tk != '}') stmt();
*++e = LEV;
```
處理 Function 內部的 statement，直到遇到屬於 function 的 `}`。`i-loc` 代表 local variables 的個數。

```clike=443
id = sym; // unwind symbol table locals
while (id[Tk]) {
  if (id[Class] == Loc) {
    id[Class] = id[HClass];
    id[Type] = id[HType];
    id[Val] = id[HVal];
  }
  id = id + Idsz;
}
```
回復所有 local variable 的 symbol table 資訊。

```clike=453
else {
    id[Class] = Glo;
    id[Val] = (int)data;
    data = data + sizeof(int);
}
```
處理 global declaration，symbol table 的 value 存的是 data section 的位址。

```clike=463
if (!(pc = (int *)idmain[Val])) { printf("main() not defined\n"); return -1; }
if (src) return 0;
```
最後檢查 `main()` function 是否有 define，以及是否要 run IR command on VM。

## Conclusion

c4 compiler 是個很好理解編譯器實作的入門磚，但還是會建議先把理論基礎建立起來，包含 compilation, assembly, linker, loader, stack frame, function call。這些理論剛開始讀來會相當生硬，但是透過理解 c4 compiler，將會對這些理論有更深刻的認知。

我們盡量 trace 每個細節，但難免有些遺漏之處或是理解錯誤的地方，如有任何建議，請再 Mail [lukyandy3162@gmail.com](mailto:lukyandy3162@gmail.com) 告知我，感謝！

