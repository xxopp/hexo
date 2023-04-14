---
title: css多行溢出加省略号方法
date: 2022-05-20 13:18:53
tags:
  - CSS
index_img: 'https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2022/0405383830.jpg'
categories: Web前端
abbrlink: 6c1f1d06
---

文字溢出为了美观前端都会给溢出部分新增省略号，最近项目中正好遇到一些小问题特此记录一下

### 单行

```
width: 300px;
overflow: hidden;
/*文本不会换行*/
white-space: nowrap;
/*当文本溢出包含元素时，以省略号表示超出的文本*/
text-overflow: ellipsis;
```

### 多行

```bash
text-overflow: ellipsis;
overflow: hidden;
display: -webkit-box; /*将对象作为弹性伸缩盒子模型显示*/
-webkit-line-clamp: 2;  /*设置显示的行数，多出的部分会显示为...*/
-webkit-box-orient: vertical;  /*设置子元素排列方式*/
```

### 特殊情况

引用第三方的插件clamp-js

```bash
yarn add clamp-js
```

[参考资料](https://www.npmjs.com/package/clamp-js)  [Github](https://github.com/josephschmitt/Clamp.js)

```bash
import clamp from 'clamp-js'
```

demo代码如下：

```html
<div class="contents" style="margin: 5px 0">
    <div class="hhh">2343碘酸钾颗粒感激遂弹冠零售店杰克伦敦是跟你考虑大肆攻击零售店管理单身狗捩手覆羹发多了发24234
    </div>
</div>
<div class="contents" style="margin: 5px 0">
    <div class="hhh">2343山峰观看立方公里咖啡馆发了打开官方力杜卡宁班克劳夫东南部老地方懒得你布隆方丹播放底部房贷吧24234
    </div>
</div>
<div class="contents" style="margin: 5px 0">
    <div class="hhh">234324 方便毒奶粉后殿函关尽过 峰哥地负海涵峰哥峰哥法国队就感觉峰哥交房后交房234
    </div>
</div>

<button @click="demo">demo</button>
```

js代码：

```bash
const dom = document.querySelectorAll('.contents')
for (let index = 0; index < dom.length; index++) {
    let text =  dom[index].getElementsByClassName('hhh')[0]
    console.log(text.innerText)
    clamp(text, {clamp: 2}) // 第二行溢出部分加省略号
}
```

### 最终结果：

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2022/20210907152240.png)
