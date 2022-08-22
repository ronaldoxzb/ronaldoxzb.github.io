---
title: 收藏题目
date: 2022-07-24 19:57:40
tags:
  - javascript
  - vue
  - webpack
categories:
  - FrontEnd-Tec
---

### vue 基础

#### v-if v-show

`v-if重新渲染 v-show 控制css`

#### v-for key 的作用

`diff算法会用到key来比较新旧节点，提升页面渲染性能`

#### v-model 实现原理

- 绑定 value=this.name
- 绑定 input 事件，this.name = event.target.value
- data 更新触发视图的更新

#### mvvm 的理解

- view viewmodel model

#### 为什么 data 必须是一个函数

`隔离属性定义，避免同名变量的引用`

#### ajax 放在哪个生命周期中

` mounted中，js是单线程，ajax异步获取数据，放在mounted之前只会让逻辑更加混乱`

#### 单页面生命周期

`beforeCreated,created,beforeMount,Mounted,beforeUpdate,Update`

#### 异步组件

- import()函数
- 异步加载

#### keep-alive

`是 Vue.js 的一个 内置组件。它能够将不活动的组件实例保存在内存中，而不是直接将其销毁，它是一个抽象组件，不会被渲染到真实 DOM 中，也不会出现在父组件链中`

- 缓存组件
- 频繁切换，不需要重复渲染

#### keep-alive 原理

- 使用 LRU 缓存机制进行缓存，max 限制缓存表的最大容量
- 根据设定的 include/exclude（如果有）进行条件匹配,决定是否缓存。不匹配,直接返回组件实例
- 根据组件 ID 和 tag 生成缓存 Key ,并在缓存对象中查找是否已缓存过该组件实例。如果存在,直接取出缓存值- 并更新该 key 在 this.keys 中的位置(更新 key 的位置是实现 LRU 置换策略的关键)
- 获取节点名称，或者根据节点 cid 等信息拼出当前 组件名称
- 获取 keep-alive 包裹着的第一个子组件对象及其组件名
  `​ Vue 内部将 DOM 节点抽象成了一个个的 VNode 节点，keep-alive 组件的缓存也是基于 VNode 节点的而不是直接存储 DOM 结构。它将满足条件（ include 与 exclude ）的组件在 cache 对象中缓存起来，在需要重新渲染的时候再将 vnode 节点从 cache 对象中取出并渲染。`

##### LRU 缓存淘汰算法*LRU（ least recently used）根据数据的历史记录来淘汰数据，重点在于保护最近被访问/使用过的数据，淘汰现阶段最久未被访问的数据*

- 新数据插入到链表尾部；
- 每当缓存命中（即缓存数据被访问），则将数据移到链表尾部
- 当链表满的时候，将链表头部的数据丢弃。

### http1.0 https1.1 http2.0 的区别

- HTTP 1.1 默认使用长连接，可有效减少 TCP 的三次握手开销。
- HTTP 1.0 规定浏览器与服务器只保持短暂的连接，浏览器的每次请求都需要与服务器建立一个 TCP 连接，服务器完成请求处理后立即断开 TCP 连接。
- HTTP 1.1 还允许客户端不用等待上一次请求结果返回，就可以发出下一次请求。
- HTTP2.0 使用多路复用技术，"HTTP1.1 在同一时间对于同一个域名的请求数量有限制，超过限制就会阻塞请求"。多路复用底层采用增加二进制分帧层的方法，使得不改变原来的语义、首部字段的情况下提高传输性能，降低延迟。

#### 浏览器缓存策略

- 浏览器每次发起请求，都会先在浏览器缓存中查找该请求的结果以及缓存标识
- 浏览器每次拿到返回的请求结果都会将该结果和缓存标识存入浏览器缓存中

##### 强缓存

强缓存不会向服务器发送请求，直接从缓存中读取资源，在 chorme 控制台 Network 选项中可以看到该请求返回 200 的状态码，并且 Size 显示 from disk cache 或 from memory cache。强缓存可以通过设置两种 HTTP Header 实现：Expries 和 Cache-Control

