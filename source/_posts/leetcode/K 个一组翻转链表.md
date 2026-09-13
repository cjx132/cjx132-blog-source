---
title: "K 个一组翻转链表"
date: 2021-02-07T00:08:54+08:00
categories:
  - leetcode
tags:
  - 字节跳动
  - 链表
---

# 题目

![image-20210207213105498](https://cjx132.github.io/picture-bed/img/image-20210207213105498.png)

# 综述

-   [参考](https://www.acwing.com/solution/content/15048/)
-   ![思路](https://cdn.acwing.com/media/article/image/2020/06/20/7416_5fe9a2e4b2-2020-06-20_141447.jpg)

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
    ListNode* reverseKGroup(ListNode* head, int k) {
        ListNode *dummy=new ListNode(-1);
        dummy->next=head;

        ListNode* p=dummy;
        while(1)
        {
            ListNode *q=p;
            for(int i=0;q&&i<k;i++)q=q->next;
            if(!q)break;

            ListNode *pre=p->next,*cur=pre->next;
            for(int i=0;i<k-1;i++)
            {
                ListNode *t=cur->next;
                cur->next=pre;
                pre=cur;
                cur=t;
            }

            auto t=p->next;
            t->next=cur;

            p->next=pre;
            p=t;
        }
        return dummy->next;
    }
};
```

# 复杂度分析
