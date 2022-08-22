---
title: 深入JS
date: 2022-07-28 13:40:40
tags:
  - javascript
categories:
  - FrontEnd-Tec
---

### 一、作用域及作用域链引出闭包

```javascript
function addABC() {
  var a = 1,
    b = 2;

  function add() {
    return a + b + c;
  }
  return add;
}

var c = 3;

var globalAdd = addABC();

console.log(globalAdd()); // 6
```

- `像 a、b、c 这样在函数中被使用，但它既不是函数参数、也不是函数的局部变量，而是一个不属于当前作用域的变量，此时它相对于当前作用域来说，就是一个自由变量。而像 add 这样引用了自由变量的函数，就叫闭包`

- LHS、RHS，是引擎在执行代码的时候，查询变量的两种方式。
  - name 变量出现在赋值操作的左侧，它就属于 LHS。LHS 意味着 变量赋值或写入内存，
  - RHS 意味着 变量查找或从内存中读取

#### 词法作用域及动态作用域

- JS 的作用域遵循的就是词法作用域模型。
- 词法作用域： 在代码书写的时候完成划分，作用域链沿着它定义的位置往外延伸
- 动态作用域： 在代码运行时完成划分，作用域链沿着它的调用栈往外延伸
- eval with 修改词法作用域

```javascript
var name = "xiuyan";

function showName() {
  console.log(name);
}

function changeName() {
  var name = "BigBear";
  showName();
}

changeName();
```

#### 闭包应用

- 模拟私有变量的实现

```javascript
// 利用闭包生成IIFE(自执行函数)，返回 User 类
const User = (function () {
  // 定义私有变量_password
  let _password;

  class User {
    constructor(username, password) {
      // 初始化私有变量_password
      _password = password;
      this.username = username;
    }

    login() {
      // 这里我们增加一行 console，为了验证 login 里仍可以顺利拿到密码
      console.log(this.username, _password);
      // 使用 fetch 进行登录请求，同上，此处省略
    }
  }

  return User;
})();

let user = new User("xiuyan", "xiuyan123");

console.log(user.username);
console.log(user.password);
console.log(user._password);
user.login();
```

`像 password 这样的变量，我们希望它仅在对象内部生效，无法从外部触及，这样的变量，就是私有变量。`

- 偏函数与柯里化（需要多个入参的函数，转化为需要更少入参的函数的方法。）
  - 柯里化是把接受 n 个参数的 1 个函数改造为只接受 1 个参数的 n 个互相嵌套的函数的过程。也就是 fn (a, b, c)会变成 fn (a)(b)(c)。
  - 偏函数应用相比之下就 “随意” 一些了。偏函数是说，固定你函数的某一个或几个参数，然后返回一个新的函数（这个函数用于接收剩下的参数）。你有 10 个入参，你可以只固定 2 个入参，然后返回一个需要 8 个入参的函数
  - 两者动机就是为了 “记住” 函数的一部分参数，实现思路就是走闭包

```javascript
// 柯里化
function generateName(prefix) {
  return function (type) {
    return function (itemName) {
      return prefix + type + itemName;
    };
  };
}

// 生成大卖网商品名专属函数
var salesName = generateName("大卖网");

// “记住”prefix，生成大卖网母婴商品名专属函数
var salesBabyName = salesName("母婴");

// "记住“prefix和type，生成洗菜网生鲜商品名专属函数
var vegFreshName = generateName("洗菜网")("生鲜");

// 输出 '大卖网母婴奶瓶'
salesBabyName("奶瓶");
// 输出 '洗菜网生鲜菠菜'
vegFreshName("菠菜");

// 啥也不记，直接生成一个商品名
var itemFullName = generateName("洗菜网")("生鲜")("菠菜");
// 偏函数
function generateName(prefix) {
  return function (type, itemName) {
    return prefix + type + itemName;
  };
}

// 把3个参数分两部分传入
var itemFullName = generateName("大卖网")("母婴", "奶瓶");
```

