---
title: "LFU 缓存"
date: 2021-08-12T00:08:54+08:00
categories:
  - leetcode
tags:
  - 哈希表
  - 字节跳动
---

# 题目

![image-20210812220822174](https://cjx132.github.io/picture-bed/img/image-20210812220822174.png)

# 综述

[看视频题解](https://leetcode-cn.com/problems/lfu-cache/solution/lfuhuan-cun-by-leetcode-solution/)

# Code

```c++
struct Node{
    int key,val,freq;
    Node(int _key,int _val,int _freq):key(_key),val(_val),freq(_freq){}
};
class LFUCache {
public:
    int capacity,min_freq;
    unordered_map<int,list<Node>>freq_table;
    unordered_map<int,list<Node>::iterator>key_table;
    LFUCache(int _capacity) {
        min_freq=0;
        capacity=_capacity;
        freq_table.clear();
        key_table.clear();
    }
    
    int get(int key) {
        if(capacity==0)return -1;
        if(!key_table.count(key))return -1;
        auto node=key_table[key];
        int val=node->val,freq=node->freq;
        //1.删除该节点
        freq_table[freq].erase(node);
        if(freq_table[freq].size()==0)
        {
            freq_table.erase(freq);
            if(min_freq==freq)min_freq+=1;
        }
        //2.把该节点加入到freq_table[freq+1]双向链表的头部
        freq_table[freq+1].push_front(Node(key,val,freq+1));
        key_table[key]=freq_table[freq+1].begin();
        return val;    
    }
    
    void put(int key, int value) {
        if(capacity==0)return;
        if(key_table.count(key))
        {
            auto node=key_table[key];
            int freq=node->freq;
            //1.删除该节点
            freq_table[freq].erase(node);
            if(freq_table[freq].size()==0)
            {
                freq_table.erase(freq);
                if(min_freq==freq)min_freq+=1;
            }
            //2.把该节点加入到freq_table[freq+1]双向链表的头部
            freq_table[freq+1].push_front(Node(key,value,freq+1));
            key_table[key]=freq_table[freq+1].begin();
        }
        else
        {
            if(key_table.size()==capacity)
            {
                auto node=freq_table[min_freq].back();
                key_table.erase(node.key);
                freq_table[min_freq].pop_back();
                if(freq_table[min_freq].size()==0)freq_table.erase(min_freq);
            }
            freq_table[1].push_front(Node(key,value,1));
            key_table[key]=freq_table[1].begin();
            min_freq=1;
        }
    }
};

/**
 * Your LFUCache object will be instantiated and called as such:
 * LFUCache* obj = new LFUCache(capacity);
 * int param_1 = obj->get(key);
 * obj->put(key,value);
 */
```

# 复杂度分析
