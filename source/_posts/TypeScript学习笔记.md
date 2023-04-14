---
title: TypeScript学习笔记
tags:
  - TypeScript
  - 转载
index_img: >-
  https://cdn.staticaly.com/gh/yeyulingfeng01/hexo@main/assets/cover/2023/amazing-2451315_640.jpg
categories: Web前端
abbrlink: 4a9ed04d
date: 2023-04-04 09:39:00
---

TypeScript是由微软进行开发和维护的一种开源的编程语言，TypeScript是JavaScript的严格语法超集，提供了可选的静态类型检查，具有两个最重要的特性——类型系统、适用于任何规模

我们知道，JavaScript 是一门非常灵活的编程语言，这种灵活性就像一把双刃剑，一方面使得 JavaScript 蓬勃发展，无所不能，；另一方面也使得它的代码质量参差不齐，维护成本高，运行时错误多。

而 TypeScript 的类型系统，在很大程度上弥补了 JavaScript 的缺点。

## 项目文件结构

### tsconfig.json

```json
{
  "compilerOptions": {
    "lib": ["es2015"],
    "module": "commonjs",
    "outDir": "dist",
    "sourceMap": true,
    "strict": true,
    "target": "es2015"
  },
  "include": [
    "src"
  ]
}
```

放于项目根目录下。

- include: TSC 在哪些文件夹中寻找 TypeScript 文件
- lib: TSC 假定运行代码的环境中有哪些 API？（es2015, es2020, esnext 等等）编写在浏览器中运行的 TypeScript 需要加入 "dom"
- module: TSC 把代码编译成哪个模块系统（commonjs, amd 等等）
- outDir: 生成的 JavaScript 放置的目录
- strict: TypeScript 严格模式（true, false）
- target: TSC 把代码编译成哪个 JavaScript 版本（es2015, es2020, esnext 等等）

### tslint.json

```json
{
  "defaultSeverity": "error",
  "extends": [
    "tslint:recommended"
  ],
  "jsRules": {},
  "rules": {
    "indent": [true, "spaces", 2],
    "semicolon": false,
    "trailing-comma": false
  },
  "rulesDirectory": []
}
```

文件生成命令：`./node_modules/.bin/tslint --init`

可定制代码风格约定：tab 还是 space、用不用分号等。详见 https://palantir.github.io/tslint/rules

## 类型

TypeScript 使用类型注解来限制变量的类型，注解的方式是在其背后添加冒号和具体的类型，例如：`let n: number = 1` 限制 `n` 的类型为 `number`，`function fullname(p: Person): string` 限制参数 `p` 的类型为 `Person`，返回值类型为 `string`。

### 类型推断

在没有显式声明变量类型时，TypeScript 会自动进行类型推断：

```typescript
let m = 12
let s = 'a string'
```

如果用的是 VSCode 编辑器，将鼠标悬停在 `m` 和 `s` 上方可以看到它们的类型分别是 `number` 和 `string`。

但如果使用 `const` 而非 `let` 来声明变量，会看到 `m` 的类型变成了 `12`，`s` 的类型变成了 `"a string"`。

```typescript
const m = 12
const s = 'a string'
```

像这样的类型叫作字面量类型，完整的写法如下：

```typescript
const m: 12 = 12
const s: 'a string' = 'a string'
```

### any

`any` 类型的对象可以调用任意方法，访问任意属性，类型检查不起任何作用，就像一个常规的 JavaScript 对象。能不使用 `any` 的情况下，应该尽量避免使用。

在使用 `any` 类型时，必须显式注解，不能用类型推导。如果 TypeScript 推导出某个对象类型为 `any` 则会抛出运行时异常。

### unknown

`unknown` 与 `any` 的主要区别是，你不能对 `unknown` 对象进行任何方法调用和属性访问，除非在上下文中显式地使用 `typeof` 或者 `instanceof` 对其进行过类型判断。但你可以直接对其使用 `==`, `===`, `||`, `&&`, `?`, `!`.

### object

在 TypeScript 中显式地将一个对象声明为 `object` 类型时，无法调用其任何方法，也无法访问其任何属性。

```typescript
let o: object = {a: 1}
console.log(o.a) // 编译时出错
```

