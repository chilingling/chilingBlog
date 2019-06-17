---
layout: post
title: LeetCode349——两个数组的交集
author: 炽凌
date: 2019-06-17
categories: LeetCode
tags: LeetCode
comments: true
finished: true
---
> 给定两个数组，编写一个函数来计算他们的交集

示例一

```sh
输入: nums1 = [1,2,2,1], nums2 = [2,2]
输出: [2]
```

示例二

```sh
输入: nums1 = [4,9,5], nums2 = [9,4,9,8,4]
输出: [9,4]
```

**说明:**

- 输出结果中的每个元素一定是唯一的。
- 我们可以不考虑输出结果的顺序。

思路：直接上ES6+的API，用一个Set来存储结果（保证重复添加进去的也是唯一的），遍历数组1，利用`indexOf`查看数组2有没有，有就暴力的添加进Set里面，最后将Set转成数组返回。在这其中，判断数组二有没有当前遍历元素尝试用过`includes`，但是性能并不如直接的`indexOf`，同时，循环也用过forEach，但是尝试之后发现直接for循环的性能的速度高一点。

代码实现：

```js
/**
 * @param {number[]} nums1
 * @param {number[]} nums2
 * @return {number[]}
 */
var intersection = function(nums1, nums2) {
    let res = new Set()
    for (let i = 0; i < nums1.length; i++) {
        if (nums2.indexOf(nums1[i]) > -1) {
            res.add(nums1[i])
        }
    }
    return [...res]
};
```

