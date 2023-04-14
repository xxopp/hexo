---
title: js获取一年所有的周
tags:
  - JavaScript
  - 代码
index_img: >-
  https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2022/children-593313_640.jpg
categories: Web前端
abbrlink: 9ee6abd3
date: 2023-01-10 17:13:17
---

 ### 获取一年中所有周(周一开始，周日结束)

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2022/78c7ef7dedc3496cba7018f879b97281.png)

### 代码

```js
// 传入年份,字符串类型年份,'2020'
setweekOption(year) {
    year = new Date(year).getFullYear()
    let days = getDay(year || new Date().getFullYear())
    // console.log(days)
    let weeks = {};
    for (let i = 0; i < days.length; i++) {
        let weeksKeyLen = Object.keys(weeks).length;
        let daySplit = days[i].split('_');
        if (weeks[weeksKeyLen] === undefined) {
            weeks[weeksKeyLen + 1] = [daySplit[0]]
        } else {
            if (daySplit[1] == '1') {
                weeks[weeksKeyLen + 1] = [daySplit[0]]
            } else {
                weeks[weeksKeyLen].push(daySplit[0])
            }
        }
    }
    let option = []
    let weeksKeyLen = Object.keys(weeks).length;
    for (let i = 1; i < weeksKeyLen + 1; i++) {
        let obj = {};
        obj.year = year
        obj.week = "第" + i + "周" 
        obj.md = weeks[i][0] + '-' + weeks[i][weeks[i].length - 1] 
        obj.s = weeks[i][0]
        obj.e = weeks[i][weeks[i].length - 1] 
        obj.text = "第" + i + "周" + '(' + weeks[i][0] + '-' + weeks[i][weeks[i].length - 1] + ')';
        obj.value = i;
        option.push(obj)
    }
    return option;
}
getDay(year) {
    let dates = [];
    for (let i = 1; i <= 12; i++) {
        for (let j = 1; j <= new Date(year, i, 0).getDate(); j++) {
            dates.push(formatNumber(i) + '/' + formatNumber(j) + '_' + new Date([year, formatNumber(i), formatNumber(j)].join('-')).getDay()) //返回当年所有日期（带星期数）
        }
    }
    return dates;
}
formatNumber(n) {
    return n.toString().length > 1 ? n : '0' + n
}

```

### 获取当前在今年的第几周

```js
let date = new Date()
// 调用
this.getYearWeek(date.getFullYear(), date.getMonth() + 1, date.getDate()) - 1

// 方法
getYearWeek (a, b, c) {
    // a为年 b为月 c为日
    let date1 = new Date(a, parseInt(b) - 1, c)
    let date2 = new Date(a, 0, 1)
    let d = Math.round((date1.valueOf() - date2.valueOf()) / 86400000)
    return Math.ceil((d + ((date2.getDay() + 1) - 1)) / 7)
}
```

