---
author: Huang Chao
pubDatetime: 2024-02-16T10:23:22Z
modDatetime: 2023-12-20T13:05:56Z
title: "力扣系列-01删除有序数组中的重复项"
featured: true
tags:
  - leetcode
description: JavaScript - 删除有序数组中的重复项.
---

#### 给你一个 非严格递增排列 的数组 nums ，请你 `原地` 删除重复出现的元素，使每个元素 只出现一次 ，返回删除后数组的新长度。元素的 相对顺序 应该保持 一致 。然后返回 nums 中唯一元素的个数。

- 更改数组 `nums` ，使 `nums` 的前 k 个元素包含唯一元素，并按照它们最初在 `nums` 中出现的顺序排列。nums 的其余元素与 `nums` 的大小不重要。

- 返回 `k`

## 示例 1：

> 输入：nums = [1,1,2]
> 输出：2, nums = [1,2]
> 解释：函数应该返回新的长度 2 ，并且原数组 nums 的前两个元素被修改为 1, 2 。不需要考虑数组中超出新长度后面的元素。

## 示例 2：

> 输入：nums = [0,0,1,1,1,2,2,3,3,4]
> 输出：5, nums = [0,1,2,3,4]
> 解释：函数应该返回新的长度 5 ， 并且原数组 nums 的前五个元素被修改为 0, 1, 2, 3, 4 。不需要考虑数组中超出新长度后面的元素。

## js代码

```js
/**
 * @param {number[]} nums
 * @return {number}
 */
const removeDuplicates = function (nums) {
  let index = 0;
  for (let i = 1; i < nums.length; i++) {
    if (nums[i] !== nums[index]) {
      nums[++index] = nums[i];
    }
  }
  return index + 1;
};
```

## 题解：

- index, 标记一个新的索引下标，方便对比原数组
- 始终将未重重的数据追加到新的`nums[++index]`中
- 最后返回新数组的`length`值，因为是从`0`开始，所以要 `+1`