- Expires
  缓存过期时间，用来指定资源到期的时间，是服务器端的具体时间点。Expires = max-age + 请求时间，需要和 -Last-modified 结合使用。Expires 是 Web 服务器响应消息头字段，在响应 http 请求时告诉浏览器在过期时间前浏览器可以直接从缓存中取数据，无需再次请求。
  Tips：Expires 受限于本地时间，如果修改了本地时间，可能会造成缓存失效。
- Cache-Control
  Cache-Control 可以在请求或者响应头中设置，并且可以组合多种指令：

##### 协商缓存

- 协商缓存生效，返回 304 和 Not Modified
  首先，浏览器发起 http 请求后，浏览器缓存结果失效，只返回了缓存标识；然后，客户端携带该缓存标识向服务器发起 http 请求；接着，服务器发现请求的资源没有更新，返回 304；最后，客户端从缓存中获取结果
- 协商缓存失效，返回 200 和 请求结果
  首先，浏览器发起 http 请求后，浏览器缓存结果失效，只返回了缓存标识；然后，客户端写到该缓存标识向服务器发起 http 请求；接着，服务器发现请求的资源更新了，返回 200 和 请求结果；最后，将该请求的请求结果和缓存标识存入浏览器缓存中
  协商缓存可以通过设置两种 HTTP Header 实现：Last-Modified 和 ETag：
- Last-Modified 和 If-Modified-Since
  浏览器在第一次访问资源时，服务器返回资源的同时，在 response header 中添加 Last-Modified 的 header，值是这个资源在服务器上的最后修改时间，浏览器接收后缓存文件和 header；
  当浏览器下次请求这个资源，浏览器检测到有 Last-Modified 这个 header，于是添加 If-Modified-Since 这个 header，值就是 Last-Modified 中的值；服务器再次收到这个资源请求，会根据 If-Modified-Since 中的值与服务器中这个资源的最后修改时间对比，如果没有变化，返回 304 和 空的响应体，直接从缓存中读取，如果 If-Modified-Since 的时间小于服务器中这个资源的最后修改时间，说明文件有更新，于是返回新的资源文件和 200
  但是 Last-Modified 存在一些问题：
  _如果本地打开缓存文件，即使没有对文件进行修改，但还是会造成 Last-Modified 被修改，服务端不能命中缓存导致发送相同的资源 _ 因为 Last-Modified 只能以秒计时，如果再不可感知的时间内完成修改文件，那么服务端会认为资源还是命中了，不会返回正确的资源\*
- ETag 和 If-None-Match
  ETag 是服务器响应请求时返回当前资源的一个唯一标识（由服务器生成），只要资源有变化，ETag 就会重新生成。
  浏览器在下一次加载资源向服务器发送请求时，会将上一次返回的 ETag 值放到 request header 里的 If-None-Match 里，服务器只需要比较客户端传来的 If-None-Match 跟自己服务器上该资源的 ETag 是否一致，就能知道资源相对于客户端是否被修改过。如果发现匹配不上，那么直接常规的 200 形式将新的资源发给客户端；如果 ETag 是一致的，则直接返回 304 只会客户端直接使用缓存即可

  `在精确度上， ETag 要优于 Last-Modified 在性能上，Last-Modified 要优于 ETag 在优先级上，服务器校验优先考虑 ETag`

##### 缓存位置

- Service Worker
- Memory Cache
- Disk Cache
- Push Cache

##### 缓存机制

**强制缓存优先于协商缓存进行，若强制缓存生效则直接使用缓存，若不生效则进行协商缓存，协商缓存由服务器决定是否使用缓存，若协商缓存失效，那么代表该请求的缓存失效，返回 200，重新返回资源和缓存标识，再存入浏览器缓存中；生效则返回 304，继续使用缓存**

#### 如何使用 beforeDestory

- 解除自定义事件 event.$off
- 清除定时器
- 解除自定义 dom 事件。window.addEventListener,window.scroll 等

#### 作用域插槽

`可以在父组件中获取插槽组件中的值`

#### vuex 中 action 和 mutation

- action 中可以处理异步 mutation 中不可以
- mutation 做一个操作
- action 可以整合多个 mutation

#### vue 页面渲染过程

