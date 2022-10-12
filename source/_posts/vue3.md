---
title: vue3基础
date: 2022-09-26 15:12:40
tags:
  + javascript
  + vue3 
categories:
  + FrontEnd-Tec
---

* #### 模版语法

<img src="https://cn.vuejs.org/assets/directive.69c37117.png">

* #### 响应式基础

 `reactive()`

```javascript
import {
    reactive
} from 'vue'

const state = reactive({
    count: 0
})

const state = reactive({
    count: 0
})

// n 是一个局部变量，同 state.count
// 失去响应性连接
let n = state.count
// 不影响原始的 state
n++

// count 也和 state.count 失去了响应性连接
let {
    count
} = state
// 不会影响原始的 state
count++

// 该函数接收一个普通数字，并且
// 将无法跟踪 state.count 的变化
callSomeFunction(state.count)
```

*局限性*
1. 仅对对象类型有效（对象、数组和 Map、Set 这样的集合类型），而对 string、number 和 boolean 这样的 原始类型 无效。

2. 因为 Vue 的响应式系统是通过属性访问进行追踪的，因此我们必须始终保持对该响应式对象的相同引用。这意味着我们不可以随意地“替换”一个响应式对象，因为这将导致对初始引用的响应性连接丢失：
`ref()` *ref() 将传入参数的值包装为一个带 .value 属性的 ref 对象：*

```javascript
import {
    ref
} from 'vue'
const count = ref(0)
const count = ref(0)

console.log(count) // { value: 0 }
console.log(count.value) // 0

count.value++
console.log(count.value) // 1

// ref 被传递给函数或是从一般对象上被解构时，不会丢失响应性
const obj = {
    foo: ref(1),
    bar: ref(2)
}

// 该函数接收一个 ref
// 需要通过 .value 取值
// 但它会保持响应性
callSomeFunction(obj.foo)

// 仍然是响应式的
const {
    foo,
    bar
} = obj
```

 `ref解包`

1. ref 在模板中的解包

```javascript
< script setup >
    import {
        ref
    } from 'vue'

const count = ref(0)

function increment() {
    count.value++
} < /script>

<
template >
    <
    button @click = "increment" > {
        {
            count
        }
    } < !--无需.value-- >
    <
    /button> < /
template >

    // 请注意，仅当 ref 是模板渲染上下文的顶层属性时才适用自动“解包”。 例如， foo 是顶层属性，但 object.foo 不是。

    // 所以我们给出以下 object：

    const object = {
        foo: ref(1)
    }
// 下面的表达式将不会像预期的那样工作：F

{
    {
        object.foo + 1
    }
}
// 渲染的结果会是一个 [object Object]，因为 object.foo 是一个 ref 对象。我们可以通过将 foo 改成顶层属性来解决这个问题：

const {
    foo
} = object {
    {
        foo + 1
    }
}
```

2. ref 在响应式对象中的解包

```javascript
const count = ref(0)
const state = reactive({
    count
})

console.log(state.count) // 0

state.count = 1
console.log(count.value) // 1
```

3. 数组和集合类型的 ref 解包

```javascript
const books = reactive([ref('Vue 3 Guide')])
// 这里需要 .value
console.log(books[0].value)

const map = reactive(new Map([
    ['count', ref(0)]
]))
// 这里需要 .value
console.log(map.get('count').value)
```

* #### 计算属性

```javascript
< script setup >
    import {
        reactive,
        computed
    } from 'vue'

const author = reactive({
    name: 'John Doe',
    books: [
        'Vue 2 - Advanced Guide',
        'Vue 3 - Basic Guide',
        'Vue 4 - The Mystery'
    ]
})
// 一个计算属性 ref
const publishedBooksMessage = computed(() => {
        return author.books.length > 0 ? 'Yes' : 'No'
    }) <
    /script>

    <
    template >
    <
    p > Has published books: < /p> <
span > {
        {
            publishedBooksMessage
        }
    } < /span> < /
    template >
```

**若我们将同样的函数定义为一个方法而不是计算属性，两种方式在结果上确实是完全相同的，然而，不同之处在于计算属性值会基于其响应式依赖被缓存。 `一个计算属性仅会在其响应式依赖更新时才重新计算` 。这意味着只要 author.books 不改变，无论多少次访问 publishedBooksMessage 都会立即返回先前的计算结果，而不用重复执行 getter 函数。**

* #### 类与样式绑定
1. 绑定 HTML class

```javascript
const isActive = ref(true)
const hasError = ref(false)

    // 配合以下模版

    <
    div
class = "static": class = "{ active: isActive, 'text-danger': hasError }" >
    <
    /div>

    // 渲染后
    <
    div class = "static active" > < /div>

    // 绑定数组
    <
    div: class = "[activeClass, errorClass]" > < /div>
```

 `对于只有一个根元素的组件，当你使用了 class attribute 时，这些 class 会被添加到根元素上，并与该元素上已有的 class 合并。`

2. 绑定内联样式

<div :style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>

* 侦听器
*计算属性允许我们声明性地计算衍生值。然而在有些情况下，我们需要在状态变化时执行一些“副作用”：例如更改 DOM，或是根据异步操作的结果去修改另一处的状态。*
1. watch

```javascript
const x = ref(0)
const y = ref(0)

// 单个 ref
watch(x, (newX) => {
    console.log(`x is ${newX}`)
})

// getter 函数
watch(
    () => x.value + y.value,
    (sum) => {
        console.log(`sum of x + y is: ${sum}`)
    }
)

// 多个来源组成的数组
watch([x, () => y.value], ([newX, newY]) => {
    console.log(`x is ${newX} and y is ${newY}`)
})
```

 `注意，你不能直接侦听响应式对象的属性值，例如:`

```javascript
const obj = reactive({
    count: 0
})

// 错误，因为 watch() 得到的参数是一个 number
watch(obj.count, (count) => {
    console.log(`count is: ${count}`)
})
// 这里需要用一个返回该属性的 getter 函数：
// 提供一个 getter 函数
watch(
    () => obj.count,
    (count) => {
        console.log(`count is: ${count}`)
    }
)
```

2. watchEffect
*watch() 是懒执行的：仅当数据源变化时，才会执行回调。但在某些场景中，我们希望在创建侦听器时，立即执行一遍回调。举例来说，我们想请求一些初始数据，然后在相关状态更改时重新请求数据。我们可以这样写*

```javascript
const url = ref('https://...')
const data = ref(null)

async function fetchData() {
    const response = await fetch(url.value)
    data.value = await response.json()
}

// 立即获取
fetchData()
// ...再侦听 url 变化
watch(url, fetchData)

// 用watchEffct()改写
// 这个例子中，回调会立即执行。在执行期间，它会自动追踪 url.value 作为依赖（和计算属性的行为类似）。每当 url.value 变化时，回调会再次执行
watchEffect(async () => {
    const response = await fetch(url.value)
    data.value = await response.json()
})
```
- watch` 只追踪明确侦听的数据源。`它不会追踪任何在回调中访问到的东西。另外，仅在数据源确实改变时才会触发回调。- watch 会避免在发生副作用时追踪依赖，因此，我们能更加精确地控制回调函数的触发时机。

- watchEffect，`则会在副作用发生期间追踪依赖。`它会在同步执行过程中，自动追踪所有能访问到的响应式属性。这更方便，而且代码往往更简洁，但有时其响应性依赖关系会不那么明确。