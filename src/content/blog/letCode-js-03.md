---
author: Huang Chao
pubDatetime: 2024-02-16T12:10:22Z
modDatetime: 2024-02-17T14:26:56Z
title: "力扣系列-03多数元素"
featured: true
tags:
  - leetcode
description: JavaScript - 获取大于 n/2 的多数元素.
---

#### 给定一个大小为 n 的数组 nums ，返回其中的多数元素。多数元素是指在数组中出现次数 大于 `n/2  `的元素。

- 你可以假设数组是非空的，并且给定的数组总是存在多数元素。

## 示例 1：

> 输入：nums = [3,2,3] 输出：3

## 示例 2：

> 输入：`nums` = [2,2,1,1,1,2,2] 输出：2

## js代码

```js
/**
 * @param {number[]} nums
 * @return {number}
 */
var majorityElement = function (nums) {
  nums.sort((a, b) => a - b);
  return nums[Math.floor(nums.length / 2)];
};
```

## 题解：

- 首先针对数组进行顺序排序
- 因为至少一半大于 `n /2` 通过math.floor函数，得到的索引下标就是次数最多的元素