- 解析模版为 render 函数
- 触发响应式，监听 data，get, set 属性
- 执行 render 函数，生成 vnode，通过 patch 函数生成页面

`这也是mvvm的体现，也是数据驱动视图的核心思想`

#### diff 算法

- vnode patch（ele,vnode）patch(vnode, newVode)
- patchVode addVode removeVodes
- updateChildren(key 的重要性)

#### vue 为何是异步渲染

- vue 合并 data 修改，最后一次性进行渲染，提高性能
- $nextTick()会在 dom 更新完之后触发回调

#### vue 常见性能优化

- 合理使用 v-if v-show
- 合理使用 computed 应为它会缓存（数据不变不会触发更新）
- v-for 加 key v-for v-if 不要同时使用 v-for 优先级更改，每次 v-for v-if 会重新计，浪费性能算
- 自定义事件、dom 事件要及时销毁

#### vue 高阶组件中的通信

- $attrs与$listeners

- $children与$parent 属性

#### vue 是怎么初始化的

- 1、选项合并，处理组件的配置内容，将传入的 options 与构造函数本身的 options 进行合并(用户选项和系统默认的选项进行合并)
- 2、初始化 vue 实例生命周期相关的属性，定义了比如：root、root、root、parent、children、children、children、refs
- 3、初始化自定义组件事件的监听,若存在父监听事件,则添加到该实例上
- 4、初始化 render 渲染所需的 slots、渲染函数等。其实就两件事：插槽的处理 和 $createElm 的声明，也就是 render 函数中的 h 的声明
- 5、调用 beforeCreate 钩子函数，在这里就能看出一个组件在创建前和后分别做了哪些初始化
- 6、初始化注入数据，隔代传参时 先 inject。作为一个组件，在要给后辈组件提供数据之前，需要先把祖辈传下来的数据注入进来
- 7、对 props,methods,data,computed,watch 进行初始化，包括响应式的处理
- 8、在把祖辈传下来的数据注入进来以后 再初始化 provide
- 9、调用 created 钩子函数，初始化完成，可以执行挂载了
- 10、挂载到对应 DOM 元素上。如果组件构造函数设置了 el 选项，会自动挂载，所以就不用再手动调用$mount 去挂载

### vue router

- hash 模式

`window.onHashChange `

- history

`history.pushState, window.onpopState `

### webpack

`webpack4 => wbpack5 `

<img width="600" align="center" src="/images/webpack.png" alt="ronaldoxzb" /></p>

#### 基本配置

- 拆分配置和 merge common dev test production 借助 const { smart } = require('webpack-merge')
- 启动本地服务 devServer
- 处理 es6 babel-loader `.babelrc`文件
- 处理样式 loader: ['style-loader', 'css-loader', 'postcss-loader', 'less-loader'] postcss-loader 处理各浏览器样式兼容性的问题 `postcss.config.js`文件
- 处理图片 1 file-loader 直接引入 2 通过 url-loader 转换 base64 html 直接插入图片避免多一次请求
- output filename => contentHash:8 根据文件生成 8 位 hash 文件不变直接从浏览器缓存获取不再发起请求

`loader的执行顺序：从后往前`

#### 高级配置

- 多入口打包

```javascript index.html other.html为例子
1: enter :{ index : path.join(srcPath, 'index.js') , other:path.join(srcPath, 'other.js')}
2: output : { filename : '[name].[contentHash:8].js'}
3: 引入htmlWebpackPlugin({ template : path.join(srcPath, inde.html),
filename: 'index.html',
// chunks表示该页面引用哪些chunk ,只引入index.js
chunks: ['index']})
```

- css 抽离 压缩

```javascript
loader: [MiniCssExtractPlugin.loader, "css-loader", "postcss-loader"];
plugin: [
  new MiniCssExtractPlugin({ filename: "css/ mian.[contenthash:8].css" }),
];

optimization: {
  // 压缩
  minimizer: [new terserJSPlugin({}, new OptimizeCSSAssetsPlugin({}))];
}
```

- 抽离公共代码和第三方模块

