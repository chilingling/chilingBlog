---
layout: post
title: LeetCode56——合并区间
author: 炽凌
date: 2019-10-15
categories: LeetCode
tags: LeetCode
comments: true
finished: true
---

> 给出一个区间的集合，请合并所有重叠的区间。

示例一

```
输入: [[1,3],[2,6],[8,10],[15,18]]
输出: [[1,6],[8,10],[15,18]]
解释: 区间 [1,3] 和 [2,6] 重叠, 将它们合并为 [1,6].
```

示例二

```
输入: [[1,4],[4,5]]
输出: [[1,5]]
解释: 区间 [1,4] 和 [4,5] 可被视为重叠区间。
```

这题难度是medium，个人感觉题目越短的，你就越需要去理解题意，想象出输入的边界条件。

比如：下面是一些输入

```
[[1,5],[4, 7], [6, 10], [15, 17], [16, 20]]  // 连续合并的情况
[[1, 4], [2, 3]] // 一个区间完全包裹另一个区间的情况
[] // 没有区间的情况
[[1, 4], [0, 4]] // 没有排序的情况
```

根据这些输入的用例，相信离写出通过的代码也就不远了。

##### 思路：

首先先将二维数组按第一列进行排序，后面我们就可以直接通过顺序遍历比较就可以完成了

```cpp
sort(intervals.begin(), intervals.end()); // 这样子默认按第一列进行排序
```

然后我们设置一个比较向量，跟当前遍历的向量进行比较，如果可以合并则将当前遍历的项合并进入当前比较向量中，否则，将当前比较二维数组添加到结果数组，然后将比较向量更新为当前项

代码实现

```cpp
class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        vector<vector<int>> res;
        // 没有区间
        if(intervals.size() <= 0) {
            return res;
        }
        // 按照第一列进行排序
        sort(intervals.begin(), intervals.end());
        int i = 1;
        // 比较向量
        vector<int> currentCompare{intervals[0][0], intervals[0][1]};
        // 遍历区间
        for(; i < intervals.size(); i++) {
        	// 可以合并
            if(currentCompare[1] >= intervals[i][0]) {
                currentCompare[0] = min(currentCompare[0], intervals[i][0]);
                currentCompare[1] = max(currentCompare[1], intervals[i][1]);
            } else {
            	// 不可以合并，
                res.push_back(currentCompare);
                currentCompare[0] = intervals[i][0];
                currentCompare[1] = intervals[i][1];
            }
        }
        res.push_back(currentCompare);
        return res;
    }
};
```

参考：[LeetCode](https://leetcode-cn.com/problems/merge-intervals/solution/)