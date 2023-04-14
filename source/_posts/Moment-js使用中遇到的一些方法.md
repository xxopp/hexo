---
title: Moment.js使用中遇到的一些方法
tags:
  - Moment
  - JavaScript
index_img: >-
  https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2022/20201119bb.jpg
categories: Web前端
abbrlink: 1448db71
date: 2020-11-20 14:27:55
---

Moment是JavaScript日期处理类库平时转换时间日期时大大方便了前台开发人员的效率。虽然npm安装固态方便，这里也要回顾下JavaScript原生的Date方法！

## 原生获取Date方法

创建一个新Date对象的唯一方法是通过new 操作符; 若将它作为常规函数调用（即不加 new 操作符），将返回一个字符串，而非 Date 对象。

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2022/20201120144124.png)

``` bash
const time = new Date()
time.getDate()	// 以数值返回天（1-31）
time.getDay()	// 以数值获取周名（0-6）
time.getFullYear()	// 获取四位的年（yyyy）
time.getHours()	// 获取小时（0-23）
time.getMilliseconds()	// 获取毫秒（0-999）
time.getMinutes()	// 获取分（0-59）
time.getMonth()	// 获取月（0-11）
time.getSeconds()	// 获取秒（0-59）
time.getTime()	// 获取时间（从 1970 年 1 月 1 日至今）
```

### getTime()打印的为时间戳，将时间戳转换成年月日可以按照一下方法：

```bash
// 需要转换格式： yyyy-MM-dd hh:mm:ss
const date = new Date(1605854372818)
const Y = date.getFullYear() + '-'
const M = (date.getMonth() + 1 < 10 ? '0' + (date.getMonth() + 1) : date.getMonth() + 1) + '-'
const D = date.getDate() + ' '
const h = date.getHours() + ':'
const m = date.getMinutes() + ':'
const s = date.getSeconds()
console.log(Y + M + D + h + m + s)
// 输出结果：2020-11-20 14:39:32
```

### 将日期格式转换成时间戳

```bash
const strtime = '2020-11-20 14:39:32'
const date = new Date(strtime)
// 以下方法均支持获取到最终的时间
const time1 = date.getTime()
const time2 = date.valueOf()
const time3 = Date.parse(date)
console.log(time1, time2, time3)
// 1605854372000 1605854372000 1605854372000
```

上述用到了3个方法获取最终的时间，为了比较三个方法的区别将strtime中的时间精度再设置高一点

```bash
const strtime = '2020-11-20 14:39:32:88'
// 结果
1605854372088 1605854372088 1605854372000
```

结果发现`date.getTime()`和`date.valueOf()`能精确到毫秒，而`Date.parse(date)`只能精确到秒毫秒直接为0

## Moment安装与使用

首先附上官网[http://momentjs.cn/](http://momentjs.cn/)

### 项目安装：

```bash
npm install moment --save   # npm
yarn add moment             # Yarn
// 引用
import moment from 'moment'
```

### 简单使用

```bash
const time = moment(row.update_time).format('yyyy-MM-DD HH:mm:ss')
```

### 设置为中文

```bash
// 从 2.8.1 开始
moment.locale('zh-CN');
```

### 两个时间比较

有些时候会遇到，在某时间前或者在某时间之后作出判断

```bash
var q = '2020-11-20 15:30:00'
var w = '2020-11-20 15:29:59'
console.log(moment(q).isBefore(w)) // false
console.log(moment(w).isBefore(q)) // true
```

### 两个时间是否为同一天

检查一个 moment 是否与另一个 moment 相同。 第一个参数会被解析为 moment（如果尚未解析）

```bash
moment('2020-10-20').isSame('2020-10-20'); // true
```

## 拓展

Day.js是国内大神新出的一款JavaScript日期处理类库，基本上用过moment就会用Day.js了，同时体积大大缩小至2kB ，下载、解析和执行更少的 JavaScript，为您的代码留出更多时间。