### 二、垃圾回收机制

- 垃圾回收算法
  - 引用计数法（内存中的每一个值都会对应一个引用计数。当垃圾收集器感知到某个值的引用计数为 0 时，就判断它 “没用” 了，随即这块内存就会被释放)`引用计数法无法甄别循环引用场景下的“垃圾”！`
  - 标记清除法
    - 标记阶段：垃圾收集器会先找到根对象，在浏览器里，根对象是 Window；在 Node 里，根对象是 Global。从根对象出发，垃圾收集器会扫描所有可以通过根对象触及的变量，这些对象会被标记为“可抵达 ”。
    - 清除阶段： 没有被标记为“可抵达” 的变量，就会被认为是不需要的变量，这波变量会被清除
- 内存泄漏成因分析
  - “手滑”导致的全局变量
  - 忘记清除的 setInterval 和 setTimeout
  - 清除不当的 DOM

### 三、this 指向问题 **【this 指向调用它所在方法的那个对象】**

**说得更通俗点， 谁调的函数，this 就归谁。**当调用方法没有明确对象时，this 就指向全局对象。在浏览器中，指向 window；在 Node 中，指向 Global。

```javascript
// 声明位置
var me = {
  name: "xiuyan",
  hello: function () {
    console.log(`你好，我是${this.name}`);
  },
};

var you = {
  name: "xiaoming",
  hello: function () {
    var targetFunc = me.hello;
    // 没有方法调用它所以它他的this指向Window
    targetFunc();
  },
};
var name = "BigBear";

// 调用位置(只是访问了hello属性，并没有调用targetFunc方法)
you.hello();
// me对象调用它，所以指向me
me.hello();
```

`特殊情境下的 this 指向`

- 立即执行函数（IIFE）
- setTimeout 中传入的函数
- setInterval 中传入的函数

`箭头函数`**当我们将普通函数改写为箭头函数时，箭头函数的 this 会在书写阶段（即声明位置）就绑定到它父作用域的 this 上。**

```javascript
var a = 1;
var obj = {
  a: 2,
  func2: () => {
    console.log(this.a);
  },

  func3: function () {
    console.log(this.a);
  },
};

// func1
var func1 = () => {
  console.log(this.a);
};

// func2
var func2 = obj.func2;
// func2 = ()=> {
//     console.log(this.a)
// }
// func3 = function(){
//     console.log(this.a)
// }
// func3
var func3 = obj.func3;

func1(); // 1
func2(); // 1
func3(); // 1
obj.func2(); // 1
obj.func3(); // 2
```

### 四、call、apply 和 bind，用来改变函数的 this 指向

- call、apply 和 bind 之间的区别比较大，前者在改变 this 指向的同时，也会把目标函数给执行掉；后者则只负责改造 this，不作任何执行操作。

- call 和 apply 之间的区别，则体现在对入参的要求上。前者只需要将目标函数的入参逐个传入即可，后者则希望入参以数组形式被传入。

`简易版`

```javascript
function test(surName, b) {
  console.log(`${this.name} ${surName}`);
  console.log(b);
  return true;
}

Function.prototype.myCall = function (context, ...args) {
  if (typeof this !== "function") {
    console.error("type error!");
  }
  context = context || window;
  context.func = this;
  const result = context.func(...args);
  delete context.func;
  return result;
};

Function.prototype.myApply = function (context, args) {
  if (typeof this !== "function") {
    console.error("type error!");
  }
  context = context || window;
  context.func = this;
  context.func(...args);
  delete context.func;
};

Function.prototype.myBind = function (context, ...args) {
  if (typeof this !== "function") {
    console.error("type error!");
  }
  context = context || window;
  that = this;
  return function fn() {
    that.apply(context, ...args);
  };
};
// call有返回结果调用
const res = test.myCall({ name: "ronaldo" }, "messi", "xzb");
test.myCall(null, "哈哈哈哈");
console.log(res);
// 只调用没有返回结果
test.myApply({ name: "ronaldoxzb" }, ["messixzb", "xzb"]);
// 调用myBind无返回值
const myBindRes = test.myBind({ name: "ronaldoxzb" }, "messixzb", "xzb")();
```

