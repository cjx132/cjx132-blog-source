---
title: "和为K的子数组"
date: 2020-09-06T13:21:59+08:00
categories:
  - Top-100
tags:
  - 哈希表
  - 字节跳动
---

# 题目

![](/images/Top100/%E5%92%8C%E4%B8%BAK%E7%9A%84%E5%AD%90%E6%95%B0%E7%BB%84.png)

# 综述

-   前缀和+哈希表
-   [大佬总结的套路非常清晰，一定要看一下](https://www.cnblogs.com/Draymonder/p/13192100.html)
-   **mp\[0\]=1的初始化**
-   unordered\_map的初始化，值初始化为0，查找时间复杂度为O(1)

# Code

```c++
class Solution {
public:
//前缀和+哈希表
    int subarraySum(vector<int>& nums, int k) {
        //key为前缀和，value为前缀和的个数
        unordered_map<int,int>mp;
        int sum=0;
        int ans=0;
        //特例:k为3，[3...]或[1,1,1...]
        mp[0]=1;
        for(int num:nums){
            sum+=num;
            //以当前sum位置为区间右边界，查看前面有多少个前缀和为sum-k的位置
            if(mp.count(sum-k))ans+=mp[sum-k];
            mp[sum]+=1;
        }
        return ans;
    }
};
```

# 复杂度分析

-   时间复杂度：O(n)
    
-   空间复杂度：O(n)
