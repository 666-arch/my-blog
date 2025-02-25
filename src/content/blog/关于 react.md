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

`组件树层级变浅`，原本的 render props等方式增加了渲染树的复杂程度，很容易形成嵌套地狱，
而这些功能目前都可以通过 react hooks 来实现。
