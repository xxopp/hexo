---
title: 通讯录实现中英文姓名按照A-Z排序
date: 2022-05-20 09:17:53
tags:
  - JavaScript
  - 代码
index_img: >-
  https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2022/1f60ad40880611ebb6edd017c2d2eca2.jpg
categories: Web前端
abbrlink: ca92deea
---

sort()是js常用的排序方法，基本上满足日常所需的排序场景了，英文排序具有天生的优势：字符序(字母在字符集里的顺序)跟字母序是一致的而中文的拼音、笔画都跟字符序没有固定的关系，想要实现这样的功能，只能自行通过映射拼音、笔画来实现

# 方案1

不过还有个简单的方法那就是localeCompare()

```bash
const array = ['白鸽', '麻雀', '大象', '狗', '猫', "鸡"]
array = array.sort(function compareFunction(item1, item2) {
    return item1.localeCompare(item2,'zh')
})
```

结果

```bash
["白鸽", "大象", "狗", "鸡", "麻雀", "猫"]
```

不过对于一些比较苛刻的场景，如中英文混个排序显然是不行了，果断pass

# 方案2

思路如下：首先通过正则判断姓名是否是中文还是英文，然后分开排序

## 代码部分

```bash
pySegSort(arr) {
	const pattern = new RegExp("[\u4E00-\u9FA5]+")
	const pattern2 = new RegExp("[A-Za-z]+")
	if (!String.prototype.localeCompare) return null
	// 小写部分 let letters = 'abcdefghjklmnopqrstwxyz'.split('')
	let letters = 'ABCDEFGHJKLMNOPQRSTWXYZ'.split('')
	let zh = '阿八嚓哒妸发旮哈讥咔垃痳拏噢妑七呥扨它穵夕丫帀'.split('')
	let segs = []
	letters.forEach((item, i) => {
		let cur = {
			letter: item,
			data: []
		}
		arr.forEach((item) => {
			console.log(item.realname.slice(0, 1))
			if (pattern2.test(item.realname.slice(0, 1))) {
				if (item.realname.localeCompare(letters[i]) >= 0 && item.realname.localeCompare(letters[i + 1]) < 0) {
					cur.data.push(item)
				}
			} else {
                    if (item.realname.localeCompare(zh[i]) >= 0 && item.realname.localeCompare(zh[i + 1]) < 0) {
                    cur.data.push(item)
				}
			}
		})
		if (cur.data.length) {
			cur.data.sort(function(a, b) {
				return a.realname.localeCompare(b.realname, 'zh')
			})
			segs.push(cur)
		}
	})
```

到这里基本上就完成了，不过项目上线后发现存在部分姓氏出错的问题

## 方案2

github上找到的开源项目C2Pin，可以直接引用到项目中

https://github.com/Alfred-kai/C2Pin/blob/master/index.js

```bash
import C2Pin from 'c2pin'
```

用首字母将汉语转拼音

```bash
C2Pin.firstChar()
```

用全字母将汉语转拼音

```bash
C2Pin.fullChar()
```

## 完整代码

```bash
pySegSort(arr) {
	const pattern = new RegExp("[\u4E00-\u9FA5]+")
	const pattern2 = new RegExp("[A-Za-z]+")
	if (!String.prototype.localeCompare) return null
	let letters = 'ABCDEFGHJKLMNOPQRSTWXYZ'.split('')
	let segs = []
	letters.forEach((item, i) => {
		let cur = {
			letter: item,
			data: []
		}
		arr.forEach((item) => {
			if (pattern2.test(item.realname.slice(0, 1))) {
				if (item.realname.localeCompare(letters[i]) >= 0 && item.realname.localeCompare(letters[i + 1]) < 0) {
						cur.data.push(item)
					}
				} else {
					let num = C2Pin.firstChar(item.realname.slice(0, 1)).toUpperCase()
					if (num === letters[i]) {
						cur.data.push(item)
					}
				}
			})
			if (cur.data.length) {
				cur.data.sort(function(a, b) {
					return a.realname.localeCompare(b.realname, 'zh')
				})
				segs.push(cur)
			}
		})
		const letterArr = segs.map(val => {
			return val.letter
		})
		this.indexList = letterArr
		this.listData = segs
		
```

# 总结



<p class="note note-primary">参考资料：https://github.com/Alfred-kai/C2Pin</p>