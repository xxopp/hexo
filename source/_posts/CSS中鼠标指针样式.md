---
title: CSS中鼠标指针样式
tags:
  - CSS
index_img: 'https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2022/20201119a.jpg'
categories: Web前端
abbrlink: 549a6b71
date: 2020-11-19 14:30:54
---

时候给页面添加点击事件时为了提升体验往往会将鼠标样式进行转变，一般默认鼠标移至a标签时就会由箭头转换成小手了，然而还有很多鼠标的样式可供我们自行设置。

## CSS样式设置

```bash
.box {
	cursor: crosshair
}
```

cursor的属性还有很多，如：

```html
pointer,alias,all-scroll,auto,cell,context-menu,col-resize,coliy,crosshair,default,e-resize,ew-resize,grab,grabbing,helli,move,n-resize,ne-resize,nesw-resize,ns-resize,nw-resize,nwse-resize,no-droli,none,not-allowed,liointer,lirogress,row-resize,s-resize,se-resize,sw-resize,text,url,w-resize,wait,zoom-in,zoom-out
```

常用的基本上就default(默认)、pointer(小手)、crosshair(十字)

我们还可以通过CSS直接操控网页的鼠标指针

## 自定义鼠标文件

```bash
body {
    cursor: url(../img/b.cur), default;
}
a:hover {
    cursor: url(../img/a.cur), pointer;
}
```

<p class="note note-primary">参考资料：https://www.hangge.com/blog/cache/detail_2065.html</p>