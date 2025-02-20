---
author: Huang Chao
pubDatetime: 2023-03-23T11:18:53Z
modDatetime: 2023-03-24T01:22:56.066Z
title: "关于 JS 那些事"
featured: true
draft: false
tags:
  - echarts
description: "JS碎片化"
---

## 有关 null 和 undefined 的区别

- null表示 一个空指针，它不指向任何对象，是我们主动赋予变量的一个值
- undefined 则表示你声明了一个变量，但是并没有赋值操作，或者说函数没有返回值的操作，都会出现undefined的情况

## 谈谈闭包

### 闭包环境出现的条件

```js
function x() {
  let count = 0;

  function y() {
    return (count += 1);
  }

  return y;
}

let fun = x();
console.log("count", fun()); // 1
console.log("count", fun()); // 2
```

它是由多个函数嵌套引用，并且多个函数作用域内针对某个外部变量进行内部引用，并且只要这个函数不销毁的情况下，这个闭包中的变量将会一直存在

### 闭包的应用

- 函数柯里化
- 实现函数模块化
  - `count` 是一个创建于 `fun` 函数作用域内的变量
  - 通过在内部处理 `count` 的读取和设置，外部无法直接调用到 count
  - 通过暴露公共方法去供外部读取

```js
function fun() {
  let count = 0;
  return {
    get() {
      return count; // 私有变量，外部无法访问
    },
    set() {
      count += 1;
    },
  };
}

let newFun = fun();
newFun.set();
console.log("result", newFun.get());
```

### 注意事项

> 如果闭包长期存在，会容易导致内存泄露，外部变量会被永远携带，可以通过赋值 null 的方式 释放闭包

## JS 中的异步编程

> JS 本身是单线程执行的，相当于每次执行一个 `task` 都是一个个执行，为了处理异步操作，JS 引入了事件循环（event loop）

### 关于事件循环 event loop

事件循环是不断的从新的任务中取出任务并执行。并且任务队列分为宏任务队列和微任务队列

> 微任务队列优先级高于宏任务队列

场景设定🍔

想象你进入一个快餐店（浏览器），里面只有一个厨师（JS主线程），但是顾客（task）却很多。这个时候 JS 引入了一套高效的排队系统

1. 同步任务

任务一个一个执行，执行完一个执行下一个

```js
console.log("点汉堡");
console.log("点薯条");
```

2. 异步任务

宏任务：

开设一个新的窗口（外卖窗口）通过 `WebAPI` 进行计时

```js
setTimeout(() => {
  console.log("外卖");
}, 0);
```

- 厨师还是会优先处理同步任务
- 同步任务全部处理完成，处理外卖任务
- 外卖任务完成，添加到外卖队列

微任务：

开设一个订单加急窗口，处理加急订单（任务）

```js
Promise.resolve().then(() => {
  console.log("加急订单");
});
```

- 把完成的订单（任务）放进加急订单中
- 当厨师把手头任务（同步）全部完成，再处理加急订单，哪怕外卖窗口有其他订单等待！

执行流程：

- 厨师会优先处理同步任务中的所有任务
- 检查微任务队列中是否有新的任务，等同步处理完优先处理
- 检查宏任务队列中是否有新的任务，如果有，只处理一个
- 循环往复，直到队列任务全部清空

## JS 如何构造一个一百个全为 0 的数组

- 通过 Array.fill
  > 用于填充一个固定值到数组中，从起始索引到终止索引

```js
new Array(100).fill(0);
```

## ES6中必须要知道的？

### let、const、var 区别？

- var 的变量提升

首先 这三个都是可以去定义一个任意类型的变量
`var` 其实是 Javascript 的一个 `bug`，在定义变量时，可以在不初始化赋值的情况下直接使用并且浏览器只会警告可能存在 undefined
情况，不会直接告知开发者并且抛出异常
这就是 `变量提升`

```js
console.log(a); // undefined
var a = 10;
```

- var 存在 `函数作用域` 或者 `全局作用域`

`if` 块外部 仍然可以访问 x 变量，显然不妥。

```js
function fun() {
  if (true) {
    var x = 10;
  }
  console.log(x); // 10
}

fun();
```