但如果不显式声明类型：

```typescript
let o = {a: 1}
console.log(o.a)
```

这样是可以正常编译和执行的。TypeScript 为自动推断 `o` 的类型为 `{a: number}`，这种类型被称作**对象字面量类型**。完整的写法如下：

```typescript
let o: {a: number} = {a: 1}
console.log(o.a)
```

而将变量声明为 `object` 只表示该变量的类型是非原始类型，除此之外没有更具体的类型信息。

声明为 `Object`（首字母 O 大写）也类似。区别在于后者可以表示原始的封装类型，前者不行：

```typescript
let o: object = 'a string' // 编译出错
let o: Object = 'a string' // 能正常执行
```

但请尽量避免使用 `Object` 类型。

### 对象字面量类型

前面的示例代码中使用了对象字面量类型：

```typescript
let o: {a: number} = {a: 1}
console.log(o.a)
```

这种类型告诉 TypeScript 编译器 `o` 这个对象的结构是含有 `a` 属性的一个对象，并且 `a` 属性的类型为 `number`。实际上，如果假设有一个类型 `C`，它的内部也有一个名为 `a` 的 `number` 属性，这时是可以将 `C` 的实例赋值给上面那个 `o` 的。

如果给对象字面量类型的变量赋值时，少了某个属性，或者多了某个属性，则会无法编译：

```typescript
let o1: {a: number, b: string} = {a: 1}
let o2: {a: number} = {a: 1, b: 2}
```

但多了属性的情况下，如果不以对象字面量的形式直接赋值，而是通过一个中间变量进行赋值，则可以正常运行：

```typescript
let x = {a: 1, b: 2}
let o: {a: number} = x
```

对象字面量类型中有一个特别的类型 `{}`，作用与 `Object` 类似，也尽量不要使用。

### 可选属性

如果希望某个属性是可选的，可以使用**可选属性**：

```typescript
let o: {a: number, b?: string} = {a: 1}
o = {a: 1, b: 'a string'}
```

像这样在 `b` 属性的后面加一个问号，就表示这个 `b` 属性是可选的。

### 索引签名

如果希望拥有不确定个数的某个类型属性，可以使用**索引签名**：

```typescript
let o: {a: number, [key: number]: boolean} = {a: 1}
o = {a: 1, 0: true, 1: false, 2: false}
```

`[key: number]: boolean` 表示该类型含有 0 个或多个属性名为 `number` 类型，属性值为 `boolean` 类型的属性。注意这里的 `key` 可以是任何合法的变量，不一定非得用 `key`，用 `index` 或者 `asdf` 都可以。并且它的类型必须是 `number` 或者 `string`，因为 JavaScript 对象通过 `[]` 取属性时只能传入数字或者字符串。

### 只读属性

使用 `readonly` 可以限定某个属性为只读属性：

```typescript
let o: {readonly a: number} = {a: 1}
o.a = 2 // 无法修改
```

### 类型别名

可以使用 `type` 给类型起一个别名，之后就可以拿这个别名来用作类型声明。使用类型别名的地方都可以替换成源类型：

```typescript
type Age = number
let age: Age = 10
let n: number = 20
age = number
```

由于 TypeScript 不会自行推断某个对象为类型别名，因此在需要使用的时候必须显式声明。

在同一作用域当中，类型别名无法重复赋值。与 `const` 和 `let` 类似，`type` 声明的类型别名具有块级作用域，块内部的声明将覆盖外部的声明。

### 交叉类型和联合类型

将多个类型用 `&` 拼接起来，就是交叉类型（Intersection Types），声明为该类型的对象必须拥有所有类型的属性：

```typescript
type Cat = {name: string, purrs: boolean}
type Dog = {name: string, barks: boolean, wags: boolean}

type CatAndDog = Cat & Dog
let wat: CatAndDog = {name: 'wat', purrs: true, barks: true, wags: true}
let wrongWat: CatAndDog = {name: 'wat', purrs: true, wags: true} // 编译错误，缺少 barks 属性
```

将多类类型用 `|` 连接起来，就是联合类型（Union Types），声明为该类型的对象必须是这些类型中的其中一种或多种：

