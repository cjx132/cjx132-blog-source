---
title: "链表中倒数第k个节点"
date: 2020-02-27T11:23:52+08:00
categories:
  - 剑指offer
tags:
  - 字节跳动
  - 链表
---

# 题目

![](/images/%E5%89%91%E6%8C%87offer/%E9%93%BE%E8%A1%A8%E4%B8%AD%E5%80%92%E6%95%B0%E7%AC%ACk%E4%B8%AA%E8%8A%82%E7%82%B9.png)

# 综述

**快慢指针**

-   定义两个指针，快指针 fast， 慢指针 low
-   让 fast 先向前移动 k 个位置，然后 low 和 fast 再一起向前移动
-   当 fast 到达链表尾部，返回 low

# Code

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* getKthFromEnd(ListNode* head, int k) {
        ListNode* fast=head;
        ListNode* low=head;
        for(int i=0;i<k;i++){
            fast=fast->next;
        }
        while(fast){
            fast=fast->next;
            low=low->next;
        }
        return low;
    }
};
```

# 复杂度分析
