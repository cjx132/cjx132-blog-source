---
title: "和为s的两个数字"
date: 2020-03-09T00:09:48+08:00
categories:
  - 剑指offer
tags:
  - 双指针
---

# 题目

![](/images/%E5%89%91%E6%8C%87offer/%E5%92%8C%E4%B8%BAs%E7%9A%84%E4%B8%A4%E4%B8%AA%E6%95%B0%E5%AD%97.png)

# 综述

-   双指针

# Code

```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        vector<int>res;
        int low=0;
        int high=nums.size()-1;
        while(low<high){
            if(nums[low]+nums[high]<target)low++;
            else if(nums[low]+nums[high]>target)high--;
            else{
                res.push_back(nums[low]);
                res.push_back(nums[high]);
                return res;
            }
        }
        return res;
    }
};
```

# 复杂度分析