```typescript
// ...
type CatOrDogOrBoth = Cat | Dog

let wat: CatOrDogOrBoth = {name: 'wat', purrs: true}
let watWithoutName: CatOrDogOrBoth = {purrs: true} // 编译错误，缺少 name 属性
let watWithoutName: CatOrDogOrBoth = {name: 'wat', purrs: true, wags: true}
```

在一些情况下，你可能需要限制某个函数的返回值只能在某几个固定值当中取值，例如：

```typescript
function rollDie(): 1 | 2 | 3 | 4 | 5 | 6 {
  // ...
}
```

这个函数返回值只能是 1, 2, 3, 4, 5, 6 其中的一个。这种类型叫数字字面量类型。当然，还有字符串字面量类型，布尔字面量类型等。

### 数组中的联合类型

TypeScript 可以对 `let a = [1, 2, 3]` 里的 `a` 推导出类型为 `number[]`。`let a = [1, 's']` 的类型则会被推导为 `(number|string)[]`。下面是稍微复杂点的情况：

```typescript
function buildArray() {
  let a = []
  a.push(1)
  a.push('s')
  return a
}

let array = buildArray()
array.push(true) // 无法编译，(number|string)[] 类型中无法插入 boolean 值
```

这种情况下，`buildArray()` 内部的 `a` 初始化时被推断为 `any[]` 类型，函数返回时类型确定，因此 `buildArray()` 的返回值被推断为 `(number|string)[]` 类型，所以外面的那个 `array` 无法再存入 `boolean` 值。

### 元组（Tuple）

元组是数组的子类型，区别在于元组的长度是固定的，并且各索引位置上值的类型是已知的。由于元组字面量与数组字面量没有区别，因此在使用元组时必须显式声明其类型，否则会被自动推断成数组。

```typescript
let tuple: [string, number, boolean] = ['a string', 1, false]
```

元组也支持可选元素和剩余元素：

```typescript
let tuple: [string, number, boolean?] = ['a string', 1]
let tuple2: [string, number, ...boolean[]] = ['a string', 1, true, false]
```

### `null`, `undefined`, `void` 和 `never`

`null` 表示空值，`undefined` 表示未定义，`void` 表示函数没有返回值，`never` 表示函数根本不返回（抛异常或者死循环）。

### 枚举

```typesctipt
enum Language {
  Chinese,
  English,
  Russian
}
```

默认情况下枚举值为从 0 开始的数字，你也可以指定具体的值。枚举值可以是数字或字符串：

```typesctipt
enum Language {
  Chinese = 'Chinese',
  English = 2
}
```

枚举值可以通过名字访问，也可以通过下标访问。不过通过下标访问容易出问题（访问不存在的位置），可以使用 `const` 来修饰枚举，这样 TypeScript 会强制你使用名字来访问枚举值。

`const enum` 生成的 JavaScript 代码中不会含有对应定义枚举的代码，而是直接将枚举值内插到使用它的地方，例如直接将 `Language.English` 替换成 2。

## 函数

一个完整的函数定义是这样的：

```typescript
function add(a: number, b: number): number {
  return a + b
}
```

多数情况下，参数的类型无法推断（除非是默认参数），因此需要声明类型。而返回值类型多数时候是可以推断出来的，因此可以省去类型声明。

### 可选参数

函数的参数同样可以用 `?` 标记为可选参数。如果参数中同时包含可选参数和必要的参数，可选参数必须放在必选参数的后面。

```typescript
function log(message: string, userId?: string) {
  let time = new Date().toLocaleTimeString()
  console.log(time, message, userId || 'Not signed in')
}
```

### 默认参数

默认参数与 JavaScript 一样，不赘述。

### 剩余参数

参数名前面加上 `...` 会使该参数变成一个数组，其中存放着参数列表中未定义的所有剩余参数。一个函数中只能有一个剩余参数，而且必须是参数列表中的最后一个参数。

```typescript
function log(message: string, ...additionalInfo: string[]) {
  let time = new Date().toLocaleTimeString()
  console.log(time, message, additionalInfo.join(', '))
}

log('Hello', 'and', 'goodbye')
```

### this 参数

和 JavaScript 一样，TypeScript 的函数在调用时，内部的 `this` 有可能指向不同的对象。如果你想限制函数内部的 `this` 指向对象的类型，可以使用 this 参数：

