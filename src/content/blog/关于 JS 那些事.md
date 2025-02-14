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

### JS 如何构造一个一百个全为 0 的数组

- 通过 Array.fill
  > 答：用于填充一个固定值到数组中，从起始索引到终止索引

```js
new Array(100).fill(0);
```
