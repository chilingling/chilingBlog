---
layout: post
title: LeetCode179——最大数
author: 炽凌
date: 2019-06-16
categories: LeetCode
tags: LeetCode
comments: true
finished: true
---

> 给定一组非负整数，重新排列它们的顺序使之组成一个最大的整数。

示例 1:

```sh
#输入: [10,2]
#输出: 210
```

示例 2:

```sh
#输入: [3,30,34,5,9]
#输出: 9534330
```

说明: 输出结果可能非常大，所以你需要返回一个字符串而不是整数。

初始思路，看到这个可以想到JavaScript的Array sort API，默认就是字典排序，但是看到示例二就会发现跟字典排序又不太一样，所以我们需要自定义排序函数。

错误的自定义排序函数思路：

1. 字典排序，如果开始的第一个数字不一样，那么就直接返回排序结果
2. 如果两个开始的某一连续部分数字一样，则遍历他们，直到末尾或者到达不一样的部分
3. 如果直到末尾他们的部分也都一样，则返回-1或者1都可以，他们的相对位置变动不会造成影响
4. 如果数字1先到达末尾，则与自身比较，如果数字2先到达末尾，则与自身比较

代码实现

```js
/**
 * @param {number[]} nums
 * @return {string}
 */
var largestNumber = function(nums) {
    let sum = nums.reduce((a, b) => a+b)
    if (sum === 0) {
        return '0'
    }
    nums.sort((a, b) => {
      let num1 = a + ""
      let num2 = b + ""
      let char1 = num1[0]
      let char2 = num2[0]
      let i = 0
      let l1 = num1.length
      let l2 = num2.length
      while (char1 === char2) {
          if (!num1[i] && !num2[i]) {
              break
          }
          if (i >= l1) {
              // return num2[0] < num2[i] ? 1 : -1
              return compare(num2, i)
          }
          if (i >= l2) {
              // return num1[0] >= num1[i] ? 1 : -1
              return compare1(num1, i)
          }
          char1 = num1[i]
          char2 = num2[i]
          i++
      }
      return char1 > char2 ? -1 : 1
    })
    return nums.join('')
};
function compare(str, i) {
    let j = 0
    let l = str.length - 1
    while (i < l && str[j] === str[i]) {
        j++   
        i++
    }
    return str[j] < str[i] ? 1 : -1
}
function compare1(str, i) {
    let j = 0
    let l = str.length - 1
    while (i < l && str[j] === str[i]) {
        j++   
        i++
    }
    return str[j] >= str[i] ? 1 : -1
}
```

但是，值得注意的是，这个代码实现并没有AC，具体的错误原因在第四步的具体实现。

于是，看了评论区，有了下面代码的巧妙实现

```js
/**
 * @param {number[]} nums
 * @return {string}
 */
var largestNumber = function(nums) {
    let sum = nums.reduce((a, b) => a+b)
    if (sum === 0) {
        return '0'
    }
    nums.sort((a, b) => {
       let num1 = a + ""
       let num2 = b + ""
       return Number(num1+num2) < Number(num2+num1) ? 1 : -1
    })
    return nums.join('')
};
```

这里的实现主要是想到比较 num1 + num2 和 num2 + num1的思路

总结：算法题感觉一段时间不做就会生疏好多，感觉自己要加强练习和理解原理