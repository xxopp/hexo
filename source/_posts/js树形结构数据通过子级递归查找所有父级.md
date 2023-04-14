---
title: js树形数据递归查询该节点的所有父级节点
tags:
  - JavaScript
  - 代码
index_img: >-
  https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2022/desert-7008952_640.jpg
categories: Web前端
abbrlink: f1e77e61
date: 2023-01-08 17:26:56
---

需求：通过传入当前节点的ID，查询出所有的父级节点

调用时，传入数据和当前节点ID(数据为树型结构)

### 方法一

```js
arr：any = [
    {
        id: 1,
        label: '一级 1',
        children: [{
            id: 4,
            label: '二级 1-1',
            children: [{
                id: 9,
                label: '三级 1-1-1'
            }, {
                id: 10,
                label: '三级 1-1-2'
            }]
        }]
    }, 
    {
        id: 2,
        label: '一级 2',
        children: [{
            id: 5,
            label: '二级 2-1'
        }, {
            id: 6,
            label: '二级 2-2'
        }]
    }, 
    {
        id: 3,
        label: '一级 3',
        children: [{
            id: 7,
            label: '二级 3-1'
        }, {
            id: 8,
            label: '二级 3-2'
        }]
    }];
    
path: any = [] // 查找的父节点
findState: Boolean = false // 如果找到了状态为true

// 调用方法(总数据，子节点id)
this.findFather(this.arr, 8)

findFather(data,value) {
    data.forEach((item,index) => {
        //如果找到了直接返回
        if (this.findState) return
        //在遍历过程中保存路径
        this.path.push(item.value)
        //如果找到了改变状态
        if (item.value === this.data.areaCode) {
            this.findState = true
        } else if (item.children.length > 0) {
            //还未找到并且有子集重新调用方法
            this.findFather(item.children,value)
        } else {
            //清除缓存
            this.path.pop()
        }
    })
    if (!this.findState) this.path.pop()
}
```

### 方法二

```js
// https://segmentfault.com/q/1010000020095550
var arr = [{
    code: 1,
    name: "湖北省",
    children: [{
        code: 1,
        name: "武汉市",
        children: [{
            code: 1,
            name: "汉阳区",
            children: [{
                code: 1,
                name: "水上分局1"
            }]
        }, {
            code: 1,
            name: "武昌区",
            children: [{
                code: 1,
                name: "水上分局2"
            }]
        }, {
            code: 1,
            name: "汉口区",
            children: [{
                code: 1,
                name: "水上分局3"
            }]
        }]
    }, {
        code: 1,
        name: "十堰市",
        children: [{
            code: 1,
            name: "郧阳区",
            children: [{
                code: 1,
                name: "安阳镇"
            }]
        }, {
            code: 1,
            name: "茅箭区",
            children: [{
                code: 1,
                name: "小川乡"
            }]
        }]
    }]
}]


function treeFindPath (tree, func, path = []) {
  if (!tree) return []
  for (const data of tree) {
    // 这里按照你的需求来存放最后返回的内容吧
    path.push(data.name)
    if (func(data)) return path
    if (data.children) {
      const findChildren = treeFindPath(data.children, func, path)
      if (findChildren.length) return findChildren
    }
    path.pop()
  }
  return []
}

treeFindPath(res, data => data.name === '水上分局')
// ["湖北省","武汉市","汉阳区","水上分局"]

```

