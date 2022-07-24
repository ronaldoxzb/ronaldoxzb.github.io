---
title: 面试题目
date: 2022-07-24 19:57:40
tags:
  - javascript  vue webpack
categories:
  - FrontEnd-Tec
---

### vue 面试

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

- 缓存组件
- 频繁切换，不需要重复渲染

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
