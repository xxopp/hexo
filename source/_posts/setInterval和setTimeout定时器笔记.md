---
title: setInterval和setTimeout定时器笔记
tags:
  - JavaScript
  - Vue
index_img: 'https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2022/020677585.jpg'
categories: Web前端
abbrlink: ce49fcb8
date: 2020-12-02 16:09:14
---

定时器在前端开发中用处倒是非常多的，目前只要分为setInterval和setTimeout，通过设置时间就能在一些时间范围内执行我们预先写好的代码。定时器的时间单位为：1000 毫秒= 1 秒

### setInterval

setInterval() 方法可按照指定的周期（以毫秒计）来调用函数或计算表达式。

setInterval() 方法会不停地调用函数，直到 clearInterval() 被调用或窗口被关闭。

```bash
timer = setInterval(() => {
	console.log('hello!')
}, 1000)
```

关闭定时器方法：

```bash
clearInterval(timer) // timer为定时器的返回的ID编号
```

注意：将定时器赋值给一个变量时，由 setInterval() 返回的 `ID` 值可用作 clearInterval() 方法的参数，setTimeout同样

### setTimeout

setTimeout() 方法用于在指定的毫秒数后调用函数或计算表达式，且只执行一次

```bash
timer = setTimeout(() => {
	console.log('hello!')
}, 1000)
```

关闭定时器方法：

```bash
clearTimeout(timer) // timer为定时器的返回的ID编号
```

### 性能问题

最近Vue项目中有一个功能，就是每隔5秒刷新一次数据，加入定时器后执行一段时间电脑风扇就呼呼转了，网上查阅一番发现是定时器写法问题。

单纯的使用setInterval会导致页面卡死！由于setInterval不会清除定时器队列，每重复执行1次都会导致定时器叠加，最终卡死网页。

当然了定时器有自带的清除定时器的方法，可以这样设置：

```bash
let timer = setInterval(() => {
	this.fun(timer)
}, 5000)
```

fun函数里面这样写:

```bash
fun (timer) {
	setTimeout(()=>{
		// 此处写执行代码
			………………
		// 执行到某个条件关闭定时器可以在条件中加上clearInterval(timer)
	}, 0)
}
```

### 补充

`定时器是异步的`，当浏览器解析到定时器时，浏览器会记录一下当前的时间，等过了你设置的时间以后会让回调函数执行

<p class="note note-primary">参考资料：https://blog.csdn.net/qq_39840470/article/details/104635153</p>