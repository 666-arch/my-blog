---
author: Huang Chao
pubDatetime: 2023-03-23T11:18:53Z
modDatetime: 2023-03-24T01:22:56.066Z
title: "关于 React 那些事"
featured: true
draft: true
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

#### React Hooks 为什么不能在条件或循环中调用？

> 首先，react 是通过 `链表结构` 来管理 `hooks` 的状态，会严格按照
> 顺序依次执行被添加到 链表 的 hooks，保证状态的一致性，
> 条件/循环语句会破坏 react hooks 调用顺序的稳定性

1. 例如在条件语句中：

```js
function compnetn() {
  if (condtion) {
    const [state1, setState1] = useState(false); //第一次执行
  }
  const [state2, setState2] = useState(true); // ❌
}
```

上述代码中，当 state1 hook 第一次 false 被执行，if语句变为 true时，
react 会认为把 state2 还是当作第一次执行，这样容易造成数据错乱

2. 例如在循环语句中：

```js
function Component({ items }) {
  for (let i = 0; i < items.length; i++) {
    const [state, setState] = useState(null); // ❌ 循环次数变化时，Hooks 数量会改变
  }
}
```

上述代码中，for循环会尝试多个 hooks，每循环一次，hooks 数量就会发生变化，显然会造成诸多不确定性。

3. React报错机制不允许我们这样使用

在条件、循环、嵌套函数中使用 hooks，会直接抛出错误如下错误：

`React Hook "useState" is called conditionally. React Hooks must be called in the exact same order in every component render.`

### react 中 ref 是干什么用的，有哪些使用场景？

> 获取深层次的 `DOM` 结构进行其他操作，通常我都用它来绑定 hooks 结合 `useRef`
> 一起使用，来针对一些 DOM 进行一些初始化 或者 判断该 DOM 是否渲染成功

### React 组件如何通信？

- 父子组件
  - Props/回调函数
- 兄弟组件
  - 状态提升：状态提升到共同父组件
  - Context
- 跨层级组件
  - redux、事件总线（尽量不用，会破坏数据流可预测性）

### 如何优化 React 性能？

1. 组件缓存

   - React.memo 缓存函数组件，避免无意义渲染
   - useMemo 缓存计算结果
   - useCallback 缓存函数引用
   - 使用 `useDebounce` 对值及事件处理函数进行防抖，避免状态频繁变动，优化渲染次数
   - 使用 `react-query` 对请求响应进行缓存、重发等，避免多次请求，减少网络 IO 消耗及优化渲染次数

#### memo 记忆组件

实际上 `memo` 接收两个参数，一个是包裹的组件，一个是 `arePropsEqual`。

`arePropsEqual` 也是一个函数，接收两个参数，一个是旧的 `props`，
一个是新的 `props`，不需要特别指定，默认情况下 它内部会通过 `Object.is` 进行
浅比较每个 `prop`

> 但是如果是针对引用类型（数组、对象），每次渲染都会生成新的引用

父组件：

```tsx
function Parent() {
  const [num, setNum] = useState(0);
  return (
    <>
      <Chrilden num={num} />
      <div onClick={() => setNum(1)}>这是父组件 {num}</div>
    </>
  );
}
```

子组件：

```tsx
const Chrilden = React.memo(function Chrilden({ num }) {
  console.log("Chrilden was rendered at", num);
  return <h3>这是子组件 {num}</h3>;
});
```

2. 代码分割，React.lazy + suspense 动态加载组件

#### 使用 `React.lazy` 按需导入组件

```jsx
const MyComponent = React.lazy(() => import("./MyComponent"));
```

使用 `Suspense`包裹懒加载组件

```jsx
import React, { Suspense } from "react";
function App() {
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <MyComponent />
      </Suspense>
    </div>
  );
}
```

注意：`Suspense` 只能用于包裹被懒加载的组件，不能包裹普通组件

3. 虚拟化长列表，使用 react-window 或者 react-virtualized 实现虚拟滚动

### 如何实现受控表单组件？

> 通过表单的 `value` 属性结合 `useState` 以及 `onChange` 控制表单的输入
> 来使得它成为一个受控制的组件

### 非受控表单的使用场景？

> 非受控组件通过 `ref` 来绑定 `DOM` 使用 `current.value` 属性来提交表单值
> 适合文件上传或者无需进行数据验证的场景

### 关于 React Fiber 的问题？

> React Fiber 是 react 16 新引入的协调算法，它目的就是解决在
> 同步任务中造成渲染阻塞然后节点卡顿的问题。

> 它把任务拆分成可中断、可恢复的异步任务，使得React能更高优先级处理
> 更新（比如动画渲染）同事保持应用的流畅性

理论上来说每个组件都对应一个 `Fiber` 节点，它关联了每个组件的
`state` 状态、`effect` 副作用等信息，然后组成一个链表结构，通过
算法能深层优先遍历节点。

在调度机制上，它利用了 `浏览器空闲机制` 然后在这段时间去分片的去执行任务吗，并且确保主线程任务不被阻塞。

### React 19 了解过吗？有什么新特性？

好像新增了几个 hooks

- useActionStatus：处理异步 pending、error、data的一些状态
- useFormStatus：追踪表单提交的状态？
