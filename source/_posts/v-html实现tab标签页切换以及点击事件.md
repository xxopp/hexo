---
title: v-html实现tab标签页切换以及点击事件
tags:
  - Vue
  - JavaScript
index_img: 'https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2022/20201117a.png'
categories: Web前端
abbrlink: b6b71b2e
date: 2020-11-19 11:52:23
---

最近工作项目用到了Mapbox GL来构建图层，在指定的图层上显示一个弹窗网上查阅了相关的资料，目前只能通过setHTML来实现。而setHTML功能其实跟Vue里面的v-html是一样的，最终调用的是innerHTML方法将指令的value插入到对应的元素里。尝试过使用Element UI实现所需的功能发现在v-html中无法解析，于是只好通过原生代码来实现tab标签页切换以及点击事件了！

## tab标签页

### HTML代码

``` bash
<div class="tab-head">
    <h3 onclick="changeTab(0)" class="selected">实时人数</h3>
    <h3 onclick="changeTab(1)">年龄构成</h3>
    <h3 onclick="changeTab(2)">性别构成</h3>
</div>
<div class="tab-content">
    <div id="c1" class="show">content1</div>
    <div id="c2">content2</div>
    <div id="c3">content3</div>
</div>
```

### CSS样式

``` bash
h2 {
    border-top: solid cornflowerblue 1px;
    border-left: solid cornflowerblue 1px;
    width: 50px;
    height: 25px;
    margin: 0;
    float: left;
    text-align: center;
}

.tab-content {
    border: solid cornflowerblue 1px;
    width: 152px;
    height: 100px;
}

.tab-content div{
    display: none;
}

.selected {
    background-color: cornflowerblue;
}

.tab-content .show{
    display: block;
}
```

### JS

当然了还有一个棘手的问题：v-html不能触发点击事件，解决思路如下！

在Vue生命周期mounted加上

``` bash
window.changeTab = (val) => {
    this.changeTabClick(val)
}
```

在methods添加以下代码实现点击切换tab栏

```bash
	changeTabClick(val) {
      var tabs = document.getElementsByClassName('tab-head')[0].getElementsByTagName('h3')
      var contents = document.getElementsByClassName('tab-content')[0].getElementsByTagName('div')
      for (var i = 0, len = tabs.length; i < len; i++) {
        if (i === val) {
          tabs[i].className = 'selected'
          contents[i].className = 'show'
        } else {
          tabs[i].className = ''
          contents[i].className = ''
        }
      }
    },
```

大功告成！

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2022/20201119140356.png)

## 补充

v-html操作存在安全问题，除非万不得已最好少用这个，容易造成xss攻击，当然vue官网也给出了友好提示 ❝ 只在可信内容上使用 v-html，永不用在用户提交的内容上。

<p class="note note-primary">参考资料：https://www.cnblogs.com/-867259206/p/5664896.html</p>