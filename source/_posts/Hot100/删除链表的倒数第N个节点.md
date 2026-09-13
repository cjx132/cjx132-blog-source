---
title: "删除链表的倒数第N个节点"
date: 2020-08-20T13:44:37+08:00
categories:
  - Top-100
tags:
  - 链表
---

# 题目

![](/images/Top100/%E5%88%A0%E9%99%A4%E9%93%BE%E8%A1%A8%E7%9A%84%E5%80%92%E6%95%B0%E7%AC%ACN%E4%B8%AA%E8%8A%82%E7%82%B9.png)

# 综述

# Code

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        ListNode *dummy=new ListNode(-1);
        dummy->next=head;

        ListNode* first=dummy,*second=dummy;
        for(int i=0;i<n;i++)first=first->next;

        while(first->next)
        {
            first=first->next;
            second=second->next;
        }

        second->next=second->next->next;
        return dummy->next; 
    }
};
```

# 复杂度分析

时间复杂度：O(n)

空间复杂度：O(1)
