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

*结论 1： 通过 npm 安装 express 包会将 express 的所有依赖都一起安装*

语义版本控制程序包`对于一个项目或包而言，在package.json的dependencies 中声明依赖版本有这样的规则`

- ~(波浪线）代表当前项目接受补丁版本，也就是如果你的项目依赖为版本为 1.0.1 的某个 npm 包（假设包名叫 express），当 express 更新到 1.0.3 的时候，你运行 npm install，会自动将 express 升级为 1.0.3。
- ^(尖号) 代表当前项目接受 次要版本 ，也就是如果你的项目依赖为版本为 1.0.1 的某个 npm 包（假设包名叫 express），当 express 更新到 1.3.2 的时候，你运行 npm install，会自动将 express 升级为 1.3.2。
- 如果版本号前没有加上修饰符修饰符，则表示指定该版本进行安装。

*结论2：npm 安装的包会根据package.json中的dependencies依赖修饰符，自动升级依赖的版本。*

*结论3：npm会递归的安装一个包的依赖。*

*结论4: npm会记录安装过的包，安装过程为广度优先。*

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

#### babel编译过程

- 解析（Parse）：包括词法分析和语法分析。词法分析主要把字符流源代码（Char Stream）转换成令牌流（ Token Stream），语法分析主要是将令牌流转换成抽象语法树（Abstract Syntax Tree，AST）。
- 转换（Transform）：通过 Babel 的插件能力，将高版本语法的 AST 转换成支持低版本语法的 AST。当然在此过程中也可以对 AST 的 Node 节点进行优化操作，比如添加、更新以及移除节点等。
- 生成（Generate）：将 AST 转换成字符串形式的低版本代码，同时也能创建 Source Map 映射。
#### nextTick的原理


