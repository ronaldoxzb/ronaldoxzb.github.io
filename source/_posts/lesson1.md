---
title: 经典题目
date: 2022-06-27 08:57:40
tags:
  - javascript 
categories:
  - FrontEnd-Tec
---

### 变量类型

#### JavaScript 是一种弱类型脚本语言，所谓弱类型指的是定义变量时，不需要什么类型，在程序运行过程中会自动判断类型。
#### ECMAScript 中定义了 6 种原始类型：

- Boolean
- String
- Number
- Null
- Undefined
- Symbol（ES6 新定义）
注意：原始类型不包含 Object。
- 

#### 
#### 鉴别类型通用方法

```javascript
function type(target) { 
    const ret = typeof(target); 
    const template = { 
        "[object Array]": "array",  
        "[object Object]":"object", 
        "[object Number]":"number - object", 
        "[object Boolean]":"boolean - object", 
        "[object String]":'string-object' 
    } 
    if(target === null) { 
        return 'null'; 
    } 
    else if(ret == "object"){ 
        const str = Object.prototype.toString.call(target); 
        return template[str]; 
    } 
    else{ 
        return ret; 
    } 
} 
```
