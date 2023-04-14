---
title: JavaScript模块化知识点整理
date: 2022-08-08 14:15:53
tags:
  - JavaScript
  - 代码
index_img: >-
  https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2022/person-875165_640.jpg
categories: Web前端
abbrlink: 52b407f5
---

前端模块化：AMD、CMD、ES6 Module和CommonJS

#### CommonJS

NodeJS是CommonJS规范的主要实践者，它有四个重要的环境变量为模块化的实现提供支持：`module`、`exports`、`require`、`global`。实际使用时，用`module.exports`定义当前模块对外输出的接口（不推荐直接用`exports`），用`require`加载模块。

`exports`和`module.export`区别：

`exports`：对于本身来讲是一个变量（对象），它不是module的引用，它是`{}`的引用，它指向`module.exports`的{}模块。只能使用`.`语法 向外暴露变量。

`module.exports`：`module`是一个变量，指向一块内存，`exports`是`module`中的一个属性，存储在内存中，然后`exports`属性指向`{}`模块。既可以使用`.`语法，也可以使用`=`直接赋值。

```js
// 定义模块(module.exports)
var basicNum = 0;
function add(a, b) {
  return a + b;
}
module.exports = {
  //在这里写上需要向外暴露的函数、变量
  add: add,
  basicNum: basicNum
}
// 引入
var math = require("./demo");
math.add(2, 5);
// --------------------------------
// 如果是exports则是
exports.name = 'hello';
exports.add = function(a, b) {
  return a + b;
}
// 引入
var math = require("./demo");
math.add(2, 5);

```

CommonJS用**同步**的方式加载模块。在服务端，模块文件都存放在本地磁盘，读取非常快，所以这样做不会有问题。但是在浏览器端，限于网络原因，更合理的方案是使用**异步加载**。

#### AMD、CMD

AMD、CMD都是异步加载的方式且支持浏览端环境运行，所有依赖模块的语句，都定义在一个回调函数中，等到加载完成之后，这个回调函数才会运行

AMD/CMD区别，虽然都是并行加载js文件，但还是有所区别，AMD是预加载，在并行加载js文件同时，还会解析执行该模块（因为还需要执行，所以在加载某个模块前，这个模块的依赖模块需要先加载完成）；而CMD是懒加载，虽然会一开始就并行加载js文件，但是不会执行，而是在需要的时候才执行。

AMD/CMD的优缺点.一个的优点就是另一个的缺点， 可以对照浏览。
AMD优点：加载快速，尤其遇到多个大文件，因为并行解析，所以同一时间可以解析多个文件。
AMD缺点：并行加载，异步处理，加载顺序不一定，可能会造成一些困扰，甚至为程序埋下大坑。

CMD优点：因为只有在使用的时候才会解析执行js文件，因此，每个JS文件的执行顺序在代码中是有体现的，是可控的。

CMD缺点：执行等待时间会叠加。因为每个文件执行时是同步执行（串行执行），因此时间是所有文件解析执行时间之和，尤其在文件较多较大时，这种缺点尤为明显。（PS：重新看这篇文章，发现这里写的不是很准确。确切来说，JS是单线程，所有JS文件执行时间叠加在AMD和CMD中是一样的。但是CMD是使用时执行，没法利用空闲时间，而AMD是文件加载好就执行，往往可以利用一些空闲时间。这么来看，CMD比AMD的优点还是很明显的，毕竟AMD加载好的时候也未必就是JS引擎的空闲时间！）

#### ES6 Module

ES6 在语言标准的层面上，实现了模块功能，而且实现得相当简单，旨在成为浏览器和服务器通用的模块解决方案。其模块功能主要由两个命令构成：`export`和`import`。`export`命令用于规定模块的对外接口，`import`命令用于输入其他模块提供的功能。

但ES6中常常伴有export default 和 export ，对此进行梳理

```tex
1.export与export default均可用于导出常量、函数、文件、模块等
2.你可以在其它文件或模块中通过import+(常量 | 函数 | 文件 | 模块)名的方式，将其导入，以便能够对其进行使用
3.在一个文件或模块中，export、import可以有多个，export default仅有一个
4.通过export方式导出，在导入时要加{ }，export default则不需要
```
如果想为输入的变量重新命名，可以使用as关键字(export default情况下无需考虑)，语法如下:

