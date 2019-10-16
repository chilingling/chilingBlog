---
layout: post
title: LeetCode75——颜色分类
author: 炽凌
date: 2019-10-16
categories: LeetCode
tags: LeetCode
comments: true
finished: true
---
> 给定一个包含红色、白色和蓝色，一共 n 个元素的数组，原地对它们进行排序，使得相同颜色的元素相邻，并按照红色、白色、蓝色顺序排列。
>
> 此题中，我们使用整数 0、 1 和 2 分别表示红色、白色和蓝色。
>
> 注意:
> 不能使用代码库中的排序函数来解决这道题。

示例：

```
输入: [2,0,2,1,1,0]
输出: [0,0,1,1,2,2]
```

无脑的解法：使用内置排序算法

#### 计数排序解法：

第一遍遍历：分别计算有多少个0、1、2

第二遍：对原数组分别填充计算所得的0、1、2个数

实现：

```cpp
class Solution {
public:
    void sortColors(vector<int>& nums) {
        int redNums = 0; // 存储有多少个0
        int whiteNums = 0; // 有多少个1
        int blueNums = 0; // 有多少个2
        for(int i = 0; i < nums.size(); i++) {
            if(nums[i] == 0) {
                redNums++;
            } else if (nums[i] == 1) {
                whiteNums++;
            } else {
                blueNums++;
            }
        }
        int i = 0;
        // 填充0
        while(redNums > 0) {
            nums[i] = 0;
            redNums--;
            i++;
        }
        // 填充1
        while(whiteNums > 0) {
            nums[i] = 1;
            whiteNums--;
            i++;
        }
        // 填充2
        while(blueNums > 0) {
            nums[i] = 2;
            blueNums--;
            i++;
        }
    }
};
```

多指针解法：

使用两个指针分别指向跟0和2相邻接的一位，用一个遍历指针遍历数组，当遍历指针大于等于指向2的指针的位置时，结束遍历，排序完成

实现：

```cpp
class Solution {
public:
    void sortColors(vector<int>& nums) {
        int redPoint = 0; // 指向跟0相邻接的一位，初始化为0
        int bluePoint = nums.size() - 1; // 指向跟2相邻接的一位，初始化为最后一个位置
        int current = 0; // 遍历指针
        // 遍历结束/完成条件    遍历指针大于跟2相邻的指针
        while(current <= bluePoint) {
            if(nums[current] == 0) { // 如果当前位是0，则跟邻接0的指针交换
                nums[current] = nums[redPoint];
                nums[redPoint] = 0;
                redPoint++;
                current++;
            } else if(nums[current] == 1){ // 如果是1，则继续遍历
                current++;
            } else if(nums[current] == 2) { // 如果当前位是2，则跟邻接2的指针交换
                nums[current] = nums[bluePoint];
                nums[bluePoint] = 2;
                bluePoint--;
            }
        }
    }
};
```

更详细的解读及动画演示，请参考[官方题解](https://leetcode-cn.com/problems/sort-colors/solution/yan-se-fen-lei-by-leetcode/)