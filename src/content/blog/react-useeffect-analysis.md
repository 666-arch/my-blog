---
author: Huang Chao
pubDatetime: 2024-04-05T04:58:53Z
modDatetime: 2024-04-05T13:05:56.066Z
title: "react hooks useEffect 使用浅析"
featured: true
tags:
  - react
description: react hooks uesEffect 解析.
---

💡：可以让你完成一些类似于class中生命周期的功能

## 简单使用规则

- useEffect要求我们传入一个回调函数，在react执行完更新DOM操作后，就会回调这个函数
- 默认情况下，无论是第一次渲染之后，还是每次更新之后，都会执行这个 回调函数

```js
import React, { memo, useState, useEffect } from "react";
export default memo(function App() {
  const [count, setCount] = useState(200);
  useEffect(() => {
    //当前传入的回调函数会在组件被渲染完成后，自动执行
    document.title = count;
  });
  return (
    <div>
      <h2>当前计数： {count}</h2>
      <button onClick={e => setCount(count + 1)}>+1</button>
    </div>
  );
});
```

## 为什么effect中需要返回一个函数，清除副作用代码

- 简单的清楚事项，重新渲染 or 再存在订阅情况下进行事件的清楚
- 这是effect可选的清除机制。每个effect都可以返回一个清除函数
- 它们都属于effect的一部分

```js
import React, { memo, useEffect, useState } from "react";
export default memo(function App() {
  const [count, setCount] = useState(0);
  //负责告知react，在执行完当前组件渲染之后要执行的副作用代码
  useEffect(() => {
    //1.监听事件
    console.log("监听redux中数据变化，监听eventBus中的why事件");
    // 返回值：回调函数 => 组件被重新渲染或者组件卸载的时候执行
    return () => {
      console.log("取消监听redux中数据变化，取消监听eventBus中的why事件");
    };
  });
  return (
    <div>
      <button onClick={e => setCount(count + 1)}>+1({count})</button>
    </div>
  );
});
```

## 异步效果（Async Effects）

- 有时候你的作用效果是异步的，例如在页组件初始化的时候进行http请求，这时候你可以在uesEffect内部定义一个函数，执行异步操作

```js
import React, { useEffect } from "react";
useEffect(
  () => {
    const fetchData = async () => {
      // 执行异步操作
      const result = await someAsyncFunction();
      // 处理异步操作的结果
    };
    fetchData();
    // 你也可以直接在 useEffect 内部调用异步函数
    // someAsyncFunction().then(result => { /* 处理异步操作的结果 */ });
  },
  [
    /* 依赖数组 */
  ]
);
```

## Effect 的条件执行

```js
import React, { useState, useEffect } from "react";
function ExampleComponent() {
  const [data, setData] = useState(null);
  const [fetchData, setFetchData] = useState(false);
  useEffect(() => {
    if (fetchData) {
      // 执行数据获取逻辑
      fetchDataFromAPI().then(result => {
        setData(result);
      });
    }
  }, [fetchData]); // 仅在 fetchData 发生变化时执行
  const handleButtonClick = () => {
    setFetchData(true); // 在按钮点击时设置 fetchData 为 true，触发 effect 执行
  };
  return (
    <div>
      <button onClick={handleButtonClick}>Fetch Data</button>
      {data && <p>{data}</p>}
    </div>
  );
}
export default ExampleComponent;
```
