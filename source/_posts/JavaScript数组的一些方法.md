---
title: JavaScript数组的一些方法
tags:
  - JavaScript
  - 代码
index_img: >-
  https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2022/woman-1509956_640.jpg
categories: Web前端
abbrlink: cc5cb51a
date: 2022-08-18 10:09:46
---

#### 数组原型方法

```js
join()：// 用指定的分隔符将数组每一项拼接为字符串
push() ：// 向数组的末尾添加新元素
pop()：// 删除数组的最后一项
shift()：// 删除数组的第一项
unshift()：// 向数组首位添加新元素
slice()：// 按照条件查找出其中的部分元素
splice()：// 对数组进行增删改
fill(): // 方法能使用特定值填充数组中的一个或多个元素
filter(): // “过滤”功能
concat()：// 用于连接两个或多个数组
indexOf()：// 检测当前值在数组中第一次出现的位置索引
lastIndexOf()：// 检测当前值在数组中最后一次出现的位置索引
every()：// 判断数组中每一项都是否满足条件
some()：// 判断数组中是否存在满足条件的项
includes()：// 判断一个数组是否包含一个指定的值
sort()：// 对数组的元素进行排序
reverse()：// 对数组进行倒序
forEach()：// ES5 及以下循环遍历数组每一项
map()：// ES6 循环遍历数组每一项
copyWithin(): // 用于从数组的指定位置拷贝元素到数组的另一个指定位置中
find(): // 返回匹配的值
findIndex(): // 返回匹配位置的索引
toLocaleString()、toString(): // 将数组转换为字符串
flat()、flatMap()：// 扁平化数组
entries() 、keys() 、values(): // 遍历数组
```

##### slice()

slice()：返回从原数组中指定开始下标到结束下标之间的项组成的新数组，原数组不变

```js
var arr = [1,3,5,7,9,11];
var arrCopy = arr.slice(1);
var arrCopy2 = arr.slice(1,4);
var arrCopy3 = arr.slice(1,-2);//相当于arr.slice(1,4)
var arrCopy4 = arr.slice(-4,-1);//相当于arr.slice(2,5)
console.log(arr);   //[1, 3, 5, 7, 9, 11](原数组没变)
console.log(arrCopy);   //[3, 5, 7, 9, 11]
console.log(arrCopy2);   //[3, 5, 7]
console.log(arrCopy3);   //[3, 5, 7]
console.log(arrCopy4);   //[5, 7, 9]
```

##### splice()

`splice()`：很强大的数组方法，它有很多种用法，可以**实现删除、插入和替换**

```js
// 删除元素,并返回删除的元素
var arr = [1,3,5,7,9,11];
var arrRemoved = arr.splice(0,2);
console.log(arr);   // [5, 7, 9, 11]
console.log(arrRemoved);   // [1, 3]
// 向指定索引处添加元素
var array1 = [22, 3, 31, 12];
array1.splice(1, 0, 12, 35);  // []
console.log(array1); // [22, 12, 35, 3, 31, 12]
// 替换指定索引位置的元素
const array1 = [22, 3, 31, 12];
array1.splice(1, 1, 8);   // [3]
console.log(array1);  // [22, 8, 31, 12]
```

##### indexOf()和 lastIndexOf()

接收两个参数：要查找的项和（可选的）表示查找起点位置的索引。

`indexOf()`： 从数组的开头（位置 0）开始向后查找。

`lastIndexOf`： 从数组的末尾开始向前查找。

这两个方法都返回要查找的项在数组中的位置，或者在**没找到的情况下返回-1**。在比较第一个参数与数组中的每一项时，会**使用全等操作符**。

```js
var arr = [1,3,5,7,7,5,3,1];
console.log(arr.indexOf(5));   //2
console.log(arr.lastIndexOf(5));   //5
console.log(arr.indexOf(5,2));   //2
console.log(arr.lastIndexOf(5,4));   //2
console.log(arr.indexOf("5"));   //-1
```

##### find() 和 findIndex()

find()与 findIndex()方法均接受两个参数：一个回调函数，一个可选值用于指定回调函数内部的 this。

该回调函数可接受三个参数：数组的某个元素，该元素对应的索引位置，以及该数组本身。

该回调函数应当在给定的元素满足你定义的条件时返回 true，而 find()和 findIndex()方法均会在回调函数**第一次返回 true 时停止查找**。

