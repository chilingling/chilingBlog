---
layout: post
title: 反转链表
author: 炽凌
date: 2019-04-09
categories: 剑指Offer
tags: 剑指Offer
comments: true
finished: true
---

> 定义有一个函数，输入一个链表的头节点，反转该链表并输出反转链表后的头节点。链表定义如下
>
> struct ListNode {
>
> ​    int   key;
>
> ​    ListNode*  next;
>
> }

编写出来的代码出现的问题

- 输入的链表头指针为NULL，或者整个链表之后一个节点时，程序立即崩溃
- 反转后的链表出现断裂
- 返回的反转之后的链表的头节点不是原始链表的尾节点

```cpp
ListNode* ReverseList (ListNode* pHead) {
    ListNode* pReversedHead = NULL;
    ListNode* pNode = pHead;  // 指向当前节点的指针
    ListNode* pPrev = NULL;  // 指向前一个节点的指针
    while (pNode != NULL) {  // 从头节点遍历到尾节点
        ListNode* pNext = pNode->next;  // 下一个节点的指针
        if (pNext == NULL) { // 下一个几点是NULL，表明当前节点已经是尾节点
            pReversedHead = pNode; //  将指向当前节点的指针指向将要返回的反转后的头指针
        }
        pNode->next = pPrev; // 将当前节点的下一个节点指针指向前一个节点
        pPrev = pNode;  // 将指向前一个节点的指针指向当前节点
        pNode = pNext;  // 将指向当前节点的指针指向下一个节点
    }
    return pReversedHead;
}
```

如何及时发现并代码中的问题？

- 提前想好测试用例（单元测试）
- 注意边界条件（常规输入、边界输入、错误输入）