- `let`，`const` 它们都具有 `块级作用域`。

一个是作为变量存储、一个是作为常量存储，他们的出现就是为了解决 `var` 带来 bug

所谓变量 就是在进行初始化赋值后，可以变化修改的值

所谓常量 就是一旦完成初始化赋值后，无法轻易的修改值（但存在特殊情况）

```js
function fun() {
  if (true) {
    const x = 10;
    let y = 20;
  }
  console.log(x); // 报错：x is not defined
  console.log(y); // 报错：y is not defined
}

fun();
```

### 箭头函数懂吗？

1. 特性

箭头函数无需通过 `function` 去定义一个函数，通过括号 + 表达式的形式去定义
语法是：当参数只有一个时，可以省略括号，当表达式也是单个时可以隐式的返回

即：

```js
const fun = (a, b) => {
  a + b;
};
```

2. 不存在 `this` 指向的问题

- 箭头函数自动继承外层作用域（静态绑定）

这使得箭头函数在回调函数比如（setTimeout 或者 react事件处理）中非常有用

```js
function timer() {
  this.seconds = 0;
  //传统函数下：this指向的是调用者（可能出现this丢失）
  setTimeout(function a() {
    this.seconds++;
  }, 1000);
  //箭头函数下：this直接继承外部函数的实例
  setTimeout(() => {
    this.seconds++;
  }, 1000);
}
```

- 避免了在 `setTimeout`、事件处理函数、或者数组方法中 this 的指向问题

```js
// 普通函数需要额外通过 bind 绑定this
button
  .addListener("click", function () {
    this.classList.toggle("active");
  })
  .bind(this);

//箭头函数 自动继承外层函数示例
button.addListener("click", () => {
  this.classList.toggle("active");
});
```

- 没有原型属性（prototype），所以无法作为对象的自身属性去访问

```js
const obj = {
  a: 1,
  b: () => this.a, //错误。，此时 this 指向全局对象
};
```

- 箭头函数符合函数式编程的风格，提高代码可读性，维护性

简化像 `map`、`filter`、`reduce`等针对数组的一些操作

```js
const nums = [1, 2, 3];
const squera = nums.map(x => x * x);
```

### JS的防抖和节流？

防抖：debounce(f, wait)

> 避免频繁的针对某一事件流进行多次频繁的触发，也就是只设置在单位时间内只有效触发最后一次

- 应用场景：
  - 用户登录，频繁的点击登录按钮
  - 用户发送短信，发送太多的请求
  - 调整浏览器窗口会造成 resize 频繁计算过多
  - 文本编辑器的实时保存，只保存有效的最后一次触发
- 防抖核心
  - 在于 `clearTimeout(timer)` 的清零

举例说明：

坐电梯时，比如按下关门按钮 到 关闭完成 这一时间为 `3s`，如果在这 `3s` 内外面还有人按开门按钮，那此次的关门按钮不作为触发的执行事件，并且重新开始计时。
以此类推，只要单位时间内一人有开门，都不暂时作为最后一次事件执行。只有门真正关闭，那么最后一次的事件才会作为执行事件触发。

节流：throttle(f, wait)

> 也是控制事件发生的频率，比如控制 1s 发生一次，甚至是 1分钟 发生一次

- 应用场景
  - scroll 滚动事件，每隔 1s 就重新记录一次位置信息
  - 浏览器的播放事件，每隔 1s 就重新记录播放的一个进度信息
  - input 搜索框，发送请求下拉列表
- 节流核心
  - 在于 加锁和重置锁 `timer=setTimeout`、`timer = null`

举例说明：节流可以比作等红绿灯，当绿灯触发到开启红灯这段事件，可以一次通过一批车辆

### JS 类数组转换数组？

什么是类数组？

> 一个对象有 length 属性，并且可以通过索引访问元素，但是不具备纯数组的方法

- `Array.from`

最直接的方法就是通过 `Array.from` 的方式进行转换

```js
const objToArray = {
  0: "apple",
  1: "orange",
  length: 2,
};
const arr = Array.from(objToArray);
// ['apple', 'orange']
```

- `Array.prototype.slice.call`

