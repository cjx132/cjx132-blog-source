---
title: "TopK全解(待总结)"
date: 2020-10-14T22:28:09+08:00
categories:
  - Top150
tags:
  - 排序
---

# 题目

![](/images/Top100/%E5%89%8DK%E4%B8%AA%E9%AB%98%E9%A2%91%E5%85%83%E7%B4%A0.png)

# 综述

## TopK问题涉及到的算法

-   小顶堆（可在一台计算机上进行流处理）；
-   随机选择算法(快排变形，可利用分布式的思想，在不同计算机并行处理，最后合并)；
-   BFPRT算法？

## TopK分类

-   单纯的求第K大的数：[具体代码参考](https://cjx132.github.io/2020/03/25/Hot100/%E6%95%B0%E7%BB%84%E4%B8%AD%E7%9A%84%E7%AC%ACK%E4%B8%AA%E6%9C%80%E5%A4%A7%E5%85%83%E7%B4%A0/)
    
    1.  小顶堆：
        
        -   把数据依次送入小顶堆，当堆内元素个数为K+1时就弹出堆顶元素。
            
        -   最终得到的元素个数为K的小顶堆的**堆顶元素**就是答案。
            
    2.  随机选择算法：
        
        -   求第K大等价于求升序数组的**第`len-k+1`个**元素；（len为数组元素个数）
        -   可选择使用两路快排或者三路快排变形得到答案。
-   求前K大的集合：具体参考本题代码即可
    
    1.  小顶堆：
        
        -   把数据依次送入小顶堆，当堆内元素个数为K+1时就弹出堆顶元素。
            
        -   最终得到的元素个数为K的小顶堆就是答案。
            
    2.  随机选择算法：
        
        -   为了理解方便，快排改变为**降序排列**
        -   需要注意答案统计的时机：具体参考本题相关代码。

# Code

## 小顶堆写法

注意本题中的c++代码写法：map中存放的是pair<int,int>,可以直接赋值

```c++
class Solution {
public:
    struct cmp{//小顶堆写法牢记
            bool operator()(const pair<int,int>& a,const pair<int,int>& b){//不要漏写括号
                return a.second>b.second;
            }
    };
    vector<int> topKFrequent(vector<int>& nums, int k) {
        vector<int>res;
        unordered_map<int,int>mp;
        priority_queue<pair<int,int>,vector<pair<int,int> >,cmp>pq;//不要漏写cmp
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

## 快排变形写法

-   排序数组是降序
-   第K大左边的元素都是答案：注意前K大集合的统计时机（假设当前元素是第M大，M<=K时即需要统计）

```c++
class Solution {
public:
    vector<int> ans;

    vector<int> topKFrequent(vector<int>& nums, int k) {
        unordered_map<int, int> occurrences;
        for (auto& v: nums) {
            occurrences[v]++;
        }

        vector<pair<int, int>> values;
        for (auto& kv: occurrences) {//map中存放的是pair<int,int>,可以直接赋值
            values.push_back(kv);
        }

        randselect(values,k,0,values.size()-1);
        return ans;                                        
    }

    void randselect(vector<pair<int,int> >& arr,int k,int left,int right){
        swap(arr[left],arr[rand()%(right-left+1)+left]);
        pair<int,int> temp=arr[left];//这里temp必须赋值为pair类型，不能赋值为int，这里之前一直踩坑
        int low=left;
        int high=right;
        //以下排序为大数在前，和快排有所不同
        while(low<high){
            while(low<high&&arr[high].second<=temp.second)high--;
            arr[low]=arr[high];
            while(low<high&&arr[low].second>=temp.second)low++;
            arr[high]=arr[low];
        }
        arr[low]=temp;

        //下面代码是快速选择算法特有

        //arr[low]是整个区间的第low-left+1大的数:用M表示
        int M=low-left+1;

        if(k<M){
            randselect(arr,k,left,low-1);
        }else{//注意统计答案的时机：当k>=M时，包含M在内左边的元素都是答案（数组是降序的）
            
            for(int i=left;i<=low;i++){//统计前M个
                ans.push_back(arr[i].first);
            }
            //继续统计从M大到k大的答案
            if(k>M){
                randselect(arr,k-M,low+1,right);
            }
        }
    }
};
```

# 复杂度分析

-   时间复杂度：O(nlogk)
    
-   空间复杂度：O(n)
