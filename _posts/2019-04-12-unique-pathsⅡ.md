---
layout: post
title: 不同路径Ⅱ
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
> 现在考虑网格中有障碍物。那么从左上角到右下角将会有多少条不同的路径？
>
> **说明：**m 和 *n* 的值均不超过 100。

示例一：

```
输入:
[
  [0,0,0],
  [0,1,0],
  [0,0,0]
]
输出: 2
解释:
3x3 网格的正中间有一个障碍物。
从左上角到右下角一共有 2 条不同的路径：
1. 向右 -> 向右 -> 向下 -> 向下
2. 向下 -> 向下 -> 向右 -> 向右
```

思考：与不同路径Ⅰ类似，但是多了障碍物，所以我们只要处理好这种情况就好了

编码实现

```js
/**
 * @param {number[][]} obstacleGrid
 * @return {number}
 */
var uniquePathsWithObstacles = function(obstacleGrid) {
    let row = obstacleGrid.length // 行数
    let column = obstacleGrid[0].length // 列数
    if (row <= 1 && column <= 1) {
        return obstacleGrid[0][0] === 1 ? 0 : 1
    }
    
    for (let i = 0; i < row; ++i) {  // 遍历行
        for (let j = 0; j < column; ++j) { // 遍历列
            if (i === 0 && j === 0) {
                if (obstacleGrid[i][j] === 1) { // 
                    return 0
                }
                obstacleGrid[i][j] = 1
                continue
            }
            if (obstacleGrid[i][j] === 1) { // 当前位为障碍物
                obstacleGrid[i][j] = false
                continue
            }
            if (i === 0) { // 第一行
                obstacleGrid[i][j] = !obstacleGrid[i][j-1] ? false : 1 // 如果遇到了障碍物，则設置该标记位为false，否则，为1，下一个条件语句也是同样的逻辑
                continue
            }
            if (j === 0) { // 第一列
                obstacleGrid[i][j] = !obstacleGrid[i-1][j] ? false : 1
                continue
            }
            obstacleGrid[i][j] += !obstacleGrid[i][j-1] ? 0 : obstacleGrid[i][j-1]
            obstacleGrid[i][j] += !obstacleGrid[i-1][j] ? 0 : obstacleGrid[i-1][j]
            
        }
    }
    return obstacleGrid[row-1][column-1]
};
```

