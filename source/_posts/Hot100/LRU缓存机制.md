---
title: "LRU缓存机制"
date: 2020-09-10T23:12:41+08:00
categories:
  - Top-100
tags:
  - 哈希表
  - 字节跳动
---

# 题目

![](/images/Top100/LRU%E7%BC%93%E5%AD%98%E6%9C%BA%E5%88%B6.png)

# 综述

hash用于快速定位key在链表中的位置

由于删除操作中需要前驱节点的信息，因此本题使用双向链表

最久未使用的在双向链表的头部，最近使用的在链表的尾部

get操作不能忘记需要删除之后再添加

# Code

```c++
struct Node
{
    Node *left, *right;
    int key,val;
    Node(int _k, int _v)
    {
        key=_k,val=_v;
        left=nullptr,right=nullptr;
    }
};
class LRUCache {
public:
    int c,tt;
    Node *head,*tail;
    unordered_map<int,Node*>h;
    LRUCache(int capacity) {
        c=capacity,tt=0;
        head=new Node(-1,-1);
        tail=new Node(-1,-1);
        head->right=tail,tail->left=head;
    }

    void remove(Node* t)
    {
        t->left->right=t->right;
        t->right->left=t->left;
    }
    
    void insert_to_tail(Node *t)
    {
        t->left=tail->left;
        t->right=tail;

        tail->left->right=t;
        tail->left=t;
    }

    int get(int key) {
        if(!h.count(key))return -1;
        auto t=h[key];
        remove(t);
        insert_to_tail(t);
        return t->val;
    }
    
    void put(int key, int value) {
        if(h.count(key))
        {
            auto t=h[key];
            t->val=value;
            remove(t);
            insert_to_tail(t);
        }
        else
        {
            if(tt==c)
            {
                auto t=head->right;
                h.erase(t->key);
                remove(t);
                delete t;
                tt--;
            }
            auto t= new Node(key,value);
            h[key]=t;
            insert_to_tail(t);
            tt++;
        }
    }
};

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache* obj = new LRUCache(capacity);
 * int param_1 = obj->get(key);
 * obj->put(key,value);
 */
```

# 复杂度分析

-   时间复杂度：O(1)
    
-   空间复杂度：O(n)
