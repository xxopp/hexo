---
title: Element ui表格树形控件结合复选框实现
tags:
  - Vue
  - Element
index_img: 'https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2022/20201119ee.jpg'
categories: Web前端
abbrlink: 2a248f00
date: 2020-11-19 17:11:57
---

Vue组件库用的最多就是Element ui以及蚂蚁金服的Ant design vue了，目前项目使用的正好是Element ui，使用过程中发现一些问题也都能在网上搜索到解决办法。项目某一处功能需要实现表格树结构，翻遍了文档发现居然不支持树形控件，尝试安卓一些第三方组件都不是很理想，找到了一篇不错的解决办法，稍加整理！

## template

使用`template`可以自定义我们想要的内容，我们可以通过对应数据的checked绑定每一行的checkbox,使用`slot="header"`可以自定义表头，因此最终我们可以写出以下代码

```bash
<el-table-column align="center">
    <template slot="header">
        <el-checkbox
        	v-model="checkedAll"
            @change="handleCheckedAll"/>
    </template>
    <template slot-scope="scope">
        <el-checkbox
        	v-model="scope.row.checked"
        	@change="handleCheckedOne(scope.row)"/>
    </template>
</el-table-column>
```

## data

每一行待渲染的数据都有其对应的`checked`,可以通过这个key绑定对应的checkbox，有一些数据有`children`,其为数组形式,这就是待渲染的子树，数组内的对象同样有`checked`属性，也同样可以有`children`属性（非必须）

```bash
tableData: [{
    id: 1,
    date: '2016-05-02',
    name: '王小虎',
    checked: false,
    address: '上海市普陀区金沙江路 1518 弄'
}, {
    id: 2,
    date: '2016-05-04',
    name: '王小虎',
    checked: false,
    address: '上海市普陀区金沙江路 1517 弄'
}, {
    id: 3,
    date: '2016-05-01',
    name: '王小虎',
    checked: false,
    address: '上海市普陀区金沙江路 1519 弄',
    children: [{
        id: 31,
        date: '2016-05-01',
        name: '王小虎',
        checked: false,
        address: '上海市普陀区金沙江路 1519 弄'
    }, {
        id: 32,
        date: '2016-05-01',
        name: '王小虎',
        checked: false,
        address: '上海市普陀区金沙江路 1519 弄',
        children: [{
            id: 61,
            date: '2016-05-01',
            name: '王小虎',
            checked: false,
            address: '上海市普陀区金沙江路 1519 弄'
        }, {
            id: 62,
            date: '2016-05-01',
            name: '王小虎',
            checked: false,
            address: '广州市普陀区金沙江路 1519 弄'
        }]
    }]
}, {
    id: 4,
    date: '2016-05-03',
    name: '王小虎',
    checked: false,
    address: '上海市普陀区金沙江路 1516 弄'
}]
```

## methods

`checkedAll`用于与表头绑定，`handleCheckedAll`和`handleCheckedOne(scope.row)`足够我们完成后续的功能，但你会发现一个问题，表头的`checkbox`无法被勾选，控制台也不会报错，解决方法是在`slot="header"`后添加`slot-scope="scope"`,同时将scope传入`handleCheckedAll`,即使传入的scope并不需要用到。

当我们勾选父级checkbox时，对应的子级checkbox也执行了相同的逻辑，子级可以还会有自己的子级，在其下面可能还存在着子级。这时，你很容易想到递归，事实上递归在解决树类问题确实实用性很高。

```bash
// boolValue接收一个布尔值
checkOne(obj, boolValue) {
    obj.checked = boolValue
    // 判断存不存在children,存在就递归赋值
    if (obj.children) {
        obj.children.forEach(item => this.checkOne(item, boolValue))
    }
},

// 绑定每一行checkbox的change事件
handleCheckedOne(row) {
    if (row.checked) {
        this.checkOne(row, true)
    } else {
        this.checkOne(row, false)
    }
}
```
当我们要点击表头的checkbox来改变所有的行的checkbox的时候，我们要改变的就是数组了。

遍历每一行执行`checkOne()`也是一种实现`checkAll()`的做法，但你还得处理最外层数据的checkbox，个人认为这样反而使得代码逻辑不清晰，泛用性差。

```pash
checkAll(arr, boolValue) {
    arr.forEach(item => {
        item.checked = boolValue
        // children同样是数组
        if (item.children) {
            this.checkAll(item.children, boolValue)
        }
    })
},

handleCheckedAll(scope) {
    // 实际上scope并没有什么用处，仅为了解决上面提到的表头checkbox不显示的bug
    // console.log(scope.column)
    
    if (this.checkedAll) {
        this.checkAll(this.tableData, true)
    } else {
        this.checkAll(this.tableData, false)
    }
}
```

<p class="note note-primary">参考资料：https://juejin.im/post/6844904197784076296</p>