### 五、执行上下文与调用栈

#### 上下文的创建和组成

_全局上下文 —— 全局代码所处的环境，不在函数中的代码都在全局执行上下文中_
_函数上下文 —— 在函数调用时创建的上下文_

- 创建阶段 —— 执行上下文的初始化状态，此时一行代码都还没有执行，只是做了一些准备工作
  - 创建的时机 —— 全局上下文在进入脚本之初就被创建，而函数上下文则是在函数调用时被创建
  - 全局上下文仅在代码刚开始被解释的时候创建一次；而函数上下文由脚本里函数调用的多少决定，理论上可以创建无数次
- 执行阶段 —— 逐行执行脚本里的代码

```javascript
var name = "xiuyan";
var tel = "123456";

function getMe() {
  return {
    name: name,
    tel: tel,
  };
}
// 创建阶段
// Global Execution Context
// window : global object
// this: window
// name: undefined
// tel: undefined
// getMe: fn()
```

#### 调用栈

### 六、原型编程范式 **原型编程范式的核心思想就是利用实例来描述对象，用实例作为定义对象和继承的基础。**

### 八、异步编程模型与异步解决方案

- 解决回调地狱问题
- 异步进化史 回调函数 —> Promise —> Generator —> async/await。

### 九、Promise 考点

- 考察 Promise 特性（问答题）
- 给出一段 Promise 代码，问输出结果（这类题的关键在于搞清楚 Promise 中不同任务的执行时机）
- 深度考察 Promise 原理（终极版本就是让你手写一个 Promise）

**Promise 对象是一个代理对象。它接受你传入的 executor（执行器）作为入参，允许你把异步任务的成功和失败分别绑定到对应的处理方法上去。一个 Promise 实例有三种状态：**

- pending 状态，表示进行中。这是 Promise 实例创建后的一个初始态；
- fulfilled 状态，表示成功完成。这是我们在执行器中调用 resolve 后，达成的状态；
- rejected 状态，表示操作失败、被拒绝。这是我们在执行器中调用 reject 后，达成的状态；
  **Promise 实例的状态是可以改变的，但它只允许被改变一次。当我们的实例状态从 pending 切换为 rejected 后，就无法再扭转为 fulfilled，反之同理。当 Promise 的状态为 resolved 时，会触发其对应的 then 方法入参里的 onfulfilled 函数；当 Promise 的状态为 rejected 时，会触发其对应的 then 方法入参里的 onrejected 函数。**

```javascript
function myPromise(executor) {
  this.value = null;
  this.reason = null;
  this.status = "pending";

  this.onResolvedQueue = [];
  this.onRejectedQueue = [];

  const self = this;

  function resolve(value) {
    if (self.status !== "pending") {
      return;
    }
    self.value = value;
    self.status = "resolved";

    setTimeout(function () {
      self.onResolvedQueue.forEach((resolved) => resolved(self.value));
    });
  }

  function reject(reason) {
    if (self.status !== "pending") {
      return;
    }
    self.reason = reason;
    self.status = "rejected";

    setTimeout(function () {
      self.onRejectedQueue.forEach((rejected) => reject(self.value));
    });
  }

  executor(resolve, reject);
}

myPromise.prototype.then = function (onResolved, onRejectd) {
  if (typeof onResolved !== "function") {
    onResolved = function (x) {
      return x;
    };
  }
  if (typeof onRejectd !== "function") {
    onRejectd = function (e) {
      throw e;
    };
  }
  var self = this;
  if (self.status === "resolved") {
    onResolved(self.value);
  } else if (self.status === "rejected") {
    onRejectd(self.reason);
  } else if (self.status === "pending") {
    self.onResolvedQueue.push(onResolved);
    self.onRejectedQueue.push(onRejectd);
  }

  return this;
};

const cutePromise = new myPromise(function (resolve, reject) {
  reject("凉了！");
});
cutePromise
  .then(
    (value) => {
      console.log(value);
      console.log("我是第 1 个任务");
    },
    (err) => {
      console.log("我是第 1 个任务", err);
    }
  )
  .then(
    (value) => {
      console.log("我是第 2 个任务", value);
      // console.log('我是第 2 个任务')
    },
    (err) => {
      console.log("我是第 2 个任务", err);
    }
  );
```

