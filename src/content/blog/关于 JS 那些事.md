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
`var` 其实是 Javascript 的一个 `bug`，在定义变量时，可以在不初始化赋值的情况下直接使用并且浏览器只会警告可能存在 undefined 情况，不会直接告知开发者并且抛出异常
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
