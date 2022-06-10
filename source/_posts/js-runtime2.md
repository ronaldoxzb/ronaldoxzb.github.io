---
title: javascript运行原理(二)
date: 2022-03-12 09:01:16
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

#### 介绍

<img width="500" align="center" src="/images/js-runtime/7.png" alt="ronaldoxzb" />

##### 哪些程序用到 V8

- Chrome 浏览器的 JS 引擎是 V8
- Nodejs 的运行时环境是 V8
- electron 的底层引擎是 V8 【ɪˈlektrɑːn】 跨平台桌面应用开发工具

blink 是渲染引擎，V8 是 JS 引擎

访问 Dom 的接口是由 Blink 提供的
<img width="500" align="center" src="/images/js-runtime/8.png" alt="ronaldoxzb" />

#### 功能

`接收 JavaScript 代码，编译代码后执行 C++程序，编译后的代码可以在多种操作系统多种处理器上运行。`

1. 编译和执行 JS 代码
2. 处理调用栈
3. 内存分配
4. 垃圾回收

#### V8 的 js 编译和执行

<img width="500" align="center" src="/images/js-runtime/9.png" alt="ronaldoxzb" />

1. 解析器 parser：源代码 -> AST
2. 解释器 interpreter：AST -> `bytecode` 或（直接执行`bytecode` `bytecode`是可是直接执行的，但是效率没有机器代码效率高）
3. 编译器 compiler：编译出机器代码（相比于`bytecode`运行更加高效）

V8 版本 5.9 之前没有解释器，但是有两个编译器

#### 5.9 版本的 V8

<img width="500" align="center" src="/images/js-runtime/11.png" alt="ronaldoxzb" />
1. parser 解释器生成抽象语法树 AST

2. `full-codegen`（基准编译器）编译器将 `AST` 编译成机器代码（未被优化）

3. `分析器线程`收集足够数据（目的：帮助`Crankshaft`（优化编译器）做代码优化 ）

4. 需要优化的**源码**重新解析生成`AST`，然后由`Crankshaft`生成优化过后的机器代码

##### 缺点

1. 没有`bytecode`而是直接编译成机器代码,机器码会占用大量的内存
2. 缺少中间层机器码，无法实现一些优化策略
3. 无法很好的支持和优化 JS 的新语特性，无法拥抱未来

<img width="200" align="center" src="/images/js-runtime/12.png" alt="ronaldoxzb" />

#### 新版本的 V8

<img width="500" align="center" src="/images/js-runtime/13.png" alt="ronaldoxzb" />

1. parser 解析器 生成 AST 抽象语法树
2. interpreter 解释器 `Ignition` 生成 `byteCode` 字节码 并直接执行
3. 清除 AST 释放内存空间
4. 得到 25% - 50%的等效机器代码大小
5. compiler 运行过程中，解释器收集优化信息发送给编译器 `TurboFan`
6. 重新生成机器码
7. 有些`热点函数`变更会由优化后的机器码还原成字节码 也就是 `deoptimization` 回退字节码操作执行

<img width="500" align="center" src="/images/js-runtime/14.png" alt="ronaldoxzb" />

##### 优化点：

1. 值声明未调用，不会被解析生成 AST
2. 函数只被调用一次，bytcode 直接被解释执行，不会进入到编译优化阶段
3. 函数被调用多次，Igniton 会收集函数类型信息，可能会被标记为热点函数，可能被编译成优化后的机器代码

##### 好处：

1. 由于一开始不需要直接编译成机器码，生成了中间层的字节码，从而节约了时间
2. 优化编译阶段，不需要从源码重新解析,直接通过字节码进行优化，也可以 deoptimization 回退操作

```javascript
function sum(x, y) {
  return x + y;
}
sum(1, 2);
sum(3, 4);
sum(5, 6);
sum("7", "8"); //会回退字节码操作执行
```

[参考视频（原文作者）](https://www.bilibili.com/video/BV1zV411z7RX)
