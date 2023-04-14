---
title: JavaScript Promise异步处理
date: 2022-08-12 11:18:53
tags:
  - JavaScript
  - 代码
index_img: >-
  https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2022/0605459972.jpg
categories: Web前端
abbrlink: f1273b70
---

在了解Promise之前我们需要先了解线程、同步异步、事件循环机制辅助深入

### 单线程

javaScript的单线程，与它的用途有关。作为浏览器脚本语言，javaScript的主要用途是与用户互动，以及操作DOM。这决定了它只能是单线程，否则会带来很复杂的同步问题。比如，假定javaScript同时有两个线程，一个线程在某个DOM节点上添加内容，另一个线程删除了这个节点，这时浏览器应该以哪个线程为准？

所以，为了避免复杂性，从一诞生，javaScript就是单线程，这已经成这门语言的核心特征，将来也不会改变。

注：所谓单线程，是指在js引擎中负责解释和执行javaScript代码的线程只有一个。

**javaScript是单线程的，但是浏览器不是单线程的。一些I/O操作，定时器的计时和事件监听是由其他线程完成的。**

到这里我们要提一下浏览器的线程问题。

与js相关的浏览器线程有三个（注意：js解析是单线程） - js代码执行线程( 主线程 ) - UI渲染线程 - 事件循环线程

其中js代码执行线程与UI渲染线程两者是互斥的，也就是说js代码执行线程运行的时候，UI渲染线程会停止工作，这样做也是为了防止js中的DOM操作会导致两线程冲突；而事件循环线程比较特殊，下面会根据setTimerout的执行过程进行讲解其作用

### 同步异步

#### 同步

如果在函数返回结果的时候，调用者能够拿到预期的结果(就是函数计算的结果)，那么这个函数就是同步的，即在主进程立即执行且前一个任务没有执行完成，后面的任务不能往下执行的任务；

如果函数是同步的，即使调用函数执行任务比较耗时，也会一致等待直到得到执行结果。如下面的代码：

```js
function wait() {
    var time = (new Date()).getTime(); // 获取当前的unix时间戳
    while((new Date()).getTime() - time > 5000) {
        return
    }
    console.log('5秒过去了');
}
wait();
console.log('慢死了');
```

#### 异步

简单说就是在处理某一件事的时候还可以去做别的事，比如：你在银行取号后等待取钱，在等待的过程中你还可以玩手机，和别人聊天等等，这个过程就是异步的

不进入主进程而进入任务队列的任务，只有任务队列通知主进程某个任务可以执行了，异步任务才会进入主进程开始执行；

异步任务我们又分为宏任务（macro-task）和微任务（micro-task）：　　　　 

宏任务（macro-task）包括：整体代码script、setTimeout、setInterval

微任务（micro-task）包括：Promise( **注意：new Promise() 是同步任务，`resolve、.then、.catch等方法`才是异步任务**)、process.nextTick

**tips：同一轮事件循环中，微任务永远比宏任务先执行；**

#### 代码分析

js异步操作的有：

```js
setTimeout
setInterval
ajax
……
```

经典的异步代码：

```js
console.log('1')
setTimeout(function(){
 console.log('2')
},100)
console.log('3')  
// 1，3，2
```

也就是说，setTimeout里的函数并没有立即执行，而是延迟了一段时间，满足一定条件后，才去执行的，这类代码，我们叫异步代码

拓展一下特殊的场景，将事件循环设为0

```js
console.log('1')
setTimeout(function(){
 console.log('2')
},0)
console.log('3')  
// 1，3，2
```

1、3、2，不是1、2、3。到这里你可能会有疑惑了，明明定时器设置的时间为0，而且浏览器解析js是按照从上到下执行的，应该是1、2、3才对啊？

到这里我们要提一下浏览器的线程问题。

与js相关的浏览器线程有三个（注意：js解析是单线程） - js代码执行线程( 主线程 ) - UI渲染线程 - 事件循环线程

其中js代码执行线程与UI渲染线程两者是互斥的，也就是说js代码执行线程运行的时候，UI渲染线程会停止工作，这样做也是为了防止js中的DOM操作会导致两线程冲突；**而事件循环线程比较特殊**

**执行过程：**

