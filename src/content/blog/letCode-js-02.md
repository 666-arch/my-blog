---
author: Huang Chao
pubDatetime: 2024-02-16T12:10:22Z
modDatetime: 2024-05-20T14:26:56Z
title: "力扣系列-02删除有序数组中的重复项"
featured: true
tags:
  - leetcode
description: JavaScript - 删除有序数组中的重复项 II.
---

#### 给你一个 非严格递增排列 的数组 nums ，请你 `原地` 删除重复出现的元素，使每个元素 只出现 `两次` ，返回删除后数组的新长度。元素的 相对顺序 应该保持 一致 。然后返回 nums 中唯一元素的个数。

- 更改数组 `nums` ，使 `nums` 的前 k 个元素包含唯一元素，并按照它们最初在 `nums` 中出现的顺序排列。nums 的其余元素与 `nums` 的大小不重要。

- 返回 `nums.length`

## 示例 1：

> **输入：** nums = [1,1,1,2,2,3] > **输出：** nums = [1,2]
> 解释：函数应该返回新的长度 2 ，并且原数组 nums 的前两个元素被修改为 1, 2 。不需要考虑数组中超出新长度后面的元素。

## 示例 2：

> **输入：** nums = [0,0,1,1,1,1,2,3,3] > **输出：** 7, nums = [0,0,1,1,2,3,3]
> 解释：函数应返回新长度 length = 7, 并且原数组的前七个元素被修改为 0, 0, 1, 1, 2, 3, 3。不需要考虑数组中超出新长度后面的元素。

## js代码

```js
/**
 * @param {number[]} nums
 * @return {number}
 */
var removeDuplicates = function (nums) {
  if (nums.length <= 2) return nums.length;
  let repeatCount = 1; //记录重复的次数
  let index = 0; //对比重复
  for (let i = 1; i < nums.length; i++) {
    if (nums[i] !== nums[index]) {
      repeatCount = 1;
      index = i;
    } else {
      if (repeatCount === 2) {
        nums.splice(i, 1);
        i--;
      } else {
        repeatCount++;
      }
    }
  }
  return nums.length;
};
```

## 题解：

- `repeatCount` 标记记录数组中出现重复元素的次数
- `index` 对比当前元素和原数组的元素

* 遍历数组
  - 如果当前对比的元素不存在重复，重新记录重复的次数 `repeatCount`，重置对比重复的 `index`
  - 反之，`repeatCount++`, 记录出现重复的次数
  - 如果出现重复次数 `=2`
    - 表明，对比的元素重复次数 `>2` 次
    - 通过 `splice` 保留当前索引下的元素，移除后一个重复的元素
    - `i--`，是为了从当前移除的元素再继续对比
* 最后返回数组的个数
