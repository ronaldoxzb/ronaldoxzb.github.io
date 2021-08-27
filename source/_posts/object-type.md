---
title: JS引用类型的用法
tags:
  - javascript 
categories:
  - FrontEnd-Tec
---
#### 操作数据
      const originData = ["1#12", "2#12", "3#13", "5#13", "4#13", "1#14"];
	  
```#```后面为projectId ，前面为modelId
#### 目标数据
    const targetData = [
      { projectId: "12", modelId: ["1", "2"] },
      { projectId: "13", modelId: ["3", "5", "4"] },
      { projectId: "14", modelId: ["1"] },
    ];
#### 具体实现
     function getList(list) {
      const temp = {}; // 定义的临时数据
      const arr = [];
      list.forEach((val) => {
        // 分割需要用到的数据
        const [mid, pid] = val.split("#");
        // 如果临时数据中存在当前项的projectId，则在temp[pid]值中追加modelId
        if (temp[pid]) {
          temp[pid].push(mid);
        } else {
        // 如果不存在临时数据中存储当前projectId对应modelId的值
          temp[pid] = [mid];
          arr.push({
            projectId: pid,
            modelId: temp[pid], // 这里取temp中projectId所对应的值「其实就是引用类型的用法」
          });
        }
      });
      return arr;
    }