```js
import { value as str } from './demo'; // 将str更名为value
```

```jsx
// export---
export const str = "blablabla~";
export function log(sth) { 
  return sth;
}
// 对应的导入方式：
import { str, log } from './demo'; //也可以分开写两次，导入的时候带花括号

// export default---
// **一个文件内不能有多个export default**
const str = "blablabla~";
export default str;
// 对应的导入方式：
import str from 'a'; //导入的时候没有花括号
```

使用export default命令，为模块指定默认输出，不需要知道所要加载模块的变量名

本质上，文件的export default输出一个叫做default的变量，然后系统允许你为它取任意名字

所以可以为import的模块起任何变量名，且不需要用大括号包含

仅支持这样导出变量，**方法会直接报错**

```js
const str = "blablabla~";
export default str;

// 引入
import any from "./demo";
console.log(any);
```

有多个命名导出和一个默认导出:

```js
export const PI = 3.14159;
export const AGE = 30;
const NAME = "David";
export default NAME;

// 引入
import NAME, { PI, AGE } from './demo';
```

export default变量命名简写

```js
export default {
 name: "Tom",
 age: 40
};

// 引入
import user from "./demo";
// 使用
console.log(user.name);  // Tom
console.log(user.age);  // 40
```

https://chinese.freecodecamp.org/news/learn-modern-javascript/

https://www.cnblogs.com/chenwenhao/p/12153332.html

ES6的模块不是对象，`import`命令会被 JavaScript 引擎静态分析，在编译时就引入模块代码，而不是在代码运行时加载，所以无法实现条件加载。也正因为这个，使得静态分析成为可能。

ES6 模块的特征：

- 严格模式：ES6 的模块自动采用严格模式
- `import` read-only特性： `import`的属性是只读的，不能赋值，类似于`const`的特性
- `export/import`提升： `import/export`必须位于模块顶级，不能位于作用域内；其次对于模块内的`import/export`会提升到模块顶部，这是在编译阶段完成的

#### ES6 模块与 CommonJS 模块的差异

https://github.com/YvetteLau/Step-By-Step/issues/43

https://zhuanlan.zhihu.com/p/121770261

ES6模块是引用，重新赋值会编译报错，不能修改其变量的指针指向，但可以改变内部属性的值；
CommonJS模块是拷贝（**浅拷贝**），可以重新赋值，可以修改指针指向；

ES6

1. ES6模块中的值属于动态只读引用。
2. 对于只读来说，即不允许修改引入变量的值，import的变量是只读的，不论是基本数据类型还是复杂数据类型。当模块遇到import命令时，就会生成一个只读引用。等到脚本真正执行时，再根据这个只读引用，到被加载的那个模块里面去取值。
3. 对于动态来说，原始值发生变化，import加载的值也会发生变化。不论是基本数据类型还是复杂数据类型。
4. 循环加载时，ES6模块是动态引用。只要两个模块之间存在某个引用，代码就能够执行。

CommonJS

1. 对于基本数据类型，属于复制。即会被模块缓存。同时，在另一个模块可以对该模块输出的变量重新赋值。
2. 对于复杂数据类型，属于浅拷贝。由于两个模块引用的对象指向同一个内存空间，因此对该模块的值做修改时会影响另一个模块。
3. 当使用require命令加载某个模块时，就会运行整个模块的代码。
4. 当使用require命令加载同一个模块时，不会再执行该模块，而是取到缓存之中的值。也就是说，CommonJS模块无论加载多少次，都只会在第一次加载时运行一次，以后再加载，就返回第一次运行的结果，除非手动清除系统缓存。
5. 当循环加载时，脚本代码在require的时候，就会全部执行。一旦出现某个模块被"循环加载"，就只输出已经执行的部分，还未执行的部分不会输出。

CommonJS模块输出的是一个值的复制，ES6模块输出的是值的引用
CommonJS模块是运行时加载，ES6模块是编译时输出接口
第二个差异是因为CommonJS加载的是一个对象，即module.export属性，该对象只有在脚本运行结束时才会生成。而ES6模块不是对象，它的对外接口只是一种静态定义，在代码静态解析阶段就会生成。
下面重点解释第一个差异。
CommonJS模块输出的是值的复制，一旦输出这个值，模块内部的变化就影响不到这个值。