### 十、 ES2015+考点

#### 对象与数组的解构

```javascript
const xzb = [1, 2, 3];

const [a, b, c] = xzb;

const school = {
  classes: {
    stu: {
      name: "Bob",
      age: 24,
    },
  },
};

const {
  classes: {
    stu: { name: newName },
  },
} = school;
newName; // Bob
```

#### 对象扩展运算

```javascript
// 对象扩展运算
const me = {
  name: "xiuyan",
  age: 24,
};

const meCopy = { ...me };

meCopy; // {name: "xiuyan", age: 24}
// 数组扩展运算
console.log(...["haha", "hehe", "xixi"]); // haha hehe xixi
```

#### 类数组的转换

```javascript
const arrayLike = { 0: "Bob", 1: "Lucy", 2: "Daisy", length: 3 };
// Array原型上的slice方法—— 这个方法如果不传参数的话会返回原数组的一个拷贝，因此可以用此方法转换类数组到数组：
const arr = Array.prototype.slice.call(arrayLike);
// Array.from方法——这是 ES6 新增的一个数组方法，专门用来把类数组转为数组：
const arr = Array.from(arrayLike);
```

### 十一、 DOM 事件体系

- 节流（Throttle）在某段时间内，不管你触发了多少次回调，我都只认第一次，并在计时结束时给予响应。

```javascript
// fn是我们需要包装的事件回调, interval是时间间隔的阈值
function throttle(fn, interval) {
  // last为上一次触发回调的时间
  let last = 0;

  // 将throttle处理结果当作函数返回
  return function () {
    // 保留调用时的this上下文
    let context = this;
    // 保留调用时传入的参数
    let args = arguments;
    // 记录本次触发回调的时间
    let now = +new Date();

    // 判断上次触发的时间和本次触发的时间差是否小于时间间隔的阈值
    if (now - last >= interval) {
      // 如果时间间隔大于我们设定的时间间隔阈值，则执行回调
      last = now;
      fn.apply(context, args);
    }
  };
}
// 用throttle来包装scroll的回调
const better_scroll = throttle(() => console.log("触发了滚动事件"), 1000);
document.addEventListener("scroll", better_scroll);
```

- 防抖（Debounce）在某段时间内，不管你触发了多少次回调，我都只认最后一次。

```javascript
// fn是我们需要包装的事件回调, delay是每次推迟执行的等待时间
function debounce(fn, delay) {
  // 定时器
  let timer = null;

  // 将debounce处理结果当作函数返回
  return function () {
    // 保留调用时的this上下文
    let context = this;
    // 保留调用时传入的参数
    let args = arguments;
    // 每次事件被触发时，都去清除之前的旧定时器
    if (timer) {
      clearTimeout(timer);
    }
    // 设立新定时器
    timer = setTimeout(function () {
      fn.apply(context, args);
    }, delay);
  };
}
// 用debounce来包装scroll的回调
const better_scroll = debounce(() => console.log("触发了滚动事件"), 1000);
document.addEventListener("scroll", better_scroll);
```

#### DOM 事件流

- 事件捕获阶段
- 目标阶段
- 事件冒泡阶段

#### 自定义事件

