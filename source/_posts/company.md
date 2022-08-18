---
title: 继续加油啊！
date: 2022-08-07 10:21:40
tags:
  - javascript es6 npm object
categories:
  - FrontEnd-Tec
---

### 城云题目

#### Object.keys(), for in

- Object.keys()：遍历对象的 key,返回一个数组，数组值为对象自有的属性，不会包括继承原型的属性
- for in :以任意顺序遍历一个对象的属性，包括自身属性，以及继承自原型的属性

```javascript
Object.prototype.aaa = "aaa";
let obj = {
  a: "1",
  b: "2",
};
Object.keys(obj).forEach((item) => {
  console.log(item); // a,b
});
for (const key in obj) {
  console.log(key); // a,b,aaa
}
for (const key in obj) {
  if (Object.hasOwnProperty.call(obj, key)) {
    console.log(key); // a,b
  }
}
```

#### Object.create()

#### npm 包依赖关系

_结论 1： 通过 npm 安装 express 包会将 express 的所有依赖都一起安装_

语义版本控制程序包`对于一个项目或包而言，在package.json的dependencies 中声明依赖版本有这样的规则`

- ~(波浪线）代表当前项目接受补丁版本，也就是如果你的项目依赖为版本为 1.0.1 的某个 npm 包（假设包名叫 express），当 express 更新到 1.0.3 的时候，你运行 npm install，会自动将 express 升级为 1.0.3。
- ^(尖号) 代表当前项目接受 次要版本 ，也就是如果你的项目依赖为版本为 1.0.1 的某个 npm 包（假设包名叫 express），当 express 更新到 1.3.2 的时候，你运行 npm install，会自动将 express 升级为 1.3.2。
- 如果版本号前没有加上修饰符修饰符，则表示指定该版本进行安装。

_结论 2：npm 安装的包会根据 package.json 中的 dependencies 依赖修饰符，自动升级依赖的版本。_

_结论 3：npm 会递归的安装一个包的依赖。_

_结论 4: npm 会记录安装过的包，安装过程为广度优先。_

#### 函数式组件

`组件没有管理任何状态,也没有监听任何传递给它的状态,也没有生命周期访问 优点就是 轻量,灵活`

```javascript
Vue.component("heading", {
  functional: true, //函数组件
  props: {
    level: {
      type: String,
      default: "1",
    },
    title: {
      type: String,
      default: "",
    },
    icon: {
      type: String,
    },
  },
  render(h, context) {
    // 子节点数组
    console.log("上下文:", context);
    let children = [];
    // 属性获取的变化
    const { icon, title, level } = context.props;

    // icon处理
    if (icon) {
      children.push(
        h("svg", { class: "icon" }, [
          h("use", {
            attrs: { "xlink:href": "#icon-" + icon },
          }),
        ])
      );
    }
    children = children.concat(context.children);
    const vnode = h("h" + level, { attrs: { title } }, children);
    console.log("查看", vnode);
    return vnode;
  },
});
```

#### cros 的原理

#### svg canvas 取舍

#### computed 第一次会执行么

### 阿里

#### babel 编译过程

- 解析（Parse）：包括词法分析和语法分析。词法分析主要把字符流源代码（Char Stream）转换成令牌流（ Token Stream），语法分析主要是将令牌流转换成抽象语法树（Abstract Syntax Tree，AST）。
- 转换（Transform）：通过 Babel 的插件能力，将高版本语法的 AST 转换成支持低版本语法的 AST。当然在此过程中也可以对 AST 的 Node 节点进行优化操作，比如添加、更新以及移除节点等。
- 生成（Generate）：将 AST 转换成字符串形式的低版本代码，同时也能创建 Source Map 映射。

#### nextTick 的原理

### 涂鸦

#### watch 原理

- watch 监听优化： 监听某个对象时，对象的任何属性改变都会触发变动， 这样比较耗性能， 如果明确知道只需监听某一属性，可以使用字符串的形式监听，如'element.data'
- watch 有一个特点，就是当值第一次绑定的时候，不会执行监听函数，只有值发生改变才会执行。如果我们需要在最初绑定值的时候也执行函数，可添加 immediate 属性
- 普通 watch 方法不能监听对象内部属性的变化，可以添加 deep 属性深度监听

`监听数据改变时， watch是如何工作的？`
Vue 会把数据设置响应式，即设置他的 get 和 set
当数据被读取，get 被触发，然后收集到读取他的东西，保存到依赖收集器
当数据被改变，set 被触发，然后通知曾经读取他的东西进行更新。

watch 在一开始初始化的时候，会 读取 一遍 监听的数据的值，于是，此时 那个数据就收集到 watch 的 watcher 了
然后 你给 watch 设置的 handler ，watch 会放入 watcher 的更新函数中
当 数据改变时，通知 watch 的 watcher 进行更新，于是 你设置的 handler 就被调用了.

`设置了immediate ， watch是如何工作的？`
设置了 immediate 时，就不需要在数据改变的时候才会触发。
而是在 初始化 watch 时，在读取了 监听的数据的值 之后，便立即调用一遍你设置的监听回调，然后传入刚读取的值.

`设置了deep ， watch是如何工作的？`
watch 有一个 deep 选项，是用来深度监听的,什么是深度监听呢？就是当你监听的属性的值是一个对象的时候，如果你没有设置深度监听，当对象内部变化时，你监听的回调是不会被触发的.

#### 前端如何做浏览器缓存

#### http 状态码

3XX(Redirection 重定向状态码)

- 301 永久性重定向
- 302 临时性重定向
- 303 该状态码表示由于请求对应的资源存在着另一个 URI，应使用 GET 方法定向获取请求的资源。

**当 301、302、303 响应状态码返回时，几乎所有的浏览器都会把 POST 改成 GET，并删除请求报文内的主体，之后请求会自动再次发送,301、302 标准是禁止将 POST 方法改变成 GET 方法的，但实际使用时大家都会这么做**

4XX(Client Error 客户端错误状态码) 表明客户端是发生错误的原因所在

- 401 Unauthorized 未授权
- 403 Forbidden 禁止访问
- 404 Not Found
- 405 Method Not Allowed

5XX(Server Error 服务器错误状态码)表明服务器本身发生错误

- 502 Bad Gateway
- 503 Service Unavailable

#### 浏览器请求 options

#### restful 接口

#### diff 算法如何比对
