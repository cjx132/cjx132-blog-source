---
title: "和为s的连续正数序列"
date: 2020-03-09T00:08:02+08:00
categories:
  - 剑指offer
tags:
  - 字节跳动
  - 双指针
---

# 题目

![](/images/%E5%89%91%E6%8C%87offer/%E5%92%8C%E4%B8%BAs%E7%9A%84%E8%BF%9E%E7%BB%AD%E6%AD%A3%E6%95%B0%E5%BA%8F%E5%88%97.png)

# 综述

-   [https://leetcode-cn.com/problems/he-wei-sde-lian-xu-zheng-shu-xu-lie-lcof/solution/shi-yao-shi-hua-dong-chuang-kou-yi-ji-ru-he-yong-h/](https://leetcode-cn.com/problems/he-wei-sde-lian-xu-zheng-shu-xu-lie-lcof/solution/shi-yao-shi-hua-dong-chuang-kou-yi-ji-ru-he-yong-h/)

# Code

```c++
class Solution {
public:
    vector<vector<int>> findContinuousSequence(int target) {
        int low=1;
        int high=1;
        int sum=0;
        vector<vector<int> >res;
        while(low<=target/2){//左边界不可能比target/2大
            if(sum<target){
                sum+=high;
                high++;
            }
            else if(sum>target){
                sum-=low;
                low++;
            }
            else{
                vector<int>t;
                for(int i=low;i<high;i++)t.push_back(i);
                res.push_back(t);
                sum-=low;
                low++;
            }
        }
        return res;
    }
};
```

# 复杂度分析