通过数组原型 `Array.prototype.slice.call`

```js
const objToArray = {
  0: "red",
  1: "blue",
  length: 2,
};
const arr = Array.prototype.slice.call(objToArray);
// ['red', 'blue']
```

### 原型和原型链？

> 在 JS 中万物皆可以是对象，那么函数也是对象，原型也是对象，所有对象都存在 _proto_ 属性，而函数拥有特殊的 prototype 属性

原型链的本质：

本质其实就是通过 `_proto_` 属性像梯子一样逐级的向上查找，直到找到属性或者 `null`（最顶层的原型，原型链终点）

原型和原型链之间就行 `DNA模型` 构造函数的 `prototype` 属性就像基因库，`_proto_` 就像继承基因

在 JS `OOP` 面向对象编程思想中，本质就是通过原型委托机制，它不是复制各对象的属性，而是通过链条的形式建立各对象直接的引用连接。

应用场景：

- 可以实现方法继承，通过 类 和实例对象的继承关系，访问其内部被公开的方法

### 浅拷贝 和 深拷贝？

首先浅拷贝和深拷贝都是针对对象进行的复制，区别在复制的层级：

|     特性     |          浅拷贝          |              深拷贝              |
| :----------: | :----------------------: | :------------------------------: |
|   复制层级   |     仅复制第一层属性     |      递归复制所有嵌套的属性      |
| 引用类型处理 | 复制一份引用地址进行共享 | 创建出一个全新对象开辟一个新空间 |
|   修改影响   | 修改嵌套对象会影响原属性 |    修改任何对象不会影响原属性    |

#### 哪些方法可以实现 浅拷贝

- 直接赋值（不是拷贝，只是引用的传递）

```js
const obj = { a: 1, nested: { b: 2 } };
const _obj = obj; //只是引用的传递
_obj.a = 999;
console.log(obj.a); // 999 原对象被修改
```

- Object.assign()

```js
const obj = { a: 1, nested: { b: 2 } };
const _obj = Object.assign({}, obj);
_obj.nested.b = 999;
console.log(obj.nested.b); // 999 浅拷贝陷阱
```

- 展开运算符

```js
const obj = { a: 1, nested: { b: 2 } };
const _obj = { ...obj };
_obj.nested.b = 666;
console.log(_obj.nested.b); // 666 同样影响原对象
```

#### 哪些方法可以实现 深拷贝

- JSON.parse(JSON.stringify()) 最快捷方式

```js
const obj = { a: 1, nested: { b: 2 } };
const deepCopy = { ...obj };
deepCopy.nested.b = 666;
console.log(deepCopy.nested.b); // 保持原值 -> 成功深拷贝
```

但是这种方法存在局限性：

- 丢失 undefined、function等类型
- 日期对象会被转换为字符串

使用第三方库 Lodash的 \_.cloneDeep 方法

手动实现一个深拷贝

```js
function cloneDeep(source: any, map = new WeakMap()) {
    //基础的类型直接返回
    if (typeof source !== 'object' || source === null) return source;
    //处理循环引用，避免无效递归
    if (map.has(source)) return map.get(source);
    //动态创建副本，如果是数组 -> []
    const target = Array.isArray(source) ? [] : {};
    map.set(source, target)
    //开始递归拷贝属性
    for (const key in source) {
        //根据key拷贝
        if (source.hasOwnProperty(key)) {
            //递归引用赋值
            target[key] = cloneDeep(source[key], map);
        }
    }
    //返回递归完成后的新对象
    return target;
}
```

Q1：为什么用WeakMap 而不是 Map

> WeakMap是弱引用类型，能更好的处理GC机制，避免内存泄露

> 更适合临时存储，深拷贝完成后，能自动清理临时数据

### 你知道的 Promise 和 async/await ？

1. `Promise` 是 Javascript 中处理 `异步` 操作的一个 `对象`

2. 他有三种状态：

- `pending` -> 初始化（未完成也未结束）状态
- `fulfilled` -> 操作完成
- `rejected` -> 操作失败

3. 链式调用：通过 `then()`、`catch()`、`finally()` 处理结果，或者捕获异常，避免 `回调地狱`