```typescript
function fancyDate(this: Date) {
  return `${this.getFullYear()}-${this.getMonth() + 1}-${this.getDate()}`
}

console.log(fancyDate.bind(new Date)())
console.log(fancyDate.bind("2022-02-02")()) // 无法编译
```

this 参数必须放在参数列表的最前面，并且实际上它并不占用参数列表的位置，是个“假参数”。

### 生成器函数

函数声明时，在 `function` 后面加一个星号，该函数就成为了一个生成器。

```typescript
function* createFibonacciGenerator() {
  let a = 0
  let b = 1
  while(true) { // 无限循环
    yield a; // 使用 yield 产出值，generator.next() 的值从这里来。注意分号。
    [a, b] = [b, a + b]
  }
}

let generator = createFibonacciGenerator()

console.log(generator.next()) //{ value: 0, done: false }
console.log(generator.next()) //{ value: 1, done: false }
console.log(generator.next()) //{ value: 1, done: false }
console.log(generator.next()) //{ value: 2, done: false }
console.log(generator.next()) //{ value: 3, done: false }
console.log(generator.next()) //{ value: 5, done: false }
```

生成器函数是惰性的，只在调用 `.next()` 时执行一次，然后就停止执行，直到下一次调用 `.next()`，因此上面的死循环并不会让程序卡死。这里执行的结果中，`done` 一直是 `false`。如果生成器内部不是无限循环，我们就有机会看到 `{done: true}` 的结果。

调用 `createFibonacciGenerator()` 得到的是一个 `IterableIterator<number>` 迭代器。

### 迭代器

迭代器是一个实现了 [Iterator protocol](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterator_protocol) 的任意对象。该协议要求对象里含有一个 `next()` 方法，该方法返回一个带有 `value` 和 `done` 属性的对象，正如上面的 `generator`。生成器本身同时也是一个迭代器。而迭代器可以有很多种。

### 可迭代对象

可迭代对象拥有 `Symbol.iterator` 属性，并且该属性是一个函数，其中也使用 `yield` 产出值。对可迭代对象，可以使用 `for...of` 来迭代。

```typescript
let numbers = {
  *[Symbol.iterator]() {
    for(let n = 1; n <= 10; n++) {
      yield n
    }
  }
}

for(let n of numbers) {
  console.log(n)
}
```

JavaScript 内置的常用集合类型（Array, Map, Set, String）都是可迭代对象。可迭代对象除了可以用 `for...of` 来操作，还可以像一个数组一样用 `...` 展开，也可以像数组一样被解构。

```typescript
let [a, b] = "a string"
```

### 函数签名

```typescript
function sum(a: number, b: number) {
  return a + b
}
```

以上函数的签名是 `(x: number, y: number) => number`。可以换一种写法：

```typescript
type Sum = (x: number, y: number) => number

let sum: Sum = (a, b) => {
  return a + b
}
```

这种写法相当于定义了一个函数接口 `Sum`，实现该接口的函数（例如这里的 `sum()`）都必须有两个 `number` 类型的参数，并且返回一个 `number` 类型的值。

因为 `Sum` 已经定义了参数的类型，`sum()` 又被指定为了 `Sum` 类型。因此 `sum()` 在实现时，参数不再需要指定类型，类型信息可以直接从 `Sum` 的定义中推断得出。

需要注意的是，带有默认值参数的函数，和带有可选参数的函数的签名是一样的：

```typescript
type Log = (message: string, userId?: string) => void

let log: Log = (message, userId = 'Not signed in') => {
  let time = new Date().toISOString()
  console.log(time, message, userId)
}
```

实际上，函数签名的完整写法如下：

```typescript
// type Log = (message: string, userId?: string) => void
type Log = {
  (message: string, userId?: string): void // 这里需要把箭头改为冒号
}
```

实际上这里的 `type Log =` 可以替换成 `interface Log`，所以前文一直在使用“接口”、“实现”这样的概念。

### 函数重载

下面是一个预订机票的函数：

```typescript
type Reserve = (from: Date, to: Date, destination: string) => void

let reserve: Reserve = (from, to, destination) => {
  // ...
}
```