```javascript
// 实现在点击A之后，B 和 C 都能感知到 A 被点击了，并且做出相应的行为——就像这个点击事件是点在 B 和 C 上一样。
<body>
  <div id="divA">我是A</div>
  <div id="divB">我是B</div>
  <div id="divC">我是C</div>
</body>;

// 创建一个本来不存在的"clickA"事件，来表示 A 被点击了，咱们可以这么写：
var clickAEvent = new Event("clickA");

// 获取 divB 元素
var divB = document.getElementById("divB");
// divB 监听 clickA 事件
divB.addEventListener("clickA", function (e) {
  console.log("我是小B，我感觉到了小A");
  console.log(e.target);
});

// 获取 divC 元素
var divC = document.getElementById("divC");
// divC 监听 clickA 事件
divC.addEventListener("clickA", function (e) {
  console.log("我是小C，我感觉到了小A");
  console.log(e.target);
});

// A 元素的监听函数也得改造下
divA.addEventListener("click", function () {
  console.log("我是小A");
  // 注意这里 dispatch 这个动作，就是我们自己派发事件了
  divB.dispatchEvent(clickAEvent);
  divC.dispatchEvent(clickAEvent);
});
```

#### 事件代理

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
</head>
<body>
  <ul id="poem">
    <li>鹅鹅鹅</li>
    <li>曲项向天歌</li>
    <li>白毛浮绿水</li>
    <li>红掌拨清波</li>
    <li>锄禾日当午</li>
    <li>汗滴禾下土</li>
    <li>谁知盘中餐</li>
    <li>粒粒皆辛苦</li>
    <li>背不动了</li>
    <li>我背不动了</li>
  </ul>
</body>
</html>
<script>
  // 获取 li 列表下面做法0分
  var liList = document.getElementsByTagName('li')
  // 逐个安装监听函数
  for (var i = 0; i < liList.length; i++) {
    liList[i].addEventListener('click', function (e) {
      console.log(e.target.innerHTML)
    })
  }
//  正确做法
  var ul = document.getElementById('poem')
  ul.addEventListener('click', function(e){
    console.log(e.target.innerHTML)
  })
</script>

