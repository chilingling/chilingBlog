---
layout: post
title: 最小路径和
author: 炽凌
date: 2019-04-12
categories: LeetCode
tags: LeetCode 动态规划
comments: true
finished: true
---
> 给定一个包含非负整数的 *m* x *n* 网格，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。
>
> **说明：**每次只能向下或者向右移动一步。

示例：

```
输入:
[
  [1,3,1],
  [1,5,1],
  [4,2,1]
]
输出: 7
解释: 因为路径 1→3→1→1→1 的总和最小。
```

分析：

这是最小路径问题的变形之一，要计算从1,1到mxn位置的的最小和的路径，那么就是在找`Min(dp[m-1][m], dp[m][n-1])`

编码实现：

```js
/**
 * @param {number[][]} grid
 * @return {number}
 */
var minPathSum = function(grid) {
    let rows = grid.length // 行数 m
    let columns = grid[0].length // 列数 n
    let dp = [] // 计算i,j位置上到达目标位置值得最小路径得和，记忆数组，避免重复计算
    for (let i = rows-1; i >= 0; --i) { // 遍历行
        dp[i] = [] //初始化该行为数组
        for (let j = columns -1; j >= 0 ; --j) {
            if (i === rows-1 && j === columns-1) { // 目标位置，直接赋值
                dp[i][j] = grid[i][j]
            } else if (i+1 >= rows) { // 处于最末尾一行
                dp[i][j] = dp[i][j+1] + grid[i][j]
            } else if (j + 1 >= columns) { // 处于最末尾一列
                dp[i][j] = dp[i+1][j] + grid[i][j]
            }else { // 有下一列和下一行的情况
                dp[i][j] = Math.min(dp[i+1][j], dp[i][j+1]) + grid[i][j]
            }
        }
    }
    return dp[0][0] // 返回第一个初始位置就是目标值
};
```