**二者的区别是**：find()方法返回匹配的值，而 findIndex()返回匹配位置的索引。

```js
let arr = [1, 2, 3, 'arr', 5, 1, 9];

console.log(arr.find((value, keys, arr) => {
    return value > 2;
})); // 3 返回匹配的值

console.log(arr.findIndex((value, keys, arr) => {
    return value > 2;
})); // 2 返回匹配位置的索引
```

##### map()和filter()

map()、filter()中都提供一个回调函数，回调函数中有三个参数分别是数组元素，元素索引，原数组本身，并且这两个方法在使用的过程中都不会改变原数组。

```js
// map()
var a = [1,2,3,4];
var newa = a.map(function(x) {
 return x = x + 1;
});
console.log(newa,a); 
// newa : 2 3 4 5   // a: 1 2 3 4

// filter()
var a = [1,2,3,4];
var newa = a.filter(function(x) {
 return x > 1;
});
console.log(newa,a); 
// newa : 2 3 4    // a: 1 2 3 4

// 把map中调用的函数换成filter里面的过滤函数是否能得到相同的结果
var a = [1,2,3,4];
var newa = a.map(function(x) {
 return x > 1;
});
console.log(newa,a); 
// newa :false true true true     // a: 1 2 3 4
```

由此得出，map()中的callback需要有return值，而filter为“过滤”、“筛选”之意。指数组filter后，返回过滤后的新数组

filter的callback函数需要返回布尔值true或false，在实际使用的时候，我们可以利用map方法方便获得对象数组中的特定属性值

##### every()和some()

every()：判断数组中每一项都是否满足条件，只有所有项都满足条件，才会返回 true。

```js
var arr = [1, 2, 3, 4, 5];
var arr2 = arr.every(function(x) {
	return x < 10;
});
console.log(arr2);  //true
var arr3 = arr.every(function(x) {
	return x < 3;
});
console.log(arr3);  // false
```

some()：判断数组中是否存在满足条件的项，只要有一项满足条件，就会返回 true。

```js
var arr = [1, 2, 3, 4, 5];
var arr2 = arr.some(function(x) {
	return x < 3;
});
console.log(arr2);  //true
var arr3 = arr.some(function(x) {
	return x < 1;
});
console.log(arr3);  // false
```

##### ES6遍历数组

entries()，keys()和values() —— 用于遍历数组。它们都返回一个遍历器对象，可以用for...of循环进行遍历

区别是**keys()是对键名的遍历**、**values()是对键值的遍历**，**entries()是对键值对的遍历**

```js
for (let index of ['a', 'b'].keys()) {  
console.log(index);  
}  
// 0  
// 1  
for (let elem of ['a', 'b'].values()) {  
console.log(elem);  
}  
// 'a'  
// 'b'  
for (let [index, elem] of ['a', 'b'].entries()) {  
console.log(index, elem);  
}  
// 0 "a"  
// 1 "b" 
```

如果不使用for...of循环，可以手动调用遍历器对象的**next方法**，进行遍历

`next()使用几次就执行几次遍历`

```js
let letter = ['a', 'b', 'c'];  
let entries = letter.entries();  
console.log(entries.next().value); // [0, 'a']  
console.log(entries.next().value); // [1, 'b']  
console.log(entries.next().value); // [2, 'c'] 
```

#### 数组去重

```js
const arr = [1,2,3,4,5,1,3,4]
Array.from(new Set(arr))
// [1, 2, 3, 4, 5]
```

#### 字符串数组转换

```js
// 字符串转数组
.split(',')
// 数组转字符串
join(',')
```

#### 判断数组中是否存在某个值

```js
// array.includes(searcElement[,fromIndex])
// 此方法判断数组中是否存在某个值，如果存在返回true，否则返回false
var arr=[1,2,3,4];
if(arr.includes(3))
    console.log("存在");
else
    console.log("不存在");
```

#### 返回数组中满足条件的第一个元素的值

```js
// array.find(callback[,thisArg])
// 返回数组中满足条件的第一个元素的值，如果没有，返回 undefined
var arr=[1,2,3,4];
var result = arr.find(item =>{
    return item > 3
});
console.log(result); // 4
```

#### 两数组对比是否相等

```js
// 数组排序后在转成字符串对比是否相等
arr1.sort().toString() === arr2.sort().toString()
```

#### 参考

https://juejin.cn/post/6907109642917117965