实际上，函数签名

假如要加入单程旅行的支持，则需要提供一个省去 `to` 参数的函数：

```typescript
type Reserve = {
  (from: Date, to: Date, destination: string): void
  (from: Date, destination: string): void
}

let reserve: Reserve = (from, to, destination) => { // 编译出错
  // ...
}
```

这里编译会出错，因为声明为 `Reserve` 的 `reserve` 并没有实现所有的函数签名，此时，需要将代码修改为：

```typescript
let reserve: Reserve = (from, toOrDestination: Date | string, destination?: string) => {
  // 代码中需要根据参数的不同情况来执行不同的逻辑
}
```

以上的写法是通过接口来定义重载，还有另一种方式，是用函数声明来定义重载：

```typescript
function reserve(from: Date, to: Date, destination: string): void
function reserve(from: Date, destination: string): void
function reserve(from: Date, toOrDestination: Date | string, destination?: string) {
  // ...
}
```

这只是两种不同的写法。

## 泛型

// 略

## 类

### 访问修饰符

TypeScript 中，类的成员默认都是 public 的。

TypeScript 使用的是结构性类型系统。 比较两种不同的类型时，TypeScript 并不在乎它们具体的类型是什么，只要所有成员的类型都是兼容的，就认为它们的类型是兼容的。

```typescript
class Zebra {
  trot() { }
}

class Poodle {
  trot() { }
}

function ambleAround(animal: Zebra) {
  animal.trot()
}
```

以上两个类的实例都可以传入 `ambleAround()` 函数，在 TypeScript 中不会有任何问题。

但是在类中含有 private 或 protected 成员时，结果就不一样了。当两个类只是拥有同样的 private/protected 成员时，这两个类型不一定是兼容的，只有当它们的 private/protected 成员来自同一处声明时，这两个类型才能算兼容。下面的代码展示了这一点：

```typescript
class Animal {
  private name: string
  constructor(theName: string) { this.name = theName }
}

class Rhino extends Animal {
  constructor() { super("Rhino") }
}

class Employee {
  private name: string
  constructor(theName: string) { this.name = theName }
}

let animal = new Animal("Goat")
let rhino = new Rhino()
let employee = new Employee("Bob")

function printName(o: Animal) {
  // ...
}

printName(employee) // 错误: Animal 与 Employee 不兼容.
```

把代码中的 `private` 修饰符去掉，这段代码才不会报错。

TypeScript 的 protected 表现与 Java 相同：在子类中可以访问。

### 参数属性

TypeScript 可以在构造函数的参数中直接声明成员变量，这叫作“参数属性”。下面两段代码是等价的：

```typescript
class Octopus {
  name: string;
  constructor (theName: string) {
    this.name = theName;
  }
}

class Octopus {
  constructor (public name: string) {
    this.name = theName;
  }
}
```

参数属性通过给构造函数参数前面添加一个访问限定符来声明，可以是 public, protected, private, readonly。

### super

在子类中调用父类的构造函数，用 `super()`。在子类中调用父类的其它方法，用 `super.method()`。`super` 不能访问父类的属性。

### 返回 `this`

类似下面这种情况，子类和父类中两个方法逻辑一样，只是返回的类型不同，可以用返回 `this` 的方式消除重复：

```typescript
class Set {
  add(value: number): Set {
    // ...
    return this
  }
}

class MutableSet extends Set {
  add(value: number): MutableSet {
    // ...
    return this
  }
}
```

让 `add()` 返回 `this` 之后：

```typescript
class Set {
  add(value: number): this {
    // ...
    return this
  }
}

class MutableSet extends Set {}
```

### 其它

TypeScript 类支持静态属性：

```typescript
class Grid {
  static origin = {x: 0, y: 0}
}
```

也支持抽象类：

```typescript
abstract class Animal {
  abstract makeSound(): void
  move(): void {
    console.log('roaming the earch...')
  }
}
```

## 接口

实际上前面已经一直在用接口了：

```typescript
type Cat = {name: string, purrs: boolean}
// 相当于
interface Cat {
  name: string
  purrs: boolean
}
```

两者用起几乎一样，但有些细微差别。比如在同一作用域中用 `interface` 声明的多个接口会自动合并。但用 `type` 声明多个同名接口（类型）会出错：

