---
title: javascript运行原理(三)
date: 2022-03-15 19:22:01
tags:
  - javascript
  - node
  - v8
  - EventLoop
categories:
  - FrontEnd-Tec
---

#### 介绍

栈：先进后出，进栈，出栈

队列：先进先出

#### 概念

调用栈是 JS 引擎追踪函数执行流程的一种机制，当执行环境中调用了多个函数时，通过这种机制，我们能够追踪到哪个函数正在执行，执行的函数体又调用了哪个函数

##### 例子

`调用栈，调用函数执行才回入栈`

1. push average()

2. push sum()

3. shift sum()

4. shift average()

5. push console.log()

6. shift console.log()

```javascript
function sum(a, b) {
  return a + b;
}
function average(a, b) {
  const aver = sum(a, b) / 2;
  return aver;
}
const num = average(3, 5);
console.log(num);
```

#### Event Loop

```javascript
(function test() {
  setTimeout(function () {
    console.log(4);
  }, 0);
  new Promise(function executor(resolve) {
    console.log(1);
    for (var i = 0; i < 10000; i++) {
      resolve(i);
    }
    console.log(2);
  }).then(function (res) {
    console.log(res);
    console.log(5);
  });
  console.log(3);
})();
```

[参考视频（原文作者）](https://www.bilibili.com/video/BV13k4y1y7vU)
