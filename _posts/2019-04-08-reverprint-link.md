---
layout: post
title: 从尾到头打印列表
author: 炽凌
date: 2019-04-08
categories: 剑指Offer
tags: 剑指Offer
comments: true
finished: true
---

> 输入一个链表的头节点，从尾到头打印每个节点的值

递归法

可能存在的崩溃，当链表非常长的时候，我们的递归可能会导致溢出

```cpp
void printList (ListNode* pHead) {
    if (pHead != nullptr) { // 检测是否是空指针
        if (pHead->next != nullptr) { // 检测是否还有下一个节点
            printList(pHead->next);
        }
        cout<<p->value;
    }
}
```

迭代法：

```cpp
void printList (ListNode* pHead) {
    stack<ListNode *> nodes;
    ListNode* pNode = pHead;
    while (pNode->next != nullptr) {// 将这些节点存到栈中
        nodes.push(pNode);
        pNode = pNode->next;
    }
    while (!nodes.empty()) { // 根据栈先进后出的特点，将他们逐个弹出栈中，就实现了从尾到头打印列表了
        pNode = nodes.top();
        cout<<pNode->value;
        nodes.pop();
    }
}
```

