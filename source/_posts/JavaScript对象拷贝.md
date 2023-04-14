---
title: JavaScript对象拷贝
date: 2022-08-12 17:18:53
tags:
  - JavaScript
index_img: >-
  https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2022/0505422018.jpg
categories: Web前端
abbrlink: c9216b5f
---

###  javaScript 的对象

javascript 的对象只是指向内存中某个位置的指针。这些指针是可变的，也就是说，它们可以重新被赋值。所以仅仅复制这个指针，其结果是有两个指针指向内存中的同一个地址

正是由于此特性，在将一个对象a赋值给b时，再修改b中某个对象属性的值，由于指向的是内存中的同一个地址，a中想对应的属性值也会被更改

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2022/20220812135231.png)

由此引申出对象深拷贝浅拷贝以及解决方案，日常工作开发中，大部分情况都是不需要随着对方的变化而变化的场景，因此我们需要正确处理Javascript 中的对象拷贝

### 浅拷贝

浅拷贝只拷贝**已存在对象的对象属性的引用**，其余非对象属性是占用新的内存空间，并非与原对象共享，只能拷贝复杂数据类型的指针，并不能改变复杂数据类型的地址，只能拷贝外层，并不能彻底拷贝，例如数组中还有数组(对象)，（准确来说是外层引用数据类型）

像常用的数组方法slice()和对象方法Object.assign()都属于浅拷贝

**注： 浅拷贝只能拷贝一层 不能继续拷贝里面的复杂数据类型**

### 深拷贝

#### 简单处理方法

想要对一个对象进行深拷贝，一个可行的方法是先把对象序列化为字符串，然后再对它进行反序列化

```js
this.tempData = JSON.parse(JSON.stringify(this.dataList))
```

But！这其中有一个坑，这个方法只在对象中包含可序列化值，同时没有循环引用的情况下适用，而日期对象是不能直接进行序列化处理的，尽管它显示的是字符串化的 ISO 日期格式，但是 `JSON.parse` 只会把它解析成为一个字符串，而不是日期类型

执行以下代码：

```js
var test = {
	name: 'a',
	date: [new Date(1536627600000), new Date(1540047600000)],
};
let b;
b = JSON.parse(JSON.stringify(test))
```

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2022/20220812152202.png)

如果obj里有RegExp、Error对象，则序列化的结果将只得到空对象

```js
const test = {
	name: 'a',
	date: new RegExp('\\w+'),
};
JSON.parse(JSON.stringify(test));
```



![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2022/20220812153644.png)

如果obj里有函数，undefined，则序列化的结果会把函数或 undefined丢失；

```js
const test1 = {
    name: undefined,
    date: function hehe() {
        console.log('fff')
    },
};
JSON.parse(JSON.stringify(test1));
```



![](https://gcore.jsdelivr.net/gh/laoyerror/pic@main/20220812153941.png)

如果obj里有NaN、Infinity和-Infinity，则序列化的结果会变成null

如果对象中存在循环引用的情况也无法实现深拷贝

JSON.stringify()只能序列化对象的可枚举的自有属性，例如 如果obj中的对象是有构造函数生成的，则使用JSON.parse(JSON.stringify(obj))深拷贝后，会丢弃对象的constructor；

```js
function Person(name) {
    this.name = name;
    console.log(name)
}

const liai = new Person('liai');

const test = {
    name: 'a',
    date: liai,
};
JSON.parse(JSON.stringify(test));
```

总结整理：

**JSON.parse(JSON.stringify())拷贝后的缺陷：**

- NaN     ===》null
- undefined ===》 空
- 时间戳   ===》字符串时间
- 错误信息 ===》 空对象
- Infinity   ===》null

`JSON.parse(JSON.stringify())`虽然简单粗暴，但是存在的问题隐患还是非常多，仅适用于对象的可枚举的自有属性序列化操作

#### 代码实现深拷贝

拷贝一个对象变量会又创建一个对相同对象的引用。

但是，如果我们想要复制一个对象，那该怎么做呢？

我们可以创建一个新对象，通过遍历已有对象的属性，并在原始类型值的层面复制它们，以实现对已有对象结构的复制。

就像这样：

```js
let user = {
  name: "John",
  age: 30
};

let clone = {}; // 新的空对象

// 将 user 中所有的属性拷贝到其中
for (let key in user) {
  clone[key] = user[key];
}

// 现在 clone 是带有相同内容的完全独立的对象
clone.name = "Pete"; // 改变了其中的数据

alert( user.name ); // 原来的对象中的 name 属性依然是 John
```

我们也可以使用 [Object.assign](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign) 方法来达成同样的效果。

语法是：

```javascript
Object.assign(dest, [src1, src2, src3...])
```

- 第一个参数 `dest` 是指目标对象。
- 更后面的参数 `src1, ..., srcN`（可按需传递多个参数）是源对象。
- 该方法将所有源对象的属性拷贝到目标对象 `dest` 中。换句话说，从第二个开始的所有参数的属性都被拷贝到第一个参数的对象中。
- 调用结果返回 `dest`。

如果被拷贝的属性的属性名已经存在，那么它会被覆盖：

```js
let user = { name: "John" };

Object.assign(user, { name: "Pete" });

alert(user.name); // 现在 user = { name: "Pete" }
```

上面的只是简单的数据进行深拷贝，但项目中遇到的数据层级结构一般都比较复杂，这时候就可以通过**递归去复制所有层级属性**

下面代码仅仅是简单的代码示例，开发中碰到的数据类型非常多，根据业务场景自行整理

```js
function deepClone(obj){
    let objClone = Array.isArray(obj)?[]:{};
    if(obj && typeof obj==="object"){
        for(key in obj){
            if(obj.hasOwnProperty(key)){
                //判断ojb子元素是否为对象，如果是，递归复制
                if(obj[key]&&typeof obj[key] ==="object"){
                    objClone[key] = deepClone(obj[key]);
                }else{
                    //如果不是，简单复制
                    objClone[key] = obj[key];
                }
            }
        }
    }
    return objClone;
}    
let a=[1,2,3,4],
    b=deepClone(a);
a[0]=2;
console.log(a,b);
// 打印结果为
// [2, 2, 3, 4], [1, 2, 3, 4]
```

或者为了不重复造轮子，采用现有的实现，例如 [lodash](https://lodash.com/) 库的 [_.cloneDeep(obj)](https://lodash.com/docs#cloneDeep)

```js
// 先安装lodash库 yarn add lodash
var objects = [{ 'a': 1 }, { 'b': 2 }];
var deep = lodash.cloneDeep(objects);
console.log(deep[0] === objects[0]);
// => false
```