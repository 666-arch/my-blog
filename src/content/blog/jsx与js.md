---
author: Huang Chao
pubDatetime: 2023-11-10T15:58:53Z
modDatetime: 2023-12-06T13:05:56.066Z
title: "JSX和JS"
featured: false
draft: false
tags:
  - react
description: 关于JSX和JS的一些区别.
---

## 什么是JSX

JSX属于react的语法糖，JSX通过babel转义时，调用了 `React.createElement` 函数
它接收三个参数，分别是：`type` 元素类型，`props` 元素属性，`children` 子元素
