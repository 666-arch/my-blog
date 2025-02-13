---
author: Huang Chao
pubDatetime: 2023-03-23T11:18:53Z
modDatetime: 2023-03-24T01:22:56.066Z
title: "关于 Vue 的那些事"
featured: false
draft: false
tags:
  - Vue
description: "前端生态框架之vue系列"
---

#### 如何理解 Vue 称自己是一个 “渐进式框架”？

> 其核心理念就是灵活的适应不同的开发需求，根据实际情况扩展框架功能

## 分层架构：按需扩展

- 核心库专注视图层（UI）：vue的核心就是只处理视图的渲染和基本的数据响应式绑定，不强制依赖其他库（路由、状态管理）
- 渐进式生态：官方提供配套库（vue router、vuex/pinia、vite）等工具链，开发者可工具情况按需引入

## 灵活集成：适应不同场景

- 技术栈自由：不会强制你使用固定的工具或者架构模式，开发者可自主选择
  - Webpack
  - vite
  - Options API
  - Composition API

## 对比其他框架

- React：核心库专注UI，但生态过于碎片化，vue提供更统一的官方支持
- Vue：既保持核心轻量、官方库完整、由开发者自由控制复杂度

## vue2中响应式的核心方法是什么？

`Object.defineProperty()`

### 如何实现，其原理是什么？

`Object.defineProperty()` 允许为对象的属性定义自定义的 `getter` 和 `setter` 核心原理就是数据 `劫持`

- 初始化 Watcher

```js
class Watcher {
  constructor(getter) {
    this.getter = getter;
    this.value = this.get();
  }
  get() {
    Dep.target = this; // 标记当前依赖
    const value = this.getter(); // 触发当前 getter，收集依赖
    Dep.target = null;
    return value;
  }
  update() {
    this.get(); // 重新执行 getter 更新视图
  }
}
```

- 依赖收集

```js
class Dep {
  constructor() {
    this.subs = []; // 存储 Watcher 实例
  }
  depend() {
    if (Dep.target) {
      this.subs.push(Dep.target); // 将当前 Watcher 添加到依赖
    }
  }
  notify() {
    this.subs.forEach(watcher => watcher.update()); // 通知所有 Watcher 更新
  }
}
Dep.target = null; // 全局变量 指向当前正在计算的 Watcher
```

- 数据变更

```js
function defineReactive(obj, key) {
  let value = obj[key];
  Object.defineProperty(obj, key, {
    configurable: true,
    enumerable: true,
    get() {
      console.log(`读取${key}: ${value}`);
      if (Dep.target) {
        Dep.target();
      }
      return value;
    },
    set(newValue) {
      if (value === newValue) return;
      console.log(`设置 ${key}:${value}`);
      value = newValue;
      //触发更新，通知所有依赖的 Watcher 执行更新
      Dep.notify();
    },
  });
}
```

### 执行步骤

- 初始化一个 Watcher 也就是观察者模式，执行 get() 方法， 触发属性的 getter
- 收集依赖 定义一个数组用来存储 Watcher 实例，需要修改的目标实例指向当前的 target
- 数据变更，触发 setter
- 派发更新，属性值的变化 调用 notify() 通知所有 Watcher 去执行 Update()

### 处理数组问题

> `Object.defineProperty()` 无法监听数组数组索引的变化，Vue通过重写数组方法实现响应式

- 通过替换数组原型链的方式

```js
const arrayProto = Array.prototype;
const arrayMethods = Object.create(arrayProto);

["push", "pop", "shift", "unshift", "splice", "sort", "reverse"].forEach(
  method => {
    const original = arrayProto[method];
    Object.defineProperty(arrayMethods, method, {
      value(...args) {
        const result = original.apply(this, args);
        console.log("数组变化，触发更新");
        dep.notify(); // 通知依赖更新
        return result;
      },
    });
  }
);

// 替换数组的原型链
function observeArray(arr) {
  arr.__proto__ = arrayMethods;
  arr.forEach(item => observe(item)); // 递归观察数组元素
}
```

## 那么 Vue3 如何做的呢？

在这之前 需要了解 `Object.defineProperty()`的局限性

- 对象局限性：无法解决对象属性的新增、删除

```js
// Vue 2 中，以下操作不会触发响应式更新
this.obj.newProp = 123; // 新增属性无效
delete this.obj.existingProp; // 删除属性无效
// 必须使用 Vue.set或delete
```

- 数组的局限性

```js
// Vue 2 无法检测以下操作
this.arr[0] = 1; // 通过索引修改元素
this.arr.length = 0; // 修改数组长度
//通过重写数组的的7个方法（push/pop等，但是仍有局限性）
```

- 性能问题
  - 针对处理复杂对象属性时，需要逐个的劫持 getter、setter，大型对象初始化慢
  - 过多的递归容易导致递归异常，栈溢出问题
  - 每次访问深层次属性，都会触发 getter、setter
