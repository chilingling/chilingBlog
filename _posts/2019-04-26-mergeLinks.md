---
layout: post
title: 合并两个排序的链表
author: 炽凌
date: 2019-04-26
categories: c++
tags: 剑指Offer 链表
comments: true
finished: true
---
> 输入两个递增排序的链表，合并这两个链表并使新链表中的结点仍然是按照递增排序的。例如输入图3.7中的链表1和链表2，则合并之后的升序链表如链表3所示。链表结点定义如下：
>
> ```cpp
> struct ListNode{    
> 	int       m_nValue;    
> 	ListNode* m_pNext;
> };
> ```

合并的过程：

我们获得的有链表一和二的头指针。

先查看链表一二头结点是否为空指针，如果是，则，返回另一条指针的头结点就好

否则，复制头指针一二，作为往后遍历的指针，我们比较指针头指针一二谁比较小，谁小则谁作为合并后返回的头结点。然后继续合并两个链表中的结点。剩下的结点依然是有序的，因此合并过程与前面一样，比较当前头指针的大小。将得到的新结点和上面一步的结点连接起来。

编码实现：

```cpp
ListNode* Merge (ListNode* pHead1, ListNode* pHead2) {
    if (pHead1 == NULL) {
        return pHead2;
    } else if (pHead2 == NULL) {
        return pHead1;
    }
    ListNode* pMergedHead = NULL;
    if (pHead1->m_nValue < pHead2->m_nValue) {
            pMergedHead = pHead1;
        	pMergedHead->m_pNext = Merge(pHead1->m_pNext, pHead2);
    } else {
        pMergedHead = pHead2;
        pMergedHead->m_pNext = Merge(pHead1, pHead2->m_pNext);
    }
    return pMergedHead;
}
```

