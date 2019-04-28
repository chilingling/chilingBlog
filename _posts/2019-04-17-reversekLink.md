---
layout: post
title: 链表中倒数第K个结点
author: 炽凌
date: 2019-04-17
categories: c++
tags: 剑指Offer 链表
comments: true
finished: true
---

> 输入一个链表，输出该链表中倒数第K个结点，为了符合大多数人的习惯，本题从1开始计数，即链表的尾结点是倒数第一个结点，例如一个链表有6个结点，从头结点开始他们的值依次是`1,2,3,4,5,6`。这个链表的倒数第三个结点是值为4的结点

链表结点定义：

```cpp
struct ListNode {
    int m_nValue;
    ListNode* m_pNext;
}
```

常规思路的解法：

- `先走到链表的尾端，再从尾端回溯K步`。问题：单向链表不能回溯
- 倒数第K个结点就是从头结点开始的第`n-k+1`个结点，如果能得到链表中的结点个数n,那么从n-k+1步就可以了，总共遍历链表两次，第一次为了得到链表结点数量，第二次为了走到倒数第K个结点

**一次遍历的方法**

定义两个指针，第一个指针从链表的头指针开始遍历向前走`k-1`步，第二个指针保持不动；从第K步开始，第二个指针也开始从链表的头指针开始遍历。这样子，两个指针的距离就保持在了`K-1`了，当第一个指针走到尾结点的时候，第二个结点自然就走到了倒数第K个结点处。

```cpp
ListNode* FindKthToTail (ListNode* pListHead, unsigned int k) {
    if (pListHead == NULL || k == 0) {
        return NULL;
    }
    ListNode *pAhead = pListHead;
    ListNode *pBehind = NULL;
    for (unsigned int i = 0; i < k - 1; ++i) { // 第一个指针先走K-1步
        if (pAhead->m_pNext != NULL) {
            pAhead = pAhead->m_pNext;
        } else {
            return NULL
        }
    }
    pBehind = pListHead; // 从第K步开始，第一个指针和第二个指针一起走
    while (pAhead->m_pNext != NULL) {
        pAhead = pAhead->m_pNext;
        pBehind = pBehind = pBehind->m_pNext;
    }
    return pBehind;
}
```

反思：

`用一个指针遍历链表不能解决问题时，可以尝试用两个指针来遍历链表。可以让其中一个指针遍历的速度快一些，或者先让它先在链表上走若干步`