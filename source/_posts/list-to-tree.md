---
title: 一维List转树形结构
date: 2022-05-05 17:24:32
tags:
  - javascript
  - tree
  - object-reference
  - list
categories:
  - FrontEnd-Tec
---

### 一维 list 生成树行结构

#### 原始数据

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

```

#### 递归形式转换

```javascript
const toTree = (arr = [], parentId) => {
    let array = []
    arr.forEach(item => {
        if (item.parentId === parentId) {
            item.children = toTree(arr, item.id)
            array.push(item)
        }
    });
    return array
}
```

#### 对象引用遍历实现

```javascript
/**
 * 对象引用遍历实现
 * @param {*} list
 * @returns
 */
const toTree = (list) => {
  const result = [];

  const map = {};
  list.forEach((item) => {
    map[item.id] = item;
  });
  list.forEach((item) => {
    const parent = map[item.parentId];
    if (parent) {
      (parent.children || (parent.children = [])).push(item);
    } else {
      result.push(item);
    }
  });

  return result;
};
```