因为 `Promise` 实际上是一个函数对象，那么既然是函数对象，就存在自己的 `prototype` 属性
而 then、catch、finally 都属于 prototype 的方法

```js
const fetchData = () => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      const num = Math.random();
      num > 0.5 ? resolve("success") : reject("fail");
    }, 1000);
  });
};
fetchData()
  .then(data => console.log(data))
  .catch(error => console.log(error));
```

#### Q1：能详细说说 链式调用吗？

> 根据上文所述，.then() 方法可以接收两个参数，一个是 Promise 成功兑现的回调函数，一个是 Promise 被拒绝的回调函数。
> 但是 .then() 方法被执行后又会返回一个新生成的 Promise 对象，同样可以继续调用 .then 方法。这种现象就像 "俄罗斯" 套娃。
> 但是每个 .then() 方法的处理对链式调用中的下游又非常重要，所以 .catch() 方法就是处理用于针对未被兑现的 Promise，并且这个
> .catch() 方法最后式放在链式调用的最后去处理以保证整个链式的完整性

#### Q2：谈谈如何通过 Promise 进行并发控制

> 首先必须要注意的是，Promise 无法做到真正意义上的 并发执行！！！

为什么？？？

> 原因很简单，Javascript 本质是 单线程，尽管控制权交给 多个不同的Promise做处理，但是本质上依然是一个任务会被执行。只是看起来是并发

但是 Promise 也提供了 四种静态方法 处理异步任务的并发

- Promise.all()

> 可传入多个可迭代的 Promise，并且如果有其中一个无法兑现，则全部无效

```js
const promise1 = Promise.resolve(6);
const promise2 = 36;
const promise3 = new Promise((resolve, reject) => {
  setTimeout(resolve, 100, "foo");
});
const allPromise = Promise.all([promise1, promise2, promise3]).then(values => {
  console.log(values);
});
// [6, 36, 'foo']
```

- Promise.allSettled()

> 可传入多个可迭代的 Promise，当一个 Promise 被敲定（要么兑现，要么拒绝）都会返回

```js
const promise1 = Promise.resolve(1);
const promise2 = new Promise((resolve, reject) => {
  setTimeout(reject, 100, "foo");
});

const allSetPromise = [promise1, promise2];
const result = Promise.allSettled(allSetPromise).then(values => {
  console.log(values);
});
// [
//   {status: 'fulfilled', value: 1},
//   {status: 'rejected', reason: 'foo'}
// ]
```

- Promise.any()

> 当传入多个可迭代对象的 Promise，返回其中一个被兑现的值，并且返回第一个成功被兑现的值

- Promise.race()
  > 当传入多个可迭代对象的 Promise，也是返回一个，那个被兑现的快就返回哪个

```js
const promise2 = new Promise((resolve, reject) => {
  setTimeout(resolve, 200, "success1");
});
const promise3 = new Promise((resolve, reject) => {
  setTimeout(resolve, 100, "success2");
});
const allSetPromise = [promise2, promise3];
const allPromise = Promise.race(allSetPromise).then(values => {
  console.log(values);
});
// success2
```

#### async 和 await呢？

> 简单来说 他们是 Promise 的语法糖

`async`

绑定给已定的新的异步函数，函数体内可以使用 `await` 这让我们更加方便的处理 Promise，每次异步调用时，它都返回一个新的 Promise
避免了显示的配置 Promise 链。

可以通过 `async function` 表达式来定义异步函数

```js
const fun1 = () => {
  return new Promise(resolve => {
    setTimeout(() => {
      console.log("resolved");
    }, 2000);
  });
};
const fun2 = async () => {
  const response = await fun1();
  console.log(response); //resolved
};
fun2();
```

`await`

await 就是等待的意思 仅在异步函数或者模块顶级中使用，无法在脱离 async情况下使用，它一般作用于 `函数体` 中

> 异步函数体可以看作由零个 或者 多个 await 分割开来，也就是说从函数顶级到执行到第一个 await，此过程都是同步的运行的

`await` 表达式后都会被认为跟着一个 `.then()` 的回调，根据这个特性，每个 Promise 链都可以根据情况进行 `链重构`
