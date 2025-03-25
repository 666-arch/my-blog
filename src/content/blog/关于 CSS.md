---
author: Huang Chao
pubDatetime: 2023-03-23T11:18:53Z
modDatetime: 2023-03-24T01:22:56.066Z
title: "关于 CSS 那些事"
featured: true
draft: true
tags:
  - css
description: "CSS碎片化"
---

### 知道重排（回流），重绘吗？

#### 重排

> 当元素作为一个几何属性，我的理解是一个大容器（尺寸、布局、位置）发生变化的时候，浏览器需要重新计算几何属性，并且更新渲染树的一个过程

#### 重绘

> 我的理解是在一个容器的颜色、或者字体大小、背景颜色、边框样式发生变化后浏览器需要重新绘制像素到屏幕上的过程

那么，也就是说 `重排` 一定会引起 `重绘`，但是重绘不一定引起重排，重排针对浏览器开销要超过重绘，因为它涉及重新计算。

### 为什么频繁的重排重绘影响性能？

这就涉及到浏览器的一个渲染过程：

#### 解析 HTML -> 构建 DOM -> 构建 CSSOM -> 合并渲染树 -> 绘制布局 -> 合成

### 如何优化这个性能呢？

1. 减少重排范围
   - 通过 position 定位属性脱离文档流，避免影响其他布局
   - css动画替代js动画，js动画是一个单一线程
2. 布局的方式去优化
   - 通过 flex 布局替代浮动布局，减少布局的复杂度，避免浮动塌陷情况
3. 虚拟列表
   - 暂时只将浏览器可视区域渲染出来，减少DOM节点的增加和删除

### 如何实现一个虚拟列表？

通过 `react-window`

```jsx
import { FixedSizeList as VirtualList } from "react-window";
const App = () => (
  <VirtualList
    height={600}
    itemCount={1000}
    itemSize={50} // 固定高度
    width={300}
  >
    {({ index, style }) => <div style={style}>Item {index}</div>}
  </VirtualList>
);
```

### 如何理解BFC？

首先 它是一种css的渲染机制，意思是块级元素与其他元素相互作用产生的。是页面的一种容器，内部不受外部影响。

#### 触发条件

- 根元素（Html）
- float: 'left' | 'right'
- position: 'absolute' | 'fixed'

#### BFC 解决了什么问题

- 解决浮动塌陷

  > 盒子设定浮动值会造成浮动塌陷问题，那么就可以过设置 overflow 属性来创建一个BFC容器，让元素撑满高度，正确计算父容器的高度

- 垂直盒子的外边距重合问题

  > 在两个盒子设置 margin 属性时，盒子直接会忽略下面盒子设置的 margin-top 也就是位置会发生重叠，也可以通过 overflow 去设置

- 两栏自适应布局
  > 给不固定那一栏开启BFC，区域就不会和浮动的盒子造成重叠的情况
