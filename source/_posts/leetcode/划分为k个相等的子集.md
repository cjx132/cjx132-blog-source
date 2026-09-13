---
title: "划分为k个相等的子集"
date: 2021-08-12T13:13:46+08:00
categories:
  - leetcode
tags:
  - 字节跳动
  - 回溯
---

# 题目

![image-20210812220100812](https://cjx132.github.io/picture-bed/img/image-20210812220100812.png)

# 综述

先算出子集的和是多少，并抽象成k个桶，每个桶的值是子集的和。然后尝试所有不同的组合（即放数到桶中），如果存在一种组合可以使每个桶都正好放下，那么返回可以。如果不存在，返回不可以。

# Code

```c++
class Solution {
public:
    bool canPartitionKSubsets(vector<int>& nums, int k) {
        int sum=0;
        for(auto & num :nums)
            sum+=num;
        if(sum%k)return false;
        //排序 小的放最前面大的放最后面
        sort(nums.begin(),nums.end());
        int targrt=sum/k;
        //如果子集的和小于数组最大的直接返回false
        if(nums[nums.size()-1]>targrt)return false;
        vector<int>arr(k,targrt);
        return dfs(nums,arr,k,nums.size()-1);
    }
    bool dfs(vector<int>& nums, vector<int>& arr, int k, int u)
    {
        //已经遍历到了-1说明前面的所有数都正好可以放入桶里，那所有桶的值此时都为0，说明找到了结果，返回true
        if (u<0)return true;
        //遍历k个桶
        for(int i=0;i<k;i++)
        {
            //如果正好能放下当前的数或者放下当前的数后，还有机会继续放前面的数（剪枝）
            if(arr[i]==nums[u]||(arr[i]-nums[u]>=nums[0]))
            {
                //放当前的数到桶i里
                arr[i]-=nums[u];
                //开始放下一个数
                if(dfs(nums,arr,k,u-1))return true;
                //这个数不该放在桶i中
                //从桶中拿回当前的数
                arr[i]+=nums[u];
            }
        }
        return false;
    }
};
```

# 复杂度分析
