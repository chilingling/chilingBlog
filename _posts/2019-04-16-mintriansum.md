---
layout: post
title: LeetCode120 三角形最小路径和
author: 炽凌
date: 2019-04-16
categories: LeetCode
tags: LeetCode 动态规划
finished: true
---

> 给定一个三角形，找出自顶向下的最小路径和。每一步只能移动到下一行中相邻的结点上。

示例：

```
[
     [2],
    [3,4],
   [6,5,7],
  [4,1,8,3]
]
```

自顶向下的最小路径和为：11 (`2+3+5+1 = 11`)

**如果你可以只使用 *O*(*n*) 的额外空间（*n* 为三角形的总行数）来解决这个问题，那么你的算法会很加分。**

思路：

动态规划的思想

常规的思路都是从上往下进行选择，但是，从上往下选择的时候从第三层开始就会有很多的选择的可能性。我们需要进行更多额外的空间，进行临时选择。

第二种方法：从底层往上进行选择。我们只需要从下面一层的相邻元素选择最小的。一直迭代到顶层，就完成了最小路径和的计算。这里应该涉及到一个概念叫最优子结构，个人对这个理解不深，就不献丑了。

代码实现

```js
/**
 * @param {number[][]} triangle
 * @return {number}
 */
var minimumTotal = function(triangle) {
    let dp = triangle[triangle.length-1] // 底层元素
    let i = triangle.length-2 // 从倒数第二层开始迭代
    while (i >= 0) {
        let j = 0
        while (j < triangle[i].length) {
            dp[j] = triangle[i][j] + Math.min(dp[j], dp[j+1]) // 从当前层的下一层的两个相邻元素选择最小的一个，就是最优的子结构
            ++j
        }
        dp.pop() // 迭代完一次抛弃掉一个
        --i
    }
    return dp // 最后迭代只剩下一个了，直接返回就好
};
```