```javascript
   optimization: {
    // 压缩css
    minimizer: [new terserJSPlugin({}, new OptimizeCSSAssetsPlugin({}))]
    // 分割代码块
    splitChunks: {
        /**
         *  initial 入口chunk，对于异步导入的文件不处理
         *  async 异步 chunk， 只对异步导入的问价做处理
         *  all 全部 chunk
         */
        chunks: 'all'
        cacheGroups: {
            // 第三方模块
            vendor: {
                name: 'vendor', // chunk名称
                priority: 1, // 权限更高，优先抽离
                test: /node_modules/,
                miniSize: 0, // 大小限制
                miniChunks: 1 //最少复用过几次
            },
            // 公共模块
            common: {
                name: 'common', // chunk名称
                priority: 1, // 权限更高，优先抽离
                miniSize: 0, // 大小限制（太小代码不用抽离，避免多次请求）
                miniChunks: 2 //最少复用过几次
            }
        }
    }
   }
```

- 异步加载 js

```javascript
// 定义chunk
import(".dynamic-data.js").then((res) => {
  console.log(res);
});
```

- 处理 jsx

```javascript
`.babelrc 文件中配置`
{
    "presets" : ["@babel/preset-env"],
    "plugin" : []
}
```

- 处理 vue 安装 vue-loader 即可

#### module chunk bundle 的区别

- module- 各个源码文件，可以引入的，webpack 中一切皆模块
- chunk- 多模块的合成， 如 entry import() splitChunk
- bundle- 最终输出的文件

#### loader plugin 的区别

- loader 模块转换器 如 less => css
- plugin 拓展插件 newHtmlWebpackPlugin

#### webpack 性能优化

##### `优化构建速度`

- 优化 babel-loader

```javascript
    {
        tets:/\.js$/,
        use:['babek-loader?cacheDirectory'],// 开启缓存
        include: path.resolve(_dirname, 'src') // 明确范围
    }
```

- IgnorePlugin 避免引入无用模块 moment.js 为例子（不引入语言包） plugin 中开启，手动引入中文包 `直接不引入`

- noParse 已经打包过的库避免重复打包 vue.js 等 `引入但不打包`

- happyPack 多进程打包

```javascript
rules: [
  {
    tets: /\.js$/,
    use: ["happypack/loader?id=babel"], // 文件处理交给id为babel的happypack实例
    include: path.resolve(_dirname, "src"), // 明确范围
  },
];
plugin: [
  new HappyPack({
    id: "babel",
    loader: ["babek-loader?cacheDirectory"],
  }),
];
```

- ParallelUglifyPlugin 多进程压缩 js 和 happypack 同理

```javascript
plugin: [
  new ParallelUglifyPlugin({
    uglifyJS: {
      output: {
        beautify: false, // 最紧凑输出
        comments: false, // 删除所有的注释
      },
      compress: {
        drop_console: true,
        collapse_vars: true, // 内嵌定义了但是只用到一次的变量
        reduce_vars: true, // 提取出出现多次但是没有定义成变量去引用的静态之
      },
    },
  }),
];
```

`关于开启多进程看情况`

- 自动刷新：整个页面全部刷新，速度慢，状态丢失。 热更新则不会

```javascript
module.export = {
  watch: true, //自动刷新
};
```

- DLLPlugin 动态连结库插件

##### `产出代码`

- 小图片 base64 格式产出
- bundle 加 hash 文件不变化 hash 不变，读缓存就可
- 懒加载
- 提取公共代码（第三方或公共代码）
- IgnorePlugin 见上一部分
- 使用 cdn 加速
- 使用 production
  - 自动开启压缩代码（体积小，加载快）
  - vue react 等会自动删除调试代码（开发环境 waring 等）
  - 自动启动 tree-sharking（ES6 module 才能让它生效， Commonjs 不生效）
  - `ES6 module静态引入，编译时引用`
  - `Commonjs 动态引入，执行时引入`
- Scope Hosting 引入插件 plugins 中配置,可 google

### babel

#### runtime

- 避免污染全局环境使用 babel-runtime

#### polyfill（补丁兼容）

- babel-polyfill(下面两个集合，7.4 已被弃用)
- core.js
- regenerator
- babel-polyfill 按需引入`.babelrc 文件中配置`
- 会污染全局变量 window.Promise() Array.prototype.includes = function (){}
