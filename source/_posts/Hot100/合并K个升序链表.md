---
title: "合并K个升序链表"
date: 2020-09-12T19:37:35+08:00
categories:
  - Top-100
tags:
  - 字节跳动
  - 排序
---

# 题目

![](/images/Top100/%E5%90%88%E5%B9%B6K%E4%B8%AA%E5%8D%87%E5%BA%8F%E9%93%BE%E8%A1%A8.png)

# 综述

归并排序思路

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
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        return merge_sort(lists,0,lists.size()-1);
    }

    ListNode* merge_sort(vector<ListNode*>& lists,int l,int r)
    {
        if(l>r)return nullptr;
        if(l==r)return lists[l];
        int mid=(l+r)>>1;

        auto left=merge_sort(lists,l,mid);
        auto right=merge_sort(lists,mid+1,r);
        return merge(left,right);
    }
    ListNode* merge(ListNode *a,ListNode *b)
    {
        if(!a)return b;
        if(!b)return a;

        if(a->val<=b->val)
        {
            a->next=merge(a->next,b);
            return a;
        }
        else 
        {
            b->next=merge(a,b->next);
            return b;
        }
    }
};
```

# 复杂度分析

-   时间复杂度：O(nlogn)
    
-   **空间复杂度：O(1)**
