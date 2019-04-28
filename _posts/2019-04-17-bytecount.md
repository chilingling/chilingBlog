---
layout: post
title: LeetCode338 比特位计数
author: 炽凌
date: 2019-04-17
categories: LeetCode
tags: LeetCode 动态规划
comments: true
finished: true
---
>给定一个非负整数 **num**。对于 **0 ≤ i ≤ num** 范围中的每个数字 **i** ，计算其二进制数中的 1 的数目并将它们作为数组返回。

示例：

```
输入: 2
输出: [0,1,1]
```

示例：

```
输入: 5
输出: [0,1,1,2,1,2]
```

进阶：

- 给出时间复杂度为**O(n\*sizeof(integer))**的解答非常容易。但你可以在线性时间**O(n)**内用一趟扫描做到吗？
- 要求算法的空间复杂度为**O(n)**。
- 你能进一步完善解法吗？要求在C++或任何其他语言中不使用任何内置函数（如 C++ 中的 **__builtin_popcount**）来执行此操作。

#### 时间复杂度为`O(n*sizeof(integer))`的解法：

- 从0遍历到n，变量为`i`  ，这里的时间复杂度是`O(n)`
- 遍历的时候，看看`i`有多少个1，这里的时间复杂度是`O(sizeof(integer))`，（其实可以优化）

编码实现：

```js
/**
 * @param {number} num
 * @return {number[]}
 */
var countBits = function(num) {
    let dp = [];
    let i = 0;
    while (i <= num) { // 遍历num+1次
        let count = 0
        let n = i
        while (n) { // 计算当前循环变量有多少个1
        	++count; // 计数加一
            n = (n-1)&n; // 把一个整数减去1，再和原整数做与运算，会把该整数最右边一个1变成0，一个整数的二进制表示仲有多少个1，就可以进行多少次这样的操作
        }
        dp.push(count);
        ++i;
    }
    return dp;
};
```

#### 时间复杂度为`O(n)`的解法

- 观察从`2-3`、`4-7`、`8-15`的二进制数量的变化，可以观察到，4是2的两倍，所以二进制表示中只有一个1，8是4的两倍，同样二进制表示中只有一个1，同时，5、6、7的二进制1的个数刚好是1、2、3的二进制数中1的个数加一，9-15的二进制数中1的个数刚好是1-7的二进制数中1的个数加一。
- 从上面的观察中，我们可以联想到动态规划的解法

初状态方程是： `dp[0] = 0`

状态转移方程： `dp[i] = i/d === 2 || i === 2 ? 1 : dp[addIndedx] + 1`  其中，`d`表示的是上一次是2的整数次幂的数

编码实现：

```js
/**
 * @param {number} num
 * @return {number[]}
 */
var countBits = function(num) {
    let dp = [0] // 第一位是初始状态
    let i = 1, d = 0, addIndex = 0 // d记录的是变为1的数，也就是，4 8 16等2的幂次数，addIndex是从前面加的顺序数
    while (i <= num) {
        if (i/d === 2 || i === 2) { //是2，4， 8，16 .。。
            dp.push(1)
            d = i
            addIndex = 1
        } else { // 普通的迭代
            dp.push(dp[addIndex] + 1)
            addIndex++
        }
        ++i
    }
    return dp
};
```

更优雅的实现：

```js
/**
 * @param {number} num
 * @return {number[]}
 */
var countBits = function(num) {
    let res = [0]
    for (let i = 1; i <= num; ++i) {
        res[i] = res[i>>1] + (i&1)
    }
    return res
};
```

