---
title: "环形链表 II"
date: 2020-08-03T16:55:52+08:00
categories:
  - Top-100
tags:
  - 链表
---

# 题目

![](/images/Top100/%E7%8E%AF%E5%BD%A2%E9%93%BE%E8%A1%A82.png)

# 综述

假设头结点到环的入口位置距离是a，环的大小是b。

快慢指针第一次相遇时，可以思考，他们一定是在环内相遇，因为有环的链表指针会一直在环内转圈。

假设相遇时，快指针走了f，慢指针走了s，则有f=s+nb；==>因为快指针比慢指针多走了n个环的大小才相遇

同时有f=2s；==>因为快指针每次走两步，慢指针每次走一步

以上可知，s=nb，**也就是说相遇时，慢指针走过的总长度是n个环的长度，慢指针再走a步就是环的入口**【重要结论】

* * *

如果此时让一个指针从头结点开始走，那么它走到环的入口位置的长度是a+nb

也就是说如果此时让慢指针再走a步，就到了环的入口，但是不知道a的大小

此时，可以设置新的指针在头结点，和慢指针一块每次走一步，他们相遇时，就走了a步，且位置正好在环的入口

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
    ListNode *detectCycle(ListNode *head) {
        ListNode* fast=head;
        ListNode* slow=head;
        while(1)
        {
            if(fast==NULL||fast->next==NULL)return NULL;
            slow=slow->next;
            fast=fast->next->next;
            if(slow==fast)break;
        }
        fast=head;//从头开始走a步
        while(fast!=slow)//再让慢指针走a步
        {
            slow=slow->next;
            fast=fast->next;
        }
        return slow;//相遇时就是环的入口位置
    }
};
```

# 复杂度分析

时间复杂度：O(n)

空间复杂度：O(1)
