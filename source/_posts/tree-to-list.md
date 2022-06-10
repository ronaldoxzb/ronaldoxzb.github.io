---
title: 树型结构转一维List
date: 2022-05-12 10:34:32
tags:
  - javascript
  - tree
  - dfs
  - bfs
categories:
  - FrontEnd-Tec
---

### 树形结构转一维 list

#### 原始数据

``` javascript
const data = [
  {
    name: "a",
    children: [
      {
        name: "a1",
        children: [
          {
            name: "a11",
          },
          {
            name: "a12",
          },
        ],
      },
      {
        name: "a2",
        children: [
          {
            name: "a21",
          },
          {
            name: "a22",
          },
        ],
      },
      {
        name: "a3",
        children: [
          {
            name: "a31",
          },
          {
            name: "a32",
          },
        ],
      },
    ],
  },
  {
    name: "b",
    children: [
      {
        name: "b1",
        children: [
          {
            name: "b11",
          },
          {
            name: "b12",
          },
        ],
      },
      {
        name: "b2",
        children: [
          {
            name: "b21",
          },
          {
            name: "b22",
          },
        ],
      },
      {
        name: "b3",
        children: [
          {
            name: "b31",
          },
          {
            name: "b32",
          },
        ],
      },
    ],
  },
];

```

#### 深度优先拍平数据`递归`

```javascript
/**
 * 深度优先拍平数据
 * @param {*} list
 * @returns
 */
const handleExpendDFS = (list) => {
  let result = [];
  function fn(list) {
    list.forEach((item) => {
      result.push(item);
      if (item.children) {
        fn(item.children);
      }
    });
  }
  fn(list);

  return result;
};

```

#### 深度优先拍平数据（非递归）`利用stack先进后出的特性`

```javascript
/**
 * 深度优先拍平数据
 * @param {*} list
 * @returns
 */
const handleExpendDFS1 = (list) => {
  const result = [];
  let satck = [...list];
  while (Array.isArray(satck) && satck.length > 0) {
    const item = satck.pop();
    result.push(item);
    if (item.children) {
      satck.push(...item.children)
      // for (let i = 0; i < item.children.length; i++) {
      //   const ele = item.children[i];
      //   satck.push(ele);
      // }
    }
  }

  return result;
};
```

#### 广度优先拍平数据 `利用queue先进先出的特性`

```javascript
/**
 * 广度优先拍平数据
 * @param {*} list
 * @returns
 */
const handleExpendBFS = (list) => {
  let result = [];
  let queue = [...list];
  while (queue.length > 0) {
    const item = queue.shift();
    result.push(item);
    if (item.children) {
      queue.push(...item.children)
      // item.children.forEach(k=> {
      //   queue.push(k)
      // })
    }
  }
  return result;
};

```

### 如何在树形结构中查找指定数据 ？

#### 深度优先搜索算法`递归`

```javascript
/**
 * 递归实现深度优先搜索算法
 * @param {*} list
 * @param {*} name
 * @returns
 */
const handleDFS = (list, name) => {
  let hasFound = false,
    target;
  function fn(list, name) {
    if (Array.isArray(list) && !hasFound) {
      list.forEach((i) => {
        if (i.name === name) {
          hasFound = true;
          target = i;
        } else if (i.children) {
          fn(i.children, name);
        }
      });
    }
  }
  fn(list, name);
  return target;
};

```

#### 广度优先遍历实现搜索算法 `利用queue先进先出的特性`

```javascript
/**
 * 广度优先遍历实现搜索算法
 * @param {*} list
 * @param {*} name
 * @returns
 */
const handleBFS = (list, name) => {
  let result;
  let queue = list;
  if (Array.isArray(queue)) {
    while (queue.length > 0) {
      const item = queue.shift();
      if (item.name === name) {
        result = item;
        break;
      } else if (item.children) {
        item.children.forEach((i) => {
          queue.push(i);
        });
      }
    }
  }

  return result;
};

```
