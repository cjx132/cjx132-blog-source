---
title: "反转链表 II"
date: 2021-02-07T00:08:55+08:00
categories:
  - leetcode
tags:
  - 字节跳动
  - 链表
---

# 题目

![image-20210207215002244](https://cjx132.github.io/picture-bed/img/image-20210207215002244.png)

# 综述

-   [参考](https://www.acwing.com/solution/content/15048/)
-   ![2020-06-20_141447.jpg](https://cdn.acwing.com/media/article/image/2020/06/20/7416_5fe9a2e4b2-2020-06-20_141447.jpg)

去除图中的第四步就是本题的代码

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
    ListNode* reverseBetween(ListNode* head, int m, int n) {
        ListNode *dummy=new ListNode(-1);
        dummy->next=head;

        ListNode *p=dummy;
        for(int i=0;i<m-1;i++)p=p->next;
        ListNode *pre=p->next,*cur=pre->next;
        
        for(int i=0;i<n-m;i++)
        {
            ListNode *t=cur->next;
            cur->next=pre;
            pre=cur;
            cur=t;
        }

        p->next->next=cur;
        p->next=pre;

        return dummy->next;
    }
};
```

# 复杂度分析

-   时间复杂度：O(n)
-   空间复杂度：O(1)
