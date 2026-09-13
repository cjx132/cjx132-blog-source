---
title: "前 K 个高频元素"
date: 2020-09-09T22:07:08+08:00
categories:
  - Top-100
tags:
  - 字节跳动
  - 排序
---

# 题目

![](/images/Top100/%E5%89%8DK%E4%B8%AA%E9%AB%98%E9%A2%91%E5%85%83%E7%B4%A0.png)

# 综述

-   TopK问题：
    1.  堆（可在一台计算机上进行流处理）；
    2.  随机选择算法(快排变形，可利用分布式的思想，在不同计算机并行处理，最后合并)；
    3.  BFPRT算法？
-   快速排序：
    1.  单路快排
    2.  双路快排
    3.  三路快排（荷兰国旗问题）[参考链接](https://zhuanlan.zhihu.com/p/113548140)
-   注意本题中的c++代码写法

# Code

## 快速选择

```c++
class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        vector<int>ans;
        unordered_map<int,int>h;
        for(auto num : nums)h[num]++;
        vector<pair<int,int>>arr;
        for(auto kv:h)arr.push_back(kv);
        int index=quick_sort(arr,0,arr.size()-1,k);
        for(int i=0;i<=index;i++)ans.push_back(arr[i].first);
        return ans;
    }
    int quick_sort(vector<pair<int,int>>& arr, int l,int r, int k)
    {
        if(l==r)return l;
        int i=l-1,j=r+1;
        auto x=arr[l + r >> 1];
        while(i<j)
        {
            do i++;while(arr[i].second>x.second);
            do j--;while(arr[j].second<x.second);
            if(i<j)swap(arr[i],arr[j]);
        }
        if(k<=j-l+1)return quick_sort(arr,l,j,k);
        else return quick_sort(arr,j+1,r,k-j+l-1);
    }
};
```

## 堆

```c++
class Solution {
public:
    class cmp{//小顶堆写法牢记
        public:
            bool operator()(const pair<int,int>& a,const pair<int,int>& b){
                return a.second>b.second;
            }
    };
    vector<int> topKFrequent(vector<int>& nums, int k) {
        vector<int>res;
        unordered_map<int,int>mp;
        priority_queue<pair<int,int>,vector<pair<int,int> >,cmp>pq;
        for(int num:nums){
            mp[num]++;
        }
        auto it=mp.begin();
        while(it!=mp.end()){
            pq.push(*it);
            if(pq.size()>k){//保持pq的大小为k
                pq.pop();
            }
            it++;
        }
        while(k--){
            res.push_back(pq.top().first);
            pq.pop();
        }
        return res;                                        
    }
};
```

# 复杂度分析

时间复杂度：O(nlogk)

空间复杂度：O(n)