```typescript
interface I {
  prop1: string
}

interface I {
  prop2: boolean
}

// 相当于：

interface I {
  prop1: string
  prop2: boolean
}
```

接口的属性可以是可选的：

```typescript
interface SquareConfig {
  color?: string
  width?: number
}
```

也可以是只读的：

```typescript
interface Point {
  readonly x: number
  readonly y: number
}
```

TypeScript 提供了一个 `ReadonlyArray<T>` 类型，是 `Array<T>` 去掉了写操作的只读版本，可以确保数组创建之后不能被修改。

```typescript
let a: number[] = [1, 2, 3, 4]
const ro: ReadonlyArray<number> = a
ro[0] = 12 // error!
ro.push(5) // error!
ro.length = 100 // error!
a = ro // error! 但可以强制转换： `a = ro as number[]`
```

接口也可以定义函数类型：

```typescript
interface SearchFunc {
  (source: string, subString: string): boolean
}

const mySearch: SearchFunc = function(a: string, b: string) {
  const result = a.search(b)
  return result > -1
}
```

注：上例中，实现了 `SearchFunc` 接口的 `mySearch()` 函数，参数中的 `string` 类型声明其实可以省去。

接口还能用来定义“可索引类型”：

```typescript
interface StringArray {
  [i: number]: string
}

const myArray: StringArray = ["Bob", "Fred"]

const myStr: string = myArray[0]
```

这个 `StringArray` 接口要求其实现必须可以通过 `[n]` 的方式读取数据，其中 `n` 为 `number` 类型，并且返回一个 `string`。

TypeScript 支持两种索引签名：字符串和数字。 可以同时使用两种类型的索引。但是数字索引的返回值必须与字符串索引返回值的类型相同，或者是其子类型。 这是因为当使用 `number` 来索引时，JavaScript 会将它转换成 `string` 然后再去索引对象。

另外，因为 JavaScript 中，所有对象的属性都可以通过 `[key]` 的方式获得，因此，在接口中定义了字符串索引签名后，该接口的所有其它属性返回值都必须与之相同：

```typescript
interface NumberDictionary {
  [index: string]: number
  length: number    // 可以，length是number类型
  name: string       // 错误，`name`的类型与索引类型返回值的类型不匹配
}
```

索引签名可以设置为只读：

```typescript
interface ReadonlyStringArray {
  readonly [index: number]: string
}
```

与 Java 一样，TypeScript 的接口里也可以定义一些方法：

```typescript
interface ClockInterface {
  currentTime: Date
  setTime(d: Date): void
}
```

可以用带 `new()` 签名的接口来限制某个类的构造函数：

```typescript
interface ClockConstructor {
  new(hour: number, minute: number): ClockInterface
}

interface ClockInterface {
  tick(): void
}

function createClock(ctor: ClockConstructor, hour: number, minute: number): ClockInterface {
  return new ctor(hour, minute)
}

class DigitalClock implements ClockInterface {
  constructor(h: number, m: number) { }
  tick() {
    console.log("beep beep")
  }
}
class AnalogClock implements ClockInterface {
  constructor(h: number, m: number) { }
  tick() {
    console.log("tick tock")
  }
}

let digital = createClock(DigitalClock, 12, 17)
let analog = createClock(AnalogClock, 7, 32)
```

与 Java 一样，TypeScritp 中的一个接口也可以继承多个其它接口。

JavaScript 中，一个 function 同时是一个对象，可以拥有其它属性，下面是一个 TypeScript 中的混合类型的示例：

```typescript
interface Counter {
  (start: number): string
  interval: number
  reset(): void
}

function getCounter(): Counter {
  let counter = function (start: number) { } as Counter
  counter.interval = 123
  counter.reset = function () { }
  return counter
}

let c = getCounter()
c(10)
c.reset()
c.interval = 5.0
```

TypeScript 中的接口可以继承类。当一个接口继承一个类时，该类中所有成员都会被继承，包括 protected 和 private 成员，但是没有具体的实现。这意味着该接口只能被这个类或它的子类实现。

转载于：[https://geeknote.net/yuan/posts/2274](https://geeknote.net/yuan/posts/2274)