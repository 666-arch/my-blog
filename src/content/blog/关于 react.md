---
author: Huang Chao
pubDatetime: 2023-03-23T11:18:53Z
modDatetime: 2023-03-24T01:22:56.066Z
title: "关于 React 那些事"
featured: true
draft: false
tags:
  - react
description: "react碎片化"
---

### 有没有使用过 React hooks？

首先，我记得 react hooks 是从 react 16.8 引入的一套理念

#### 为什么引入？

相比复杂的类组件，函数式组件更符合 `react` 官方提倡的 函数式编程，组件化理念，减少了复杂的生命周期
不用担心 `this` 的问题，能更好的进行逻辑复用，代码逻辑更清晰，状态管理也更加清晰

`组件树层级变浅`，原本的 render props 等方式增加了渲染树的复杂程度，很容易形成嵌套地狱，
而这些功能目前都可以通过 react hooks 来实现。

### 你在项目中使用过哪些 hooks？

- useState
- useEffect
- useRef

#### useState？

react 允许在组件中通过 `useState` 存储一些状态变量，这些状态变量
可以是基本的数据类型，也可以是复杂的数据类型

#### 为什么状态更新不及时？

因为 `useState` 状态更新机制是异步的，并不能直接在代码下一行获取最新
出于性能优化（批量更新机制）

#### 如何解决？

- 通过 `useEffect` 监听需要更新的状态值，通过依赖值，状态更新hou
- 通过 函数式更新，使用 `setState(pre=> newValue)` 确保获取最新的值
- 使用 `useRef` 捕获最新的值
- 通过 `flushSync` 强制同步更新（不推荐）

#### useEffect？

定义：

> 在函数组件中处理副作用（数据请求，DOM操作，订阅）等操作，它替代了
> 类组件中componentDidMount、DiUpdate、WillMount生命周期

原理：

> 底层机制就是 react 在每次渲染后（首次渲染和更新）执行useEffect
> 中的第一个参数（回调函数），如果存在第二个参数依赖项，react会比较
> 这个依赖项是否发生变化，有就执行回调

空数组表示只在初始化挂载的时候运行

注意项 1（闭包问题）：

> 在使用定时器或者遇到闭包问题时候，当组件被卸载后，定时器不会被自动清除，会导致无法GC，然后内存泄露
> 可以通过 `return` 调当前外部的回调函数做一个 `clear` 操作

注意项 2（闭包问题）：

> 当我在回调函数中直接使用状态变量 state 时，也会出现闭包问题，
> 无法获取到最新值，我会改用通过 useRef 记录当前最新的 current 属性
> 保证获取到的是最新的状态

#### 如何避免 useEffect 的无限循环？

- 检查依赖项数组是否遗漏了回调函数内部使用的状态
