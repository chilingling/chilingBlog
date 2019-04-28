---
layout: post
title: 不同路径Ⅰ
author: 炽凌
date: 2019-04-12
categories: LeetCode
tags: LeetCode 动态规划
comments: true
finished: true
---
> 一个机器人位于一个 *m x n* 网格的左上角 （起始点在下图中标记为“Start” ）。
>
> 机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为“Finish”）。
>
> 问总共有多少条不同的路径？
>
> 说明：m和n的值均不超过100

示例一

```
输入: m = 3, n = 2
输出: 3
解释:
从左上角开始，总共有 3 条路径可以到达右下角。
1. 向右 -> 向右 -> 向下
2. 向右 -> 向下 -> 向右
3. 向下 -> 向右 -> 向右
```

示例二：

```
输入: m = 7, n = 3
输出: 28
```

思路过程：

动态规划的思想，从1x1、2x1、3x1、nx1、2x1、3x1、 4x1、 5x1 、nx1、 2x2 、3x2 、3x3等找规律

一段时间后，找出规律：

`m=1, n= x` 或者  `m=x  n=1`的时候只有一种路径

`m =  m, n = n`  的时候，路径数的和是： `m = m-1,  n = n` 加  `m = m, n = n-1`  时候的和

因此可以得出：

```
初始状态：dp[n][0] = 1  dp[1][0] = 1  dp[m-1][n-1] = 1
状态转移方程：  dp[m][n] = dp[m-1][n] + dp[m][n-1]
```

边界条件：

```
m <= 0, n <= 0  的时候，结果为0
```

编码实现一：

利用递归实现：

```js
var uniquePaths = function(m, n) {
    if (m === 1 || n === 1) {
        return 1
    }
    return uniquePaths(m-1, n) + uniquePaths(m, n-1)
};
```

特点：第一种实现的方式，简单易懂

缺点：存在重复计算，比如 `m= 3, n =3`的情况，要计算`m=3, n= 2`+ `m=2,n= 3`的情况和，而这两种情况的和都需要计算`m=2,n=2`的和，假如`m`和`n`的数值比较大，重复的计算也就更多，在LeetCode上面测试也不出意外的超时了

编码实现二：

循环实现，最接近目标的位置开始迭代，同时使用dp数组保存每一次计算下来的值，避免重复计算

时间复杂度：`O(mxn)`

空间复杂度：`O(mxn)`

```js
/**
 * @param {number} m
 * @param {number} n
 * @return {number}
 */
var uniquePaths = function(m, n) {
    if (m < 1 || n < 1) {
        return 0
    }
    var dp = [] // 保存计算结果的二维数组
    // 循环行
    for (let i = 0; i < m; ++i) { // 从0开始迭代，比较习惯，从0开始迭代也就意味着我们的目标在0,0 的位置，机器人在m-1,n-1的位置
         dp[i] = []
         // 循环列
         for (let j = 0; j < n; ++j) {
             if (i === 0 || j === 0) {
                 dp[i][j] = 1
             } else  {
                 dp[i][j] = dp[i-1][j] + dp[i][j-1]
             }
         }
     }
     return dp[m-1][n-1]
};
```

编码实现三

有大佬注意到这是一个组合问题，问题直接变成下面公式的接:
$$
C_{m+n-1}^{m-1}
$$

```js
/**
 * @param {number} m
 * @param {number} n
 * @return {number}
 */
var uniquePaths = function(m, n) {
    if (m < 1 || n < 1) {
        return 0
    }
    let p1 = m+n-2 // 下标
    let p2 = m-1 // 上标
    let i = 0 // 遍历用的
    let s1 = 1
    let s2 = 1
    while ( i < p2) {
        s1 *= (p1 - i)
        s2 *= (p2 - i)
        ++i
    }
    return s1/s2
};
```