```

### 十二、浏览器渲染原理

目前市面上常见的浏览器内核有以下几种：

Trident（IE）
Gecko（火狐）
Blink（Chrome、Opera）
Webkit（Safari）。
**渲染引擎处理**

1. HTML 解析 =>DOM 树

在这一步浏览器对 HTML 文档进行解析，并在解析 HTML 的过程中发出了页面渲染所需的各种外部资源请求。

2. CSS 解析 => CSSOM 树
   浏览器将识别并加载所有的 CSS 样式信息。

3. 样式与结构合并 =>渲染树(DOM 树与 CSSOM 树“合体” ——渲染树)
   将样式信息和文档结构合并，最终生成页面 render 树（:after :before 这样的伪元素会在这个环节被构建到 DOM 树中）。

4. 布局阶段 =>盒模型
   页面中所有元素的相对位置信息，大小等信息均在这一步得到计算。

5. 页面绘制 => 目标界面
   在这一步中浏览器会根据我们前面处理出来的结果，把每一个页面图层转换为像素，并对所有的媒体文件进行解码。

- 重排 _重排多数情况下是由对元素几何属性的修改引发的_ 如何触发重排
  - 改变 DOM 树的结构
  - 获取一些特定属性的值（重要）
    如 offsetTop、offsetLeft、 offsetWidth、offsetHeight、scrollTop、scrollLeft、scrollWidth、scrollHeight、clientTop、clientLeft、clientWidth、clientHeight 等属性（挑几个背下来，答题的时候不要哑了）。
- 重绘*当我们对 DOM 的修改导致了样式的变化、却并未影响其几何属性（比如修改了颜色或背景色）*
  `都会更新cssom树 => 更新渲染树 => 重新绘制` 重绘不会触发重新布局

### 十三、浏览器中的 Event-Loop 机制解析

**函数调用栈、宏任务（macro-task)队列和微任务(micro-task)队列。**

- 当引擎第一次遇到 JS 代码时，会产生一个全局执行上下文并压入调用栈。后面每遇到一个函数调用，就会往栈中压入一个新的函数上下文。JS 引擎会执行栈顶的函数，执行完毕后，弹出对应的上下文

- 宏任务队列：常见的 macro-task 比如： setTimeout、setInterval、 setImmediate（node 独有）、 script（整体代码）、I/O 操作等。

- 微任务队列：常见的 micro-task 比如: process.nextTick（node 独有）、Promise、MutationObserver 等

### 十四、node 中的 Event-Loop

**Node 整体上由这三部分组成：**
1:应用层：这一层就是大家最熟悉的 Node.js 代码，包括 Node 应用以及一些标准库。

2:桥接层：Node 底层是用 C++ 来实现的。桥接层负责封装底层依赖的 C++ 模块的能力，将其简化为 API 向应用层提供服务。

3:底层依赖：这里就是最最底层的 C++ 库了，支撑 Node 运行的最基本能力在此汇聚。其中需要特别引起大家注意的就是 V8 和 libuv：

V8 是 JS 的运行引擎，它负责把 JavaScript 代码转换成 C++，然后去跑这层 C++ 代码。
libuv：它对跨平台的异步 I/O 能力进行封装，同时也是我们本节的主角：Node 中的事件循环就是由 libuv 来初始化的。

**libuv 六个循环阶段**

1:timers 阶段：执行 setTimeout 和 setInterval 中定义的回调；
2:pending callbacks：直译过来是“被挂起的回调”，如果网络 I/O 或者文件 I/O 的过程中出现了错误，就会在这个阶段处理错误的回调（比较少见，可以略过）；
3:idle, prepare：仅系统内部使用。这个阶段我们开发者不需要操心。（可以略过）；
4:poll （轮询阶段）：重点阶段，这个阶段会执行 I/O 回调，同时还会检查定时器是否到期；
5:check（检查阶段）：处理 setImmediate 中定义的回调；
6:close callbacks：处理一些“关闭”的回调，比如 socket.on('close', ...)就会在这个阶段被触发。
`Node 中的事件循环流程`

- 1:执行全局的 Script 代码（与浏览器无差）；
- 2:把微任务队列清空：注意，Node 清空微任务队列的手法比较特别。在浏览器中，我们只有一个微任务队列需要接受处理；但在 Node 中，有两类微任务队列：next-tick 队列和其它队列。其中这个 next-tick 队列，专门用来收敛 process.nextTick 派发的异步任务。在清空队列时，优先清空 next-tick 队列中的任务，随后才会清空其它微任务；
- 3:开始执行 macro-task（宏任务）。注意，Node 执行宏任务的方式与浏览器不同：在浏览器中，我们每次出队并执行一个宏任务；而在 Node 中，我们每次会尝试清空当前阶段对应宏任务队列里的所有任务（除非达到了系统限制）；
  `整体来看，Node 中每次执行异步任务都是以批量的形式，“一队一队”地执行。循环形式为：宏任务队列 -> 微任务队列 -> 宏任务队列 —> 微任务队列… 这样交替进行。`

### 十五、虚拟 DOM

虚拟 DOM 这一层是用 JS 实现的。也就是说在这个阶段所有的更改、对比操作都是纯 JS 层面的计算。JS vs DOM 操作，其性能消耗完全不在一个量级上。

**数据 + 模板 = 虚拟 DOM -> diff 新旧两套虚拟 DOM 的差异，得到补丁集 -> 把“补丁”打到真实 DOM 上**

### 十六、设计模式

- 发布订阅

  - vue 总线机制

  ```javascript
  class EventEmitter {
    constructor() {
      // handlers是一个map，用于存储事件与回调之间的对应关系
      this.handlers = {};
    }

    // on方法用于安装事件监听器，它接受目标事件名和回调函数作为参数
    on(eventName, cb) {
      // 先检查一下目标事件名有没有对应的监听函数队列
      if (!this.handlers[eventName]) {
        // 如果没有，那么首先初始化一个监听函数队列
        this.handlers[eventName] = [];
      }

      // 把回调函数推入目标事件的监听函数队列里去
      this.handlers[eventName].push(cb);
    }

    // emit方法用于触发目标事件，它接受事件名和监听函数入参作为参数
    emit(eventName, ...args) {
      // 检查目标事件是否有监听函数队列
      if (this.handlers[eventName]) {
        // 如果有，则逐个调用队列里的回调函数
        this.handlers[eventName].forEach((callback) => {
          callback(...args);
        });
      }
    }
  }
  ```

- 单例模式
  - 实现一个模态框
  ```javascript
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <title>单例模式真题解读</title>
  </head>
  <style>
      #modal {
          height: 300px;
          width: 300px;
          line-height: 300px;
          position: fixed;
          left: 50%;
          top: 50%;
          transform: translate(-50%, -50%);
          border: 1px solid red;
          text-align: center;
      }
  </style>
  <body>
      <button id='open'>打开 Modal 弹层</button>
      <button id='close'>关闭 Modal 弹层</button>
  </body>
  // 点击打开按钮展示模态框
  document.getElementById("open").addEventListener("click", function () {
    // 未点击则不创建modal实例，避免不必要的内存占用;此处不用 new Modal 的形式调用也可以，和 Storage 同理
    const modal = createModal();
    modal.style.display = "block";
  });
  // 点击关闭按钮隐藏模态框
  document.getElementById('close').addEventListener('click', function() {
  const modal = createModal()
  if(modal) {
  modal.style.display = 'none'
  }
  })
  // 核心逻辑，这里采用了闭包思路来实现单例模式
  const createModal = (function() {
  let modal = null
  return function() {
  if(!modal) {
  modal = document.createElement('div')
  modal.innerHTML = 'Modal 模态框，全局唯一'
  modal.id = 'modal'
  modal.style.display = 'none'
  document.body.appendChild(modal)
  }
  return modal
  }
  })()
  ```

### 十七、手写题目
- 1. 防抖

```javascript
function debounce(func, ms = 1000) {
  let timer;
  return function (...args) {
    if (timer) {
      clearTimeout(timer)
    }
    timer = setTimeout(() => {
      func.apply(this, args)
    }, ms)
  }
}

