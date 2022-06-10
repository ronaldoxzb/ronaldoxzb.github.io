---
title: javascript运行原理(一)
date: 2022-03-11 14:29:32
tags:
  - javascript
  - node
  - v8
  - parser
  - interpreter
  - compiler
categories:
  - FrontEnd-Tec
---

#### JS 的语言组成

- 基本语法：C
- 数据类型: Java 内存管理：
- 函数式编程: Scheme 函数是第一等公民
- 原型继承：Self 基于原型 prototype 的继承机制

<img width="500" align="center" src="/images/js-runtime/1.png" alt="ronaldoxzb" />

#### 语言特性

- 动态类型语言 JS 编译环境 JIT Just In Time Compilation `在运行时编译成机器代码`
- 静态类型语言 C++ 编译环境 AOT Ahead Of Time `在运行前提前生成好机器代码`

#### JS 引擎

将 js 代码编译成机器能够识别的代码，常见的有：

- 谷歌 - V8
- 苹果 - javaScriptCore
- 火狐 - SpideMonkey
- QuickJs
- FaceBook - Hermes

<img width="500" align="center" src="/images/js-runtime/2.png" alt="ronaldoxzb" />
#### 奇葩的JS

```JavaScript
('b'+'a'+ + 'a'+'a'+ +'').toLowerCase() //"banana0"
0 == '0'  //true
0 == []  //true
'0' == []  //false
typeof NaN  //number
99999999999 //100000000000
0.1+0.2==0.3 //false 精度丢失
Math.max() //-Infinity  负无穷
Math.min() //Infinity  正无穷
[] + []  //""
[] + {}  //"【object object】"
{} + []  // 0
true + true + true===3  //true
true - true  //0
(!+[]+[]+![]).length //9 "truefalse"
9 + "1"  //91
91 - "1"  //90
[] == false  //true
0==false  //true
undefined == false  //true
NaN == false  //true
"" == false  //true  包括多空格
null == false  //true

```

#### JS 编译流程

1. parser - 将 js 源码通过 parser 解析器生成 AST 抽象语法树
   <img width="500" align="center" src="/images/js-runtime/3.png" alt="ronaldoxzb" />
2. interpreter - 通过解释器将 AST 编程成功字节码 bytecode (字节码与平台无关，是中间层，可以在各个平台上运行)
   <img width="500" align="center" src="/images/js-runtime/4.png" alt="ronaldoxzb" />
3. compiler - 根据当前平台编译出相应的机器代码也就是汇编代码 常见的操作系统平台有：IA32 X64 ARM MIPS
   <img width="500" align="center" src="/images/js-runtime/5.png" alt="ronaldoxzb" />


[参考视频（原文作者）](https://www.bilibili.com/video/BV1vh411Z7QG)
