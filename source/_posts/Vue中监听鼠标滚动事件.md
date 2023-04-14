---
title: Vue中监听鼠标滚动事件
tags:
  - Vue
  - JavaScript
index_img: 'https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2022/20201119c.jpg'
categories: Web前端
abbrlink: dd5a6262
date: 2020-11-19 14:55:08
---

在mounted()方法里监听mousewheel

Chrome以及IE：

```bash
window.addEventListener('mousewheel',this.handleScroll,false)
```

火狐浏览器：

```bash
window.addEventListener('DOMMouseScroll',this.handleScroll,false)
```

methods中定义方法

```bash
handleScroll (e) {
	var direction = e.deltaY>0?'down':'up' // 判断滚轮是向上滑动还是向下
}
```