// 测试
const task = () => { console.log('run task') }
const debounceTask = debounce(task, 1000)
window.addEventListener('scroll', debounceTask)

```
- 2. 节流

```javascript
function throttle(func, ms = 1000) {
  let canRun = true
  return function (...args) {
    if (!canRun) return
    canRun = false
    setTimeout(() => {
      func.apply(this, args)
      canRun = true
    }, ms)
  }
}

// 测试
const task = () => { console.log('run task') }
const throttleTask = throttle(task, 1000)
window.addEventListener('scroll', throttleTask)

```
- 3. new
```javascript
function myNew(Func, ...args) {
  const instance = {};
  if (Func.prototype) {
    Object.setPrototypeOf(instance, Func.prototype)
  }
  const res = Func.apply(instance, args)
  if (typeof res === "function" || (typeof res === "object" && res !== null)) {
    return res
  }
  return instance
}

// 测试
function Person(name) {
  this.name = name
}
Person.prototype.sayName = function() {
  console.log(`My name is ${this.name}`)
}
const me = myNew(Person, 'Jack')
me.sayName()
console.log(me)

```

- 深度克隆

```javascript
const obj = [
  {
    name_key_nb: "ronaldo",
    age: 28,
    project: {
      vue: ["good", "bad"],
      react: "good",
      webpack: {
        devServer: 100,
      },
    },
  },
];

const deepClone = (target) => {
  let result = target instanceof Array ? [] : {};
  if (typeof target === "object") {
    for (const key in target) {
      if (Object.hasOwnProperty.call(target, key)) {
        const element = target[key];
        if (typeof element === "object") {
          const newKey = toCamel(key);
          result[newKey] = deepClone(element);
        } else {
          const newKey = toCamel(key);
          result[newKey] = element;
        }
      }
    }
  }

  return result;
};