1. js主线程运行，遇到console.log(1)，直接运行，在控制台输出结果；
2. 主线程继续运行，然后遇到一个setTimeout，接着setTimeout中的回调函数会被放入到一个事件队列中（这里的事件队里可以想象成一个备忘录，里面记录的全是一些需要做而未做的事）；
3. 遇到第二个setTimeout，其中的回调函数依然被加入到事件队列中；
4. 执行console.log('3')，到这里主线程的任务全部执行完毕，除了setTimeout里面的回调函数；
5. 这个时候，我们还未说明的事件循环线程开始工作，它会去循环遍历事件队列（也就是我们的备忘录），如果事件队列中有回调函数，它就会将事件队列中的回调函数重新交给主线程；
6. 主线程收到回调函数，然后开始执行函数体。（这里要注意，因为两个setTimeout本身有执行时间，所以在这里的时候就会根据时间的长短按顺序执行啦。）

**总的来说，setTimeout与setInterval的执行会等到主线程的所有任务全部执行后，才会再执行其中的回调函数，所以在使用它们的时候也要注意，特别是在主线程中有特别耗时的任务的时候，两种定时器会被不可预测的延后。**

讲到这里，大家有没有想到什么呢？

恩，就是异步，setTimeout的执行有没有一点异步的感觉呢？但又因为它必须是等到主线程全部执行完才会执行，所以可以称之为伪异步。

ajax异步执行过程与setTimeout一样，ajax中也是有回调函数的，比如请求成功后的回调，失败后的回调，这些回调函数与setTimeout中的回调一样会被推入到事件队列中，浏览器接会再次提供一个线程接收AJAX请求返回的数据，事件循环线程这时候知道事件队列中AJAX的回调函数能够被执行了，遍历事件队列，将其中的回调函数交回给js主线程，主线程执行AJAX回调函数内部代码

总的说来，AJAX的请求不会干扰到主线程任务的执行，它有自己专供的线程来处理其任务，就像是浏览器的亲儿子

### javaScript事件循环机制

https://www.cnblogs.com/dengyao-blogs/p/13156045.html

同步和异步任务分别进入不同的执行"场所"，同步的进入主线程，异步的进入Event Table（任务队列）并注册函数。

当指定的事情完成时，Event Table（任务队列）会将这个函数移入Event Queue（事件队列）。

主线程内的任务执行完毕为空，会去Event Queue（事件队列）读取对应的函数，进入主线程执行。

上述过程会不断重复循环，也就是常说的Event Loop(事件循环)。

![](https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/hexo/2022/1650307-20210426133431635-1846058683.png)

#### 拓展练习

```js
console.log('1');   
 setTimeout(() => {
    console.log('2')  
 }, 1000);
 new Promise((resolve, reject) => {
    setTimeout(() => {
        console.log('3');  
    }, 0);
    console.log('4');  
    resolve();
    console.log('5');  
 }).then(() => {
    console.log('6');  
 });
 console.log('7');  
```

#### 打印结果

```js
" 1 "
" 4 "
" 5 "
" 7 "
" 6 "
" 3 "
" 2 "
```

#### 简单分析

首先浏览器会执行" 1 "这个同步任务，接着往下发现是异步宏任务，放入到事件队列中接着往下走，Promise里面还是有异步宏任务，继续往下发现两个同步任务，同步任务按顺序执行所以先输出" 4 "在输出" 5 "，接着往下.then是异步微任务，放入任务队列中；然后发现同步任务执行" 7 "，本轮同步任务执行完毕以后会去执行异步队列中的微任务，所以我们接着会去执行.then方法输出" 6 "，微任务执行完成以后浏览器再执行本轮循环中的宏任务，所有浏览器再执行Promise里面的setTimeout输出" 3 "，最后我们这一轮事件循环执行完了，然后与Promise同层的" 2 "还没有执行，还在任务队列中，最后浏览器再执行且输出" 2 "；注意，事件循环是分轮次的，只有本层的任务执行完以后再会往上去执行；所以我们Promise里面的setTimeout要先于外层的setTimeout执行，这个就是因为层级的原因；所以最后的执行结果应该是：" 1 " > " 4 " > " 5 " > " 7 " > " 6 " > " 3 " > " 2 "

#### 总结

浏览器在执行代码的时候碰到同步任务会一步一步往下执行，碰到异步任务会放入到任务队列中等同步任务执行完以后再放入到主进程中执行；所以每一轮事件循环的执行顺序应该都是同步优先异步执行，异步又分为宏任务和微任务，当执行异步任务的时候微任务优先于宏任务，本轮的循环执行结束后才会去上一层进行往下执行；

### Promise开始

终于回到Promise了，那么首先什么是Promise？

`Promise` 是 `ES6` 提供的原生对象，用来处理异步操作

一个 Promise 对象表示一个异步操作的执行结果，包括状态（成功/失败）和值（成功返回值/错误原因）。一个 Promise 在创建的时候异步操作可能还没执行完成，通过持有这个 Promise 对象，可以在未来异步操作完成的时候对结果进行相应操作。

