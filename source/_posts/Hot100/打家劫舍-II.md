---
title: "打家劫舍 II"
date: 2020-09-08T18:19:55+08:00
categories:
  - Top-100
tags:
  - 动态规划
---

# 题目

![](/images/Top100/%E6%89%93%E5%AE%B6%E5%8A%AB%E8%88%8D2.png)

# 综述

-   见注释

# Code

```c++
class Solution {
public:
    int rob(vector<int>& nums) {
        int len=nums.size();
        if(len==0)return 0;
        if(len==1)return nums[0];
        if(len==2)return max(nums[0],nums[1]);
        //有三种可能：选择第一个，不选择最后一个；选择最后一个，不选择第一个；两个都不选（由于全为正数，此种情况明显要小）
        return max(help(0,len-2,nums),help(1,len-1,nums));
    }
    //以下为打家劫舍1的解法
    int help(int start,int end,vector<int>& nums){
        int a=nums[start];
        int b=max(nums[start+1],nums[start]);
        for(int i=start+2;i<=end;i++){
            int t=b;
            b=max(b,nums[i]+a);
            a=t;
        }
        return b;
    }
};
```

# 复杂度分析

时间复杂度：O(n)

空间复杂度：O(1)