const deepClone1 = (obj) => {
  if (typeof obj !== "object") return;
  let newObj = obj instanceof Array ? [] : {};
  for (let key in obj) {
    if (typeof obj[key] === "object") {
      newObj[key] = deepClone1(obj[key]);
    } else {
      newObj[key] = obj[key];
    }
  }
  return newObj;
};
// 下划线转驼峰
const toCamel = (str) => {
  const arr = str.split("_");
  for (var i = 0; i < arr.length; i++) {
    arr[i] =
      arr[i].charAt(0).toUpperCase() + arr[i].substr(1, arr[i].length - 1);
  }
  return arr.join("");
};
const newObj = deepClone(obj);
// const obj1 = obj
// console.log(obj)
obj[0].project.react = "messi";
console.log(obj);
console.log(newObj);
```

- 数组间的转换

```javascript
let datas = [
  { id: 0, parentId: null, name: "一级菜单A", rank: 1 },
  { id: 1, parentId: 0, name: "一级菜单A", rank: 1 },
  { id: 2, parentId: 0, name: "一级菜单B", rank: 1 },
  { id: 3, parentId: 0, name: "一级菜单C", rank: 1 },
  { id: 4, parentId: 1, name: "二级菜单A-A", rank: 2 },
  { id: 5, parentId: 1, name: "二级菜单A-B", rank: 2 },
  { id: 6, parentId: 2, name: "二级菜单B-A", rank: 2 },
  { id: 7, parentId: 4, name: "三级菜单A-A-A", rank: 3 },
  { id: 8, parentId: 7, name: "四级菜单A-A-A-A", rank: 4 },
  { id: 9, parentId: 8, name: "五级菜单A-A-A-A-A", rank: 5 },
  { id: 10, parentId: 9, name: "六级菜单A-A-A-A-A-A", rank: 6 },
  { id: 11, parentId: 10, name: "七级菜单A-A-A-A-A-A-A", rank: 7 },
  { id: 12, parentId: 11, name: "八级菜单A-A-A-A-A-A-A-A", rank: 8 },
  { id: 13, parentId: 12, name: "九级菜单A-A-A-A-A-A-A-A-A", rank: 9 },
  { id: 14, parentId: 13, name: "十级菜单A-A-A-A-A-A-A-A-A-A", rank: 10 },
];

const toTree = (array = [], parentId = null) => {
  let arr = [];
  array.forEach((item) => {
    if (item.parentId === parentId) {
      item.children = toTree(array, item.id);
      arr.push(item);
    }
  });
  return arr;
};
const toTree1 = (arr) => {
  const result = [];
  const map = {};
  arr.forEach((i) => (map[i.id] = i));
  // console.log(map)
  arr.forEach((item) => {
    const parent = map[item.parentId];
    if (parent) {
      (parent.children || (parent.children = [])).push(item);
    } else {
      result.push(item);
    }
  });
  return result;
};

const a = toTree1(datas);
// console.log(a)

const DFS = (list) => {
  const res = [];
  const stack = [...list];

  while (stack.length > 0) {
    const item = stack.pop();
    res.push(item);
    if (item.children) {
      stack.push(...item.children);
      delete item.children;
    }
  }

  return res;
};
const BFS = (list) => {
  const res = [];
  const queue = [...list];

  while (queue.length > 0) {
    const item = queue.shift();
    res.push(item);
    if (item.children) {
      // console.log(item.children)
      // console.log(...item.children)
      queue.push(...item.children);
      delete item.children;
    }
  }

  return res;
};

const toList = (list) => {
  const res = [];
  list.forEach((item) => {
    res.push(item);
    if (item.children) {
      console.log(item.children);
      toList(item.children);
    }
  });
  return res;
};
const handleExpendDFS = (list) => {
  let result = [];
  function fn(list) {
    list.forEach((item) => {
      result.push(item);
      if (item.children) {
        fn(item.children);
        delete item.children;
      }
    });
    return result;
  }
  fn(list);

  return result.sort((a, b) => a.id - b.id);
};
const list = handleExpendDFS(a);
console.log(list);
```