![](https://gcore.jsdelivr.net/gh/laoyerror/pic@main/5ALBgbaelwtNVWM.png)

这里有几个常用的名词，用来表示 Promise 对象当前的具体状态：

- `pending`: 初始状态，不是成功或失败状态。
- `fulfilled`: 意味着操作成功完成。
- `rejected`: 意味着操作失败。
- Settled 已敲定 / Resolved 已决议：“待定”状态的反面，都表示异步操作已经执行完成，即已兑现**或**已拒绝

#### Promise简单使用

通过 `new Promise` 来实例化，支持链式调用

```js
new Promise((resolve, reject)=>{
  // 逻辑
}).then(()=>{
  //当上面"逻辑"中调用 resolve() 时触发此方法
}).catch(()=>{
  //当上面"逻辑"中调用 reject() 时触发此方法
})
```

平时一直在代码中 .then() .catch() 地写来写去，用于异步请求的axios返回的也是是一个*Promise* 对象

```js
// 这是一个异步方法
function ajax(url){
  return new Promise(resolve=>{
    console.log('异步方法开始执行')
    setTimeout(()=>{
      console.log('异步方法执行完成')
      resolve(url+'的结果集')
    }, 1000)
  })
}
// 调用请求函数，并接受处理返回结果
ajax('/user/list').then((res)=>{
  console.log(res)
})

// 执行结果
// "异步方法开始执行"
// "异步方法执行完成"
// "/user/list的结果集"
```

Promise 构造函数需要接受一个函数作为参数，该函数的两个参数分别是 resolve 和 reject.

`resolve`函数的作用是，将 Promise 对象的状态从 未处理 变成 处理成功 (unresolved => resolved)， 在异步操作成功时调用，并将异步操作的结果作为参数传递出去。

`reject`函数的作用是，将 Promise 对象的状态从 未处理 变成 处理失败 (unresolved => rejected), 在异步操作失败时调用，并将异步操作报出的错误，作为参数传递出去。

**Promise 实例生成以后，可以用 then 方法和 catch 方法分别指定 resolved 状态和 rejected 状态的回调函数。**

**then()方法是异步执行**就是当.then()前的方法执行完后再执行then()内部的程序
这样就避免了，数据没获取到等的问题

`.then` 支持返回 `Promise` 对象进行链式调用

```js
ajax('/user/info').then((res)=>{
  // 用户信息查询成功后，可以根据返回结果查询后续信息
  console.log('用户信息:', res)
  return ajax('/user/score')
}).then((res)=>{
  console.log('用户成绩:', res)
  return ajax('/user/friends')
}).then((res)=>{
  console.log('用户朋友:', res)
})
```

`Promise`一旦创建就立即执行，并且无法中途取消，执行逻辑和顺序可以从下面的示例中获得

#### 结合事件循环分析下列代码

```js
console.log('1.开始创建并执行 Promise')
new Promise(function(resolve, reject) {
  console.log('2.由于创建会立即执行，所以会立即执行到本行')
  setTimeout(()=>{ // 模拟异步请求
    console.log('4. 1s之期已到，开始执行异步操作')
    if (true) {
        // 一般我们符合预期的结果时调用 resolve()，会在 .then 中继续执行
        resolve('成功')
    } else {
        // 不符合预期时调用 reject()，会在 .catch 中继续执行
        reject('不符合预期')
    }
  }, 1000)
}).then((res)=>{
  console.log('5.调用了then，接收数据：' + res)
}).catch((error)=>{
  console.log('5.调用了catch，错误信息：' + error)
})
console.log('3.本行为同步操作，所以先于 Promise 内的异步操作（setTimeout）')
```

#### 运行结果为

```js
// "1.开始创建并执行 Promise"
// "2.由于创建会立即执行，所以会立即执行到本行"
// "3.本行为同步操作，所以先于 Promise 内的异步操作（setTimeout）"
// "4.1s之期已到，开始执行异步操作"
// "5.调用了then，接收数据：成功"
```

### 回调函数

https://www.cnblogs.com/skuld-yi/p/14599594.html

如果完全不关心异步操作的执行结果，那就把它放在那自己执行就可以了；但通常情况下我们总是要对操作执行的结果进行后续处理的，例如更改页面上的数据显示、错误处理等。但由于异步操作不知道什么时候可以执行完成，就出现了**“回调函数”**的概念，意思就是等到异步操作处理结束了，再回过头来调用这个函数来对执行结果进行处理。

传统做法是，在执行异步操作的时候就把回调函数作为参数传进去，比如最常见的：

```js
setTimeout(function(){
    console.log("成功!");
}, 250);
```

`setTimeout()` 函数是最常见的异步函数之一，众所周知它的作用就是在指定时间后执行指定代码。仔细看就会发现，`setTimeout()` 函数接收两个参数，第二个参数是等待时间，而第一个参数就是**回调函数**，即等待指定的时间之后要回来调用这个函数。

很显然这种传参的做法有很多不方便的地方，比如把对结果的后续处理和异步操作本身耦合在了一起，以及著名的*回调地狱：*

```js
doSomething(function(result) {
  doSomethingElse(result, function(newResult) {
    doThirdThing(newResult, function(finalResult) {
      console.log('Got the final result: ' + finalResult);
    }, failureCallback);
  }, failureCallback);
}, failureCallback);
```

### Promise.then() 绑定回调函数

有了 Promise 之后，就能把回调和异步操作本身分开了。无论一个 Promise 对象当前是否已经执行完毕，我们都能在它上面绑定回调函数，并且保证回调函数被执行；这就是喜闻乐见的 `then()` 方法。

```js
p.then(onFulfilled[, onRejected]);
p.then(value => {
  // fulfillment
}, reason => {
  // rejection
});
```

then() 方法的语法很简单，有两个可选参数，分别代表当 Promise 的状态变为成功（fulfilled）和失败（rejected）时所使用的回调函数。

如果只想绑定 `onRejected`（即失败时的错误处理函数），下面两种写法完全等价，第二种是第一种的简写形式。

```js
p.then(null, failureCallback);
p.catch(failureCallback);
```

### 使用 Promise：链式调用

如果只是用 then 来绑定回调函数，那并不能解决回调地狱的问题。然而很妙的地方来了：Promise 支持**链式调用**：

```js
doSomething().then(function(result) {
  return doSomethingElse(result);
})
.then(function(newResult) {
  return doThirdThing(newResult);
})
.then(function(finalResult) {
  console.log('Got the final result: ' + finalResult);
})
.catch(failureCallback);
```

### 链式调用的实现

能做到链式调用的魔法来自 `then()` 方法：它会在执行相应的回调函数之后，**返回一个新的 Promise 对象，并且插入 Promise 链的当前位置**。

这里稍微有点绕，容易把回调函数等同于 then() 方法本身。实际上成功/失败的回调函数只是 then() 的参数而已；而实际执行 then() 的时候，它会先根据 promise 的状态调用相应的回调函数，再根据回调函数的执行结果生成一个新的 Promise 对象并返回；具体的对应规则如下：

| 回调函数执行情况                    | then() 返回的 Promise 对象           |
| ----------------------------------- | ------------------------------------ |
| 返回值 `return x;`                  | fulfilled 状态，参数为 x             |
| 直接返回 `return;` / 无 return 语句 | fulfilled 状态，参数为 undefined     |
| 抛出错误 `throw err;`               | rejected 状态，参数为 err            |
| 返回已决议的 Promise                | 状态和参数与返回的 Promise 一致      |
| 返回未定的 Promise                  | 未定的 Promise，回调参数与返回的相同 |

下面这个例子中，初始 Promise 的状态为已拒绝，然后第一个 then() 调用了绑定的 onRejected，返回了状态为 fulfilled 的新 Promise 对象，并传递给了链中的下一个 then()：

```js
Promise.reject()
  .then(() => 99, () => 42) // 调用 onRejected（return 42;），表格中的第一种情况
  .then(solution => console.log('Resolved with ' + solution)); // Resolved with 42
```

同时，你可能还记得 then() 的参数定义，两个回调函数都是可选的；如果没有传入对应的回调函数，then() 会直接把原 promise 的终态返回，不做额外处理。

### 错误处理

遇到异常抛出（被拒绝的 promise）时，浏览器会顺着 Promise 链寻找下一个 `onRejected` 回调函数（经常被简写为 `.catch()`），并跳过中间的 `onFulfilled` 回调函数。这种执行逻辑与同步代码中的 try-catch 执行过程非常相似：

```js
// 异步 Promise
doSomething()
.then(result => doSomethingElse(result))
.then(newResult => doThirdThing(newResult))
.then(finalResult => console.log(`Got the final result: ${finalResult}`))
.catch(failureCallback);

// 同步
try {
  let result = syncDoSomething();
  let newResult = syncDoSomethingElse(result);
  let finalResult = syncDoThirdThing(newResult);
  console.log(`Got the final result: ${finalResult}`);
} catch(error) {
  failureCallback(error);
}
```

一个具体的例子：

```js
Promise.resolve()
.then(() => {
    throw new Error('出错');
    console.log('a');
})
.then(() => {
    console.log('b');
})
.catch(() => {
    console.log('c');
})
.then(() => {
    console.log('d');
});

// 输出结果：
// "c"
// "d"
```

### 常见错误

```js
doSomething().then(function(result) {
  doSomethingElse(result) // 没有返回 Promise 以及没有必要的嵌套 Promise
  .then(newResult => doThirdThing(newResult));
}).then(() => doFourthThing());
// 最后，是没有使用 catch 终止 Promise 调用链，可能导致没有捕获的异常
```

上面这个例子在 Promise 中进行了嵌套，但没有将嵌套的 Promise 对象返回，因此`doFourthThing()` 不会等待 `doSomethingElse()` 或 `doThirdThing()` 完成，而是并行运行；并且如果有传入参数，接收到的会是 `undefined` 而不是 doThirdThing() 的执行结果。

正确的写法应该是：

注：箭头函数 `() => x` 是 `() => { return x; }` 的简写，即返回了新的 Promise 对象

```js
doSomething()
.then(function(result) {
  return doSomethingElse(result);
})
.then(newResult => doThirdThing(newResult))
.then(() => doFourthThing())
.catch(error => console.log(error));
```

### 创建 Promise 对象

如果要执行的异步操作没有返回 Promise 对象，可以用 new 和构造器创建自己的 promise。构造器的两个参数的作用是在异步操作成功/失败时，转换 Promise 对象的状态并传递对应参数。

```js
const myFirstPromise = new Promise((resolve, reject) => {
  // 做一些异步操作，最终会调用下面两者之一:
  // resolve(someValue); // fulfilled
  // reject("failure reason"); // rejected
});

// 一个例子
function myAsyncFunction(url) {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    xhr.open("GET", url);
    xhr.onload = () => resolve(xhr.responseText);
    xhr.onerror = () => reject(xhr.statusText);
    xhr.send();
  });
};
```

### Promise 其他静态方法

#### 创建已决议的 Promise 对象

`Promise.resolve(value)` 和 `Promise.reject(reason)` 方法分别返回一个已经成功/失败的 Promise 对象。

```js
const p1 = new Promise((resolve, reject) => {
  resolve();
});
const p2 = Promise.resolve();
```

如果 `resolve(value)` 的参数是带有 then() 方法的 Promise 对象，函数将返回其自带 then() 方法的执行结果；如果参数为空或是基本类型，返回的Promise对象与在 then() 方法中 return 对应值的结果一致，参见上文表格。基于这样的特性， `resolve(value)` 方法可以用于将不确定是否为 Promise 对象的 value 值统一为 Promise。

#### 多个 Promise 对象

```
Promise.all(iterable)
```
https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise/all
```js
const promise1 = Promise.resolve(3);
const promise2 = 42;
const promise3 = new Promise((resolve, reject) => {
  setTimeout(resolve, 100, 'foo');
});

Promise.all([promise1, promise2, promise3]).then((values) => {
  console.log(values);
});
// expected output: Array [3, 42, "foo"]
```

- 参数列表中的所有的 promises 都成功时，返回一个 fulfilled 的 Promise 对象，参数值是所有 promises 成功返回值的列表（顺序不变）
- 如果任何一个 promise 失败，立即返回一个 rejected 的 Promise 对象，参数是这个失败 promise 的错误信息

```js
Promise.all([func1(), func2(), func3()])
.then(([result1, result2, result3]) => { /* use result1, result2 and result3 */ });
```

`Promise.allSettled(iterable)`
列表中所有 promises 都已敲定后返回一个promise，并带有一个对象数组，对应每个promise 的结果。

`Promise.any(iterable)`
当列表中的任意一个 promise 成功时，立即返回这个 promise 的值。

`Promise.race(iterable)`
当列表中任意一个 promise 成功或失败时，立即返回该 promise 对象的执行结果。

一个综合例子（使用 setTimeout 模拟异步操作）：

```js
// 创造一个状态为 fulfilled，参数为"foo"的 Promise 对象
Promise.resolve("foo")
  .then(function(string) {	// string: "foo"
    // 返回状态为 fulfilled，参数为"foobar"的对象
    return new Promise(function(resolve, reject) {
      setTimeout(function() {
        string += 'bar';
        resolve(string);
      }, 1);
    });
  })
  .then(function(string) {	// string: "foobar"
    setTimeout(function() {
      string += 'baz';
      console.log(string);
    }, 1)
    // 返回值"foobar"
    return string;
  })
  .then(function(string) {	// string: "foobar"
    console.log("Last Then");
    console.log(string);
  });

// 输出结果：
// Last Then
// foobar
// foobarbaz（由第二个 then 中的 setTimeout 输出）
```
