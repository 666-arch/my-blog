---
author: Huang Chao
pubDatetime: 2023-03-23T11:18:53Z
modDatetime: 2023-03-24T01:22:56.066Z
title: "关于 Vue 的那些事"
featured: false
draft: true
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
