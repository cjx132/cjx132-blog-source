---
title: "和至少为 K 的最短子数组"
date: 2021-02-23T13:13:46+08:00
categories:
  - leetcode
tags:
  - 字节跳动
  - 单调队列
---

# 题目

![image-20210223013935856](https://cjx132.github.io/picture-bed/img/image-20210223013935856.png)

# 综述

-   [参考](https://www.acwing.com/solution/content/612/)
-   假如当前元素小于等于队尾元素，当前元素靠右且小，更容易满足 `s(j)+K≤s(i)`，弹出队尾元素，直到当前元素大于队尾元素，此时把当前元素加入队尾，因此队列是一个单调递增的队列
-   之后，从队头开始，满足条件则不断更新最小值

# Code

```c++
class Solution {
public:
    int s[50010],q[50010];
    int shortestSubarray(vector<int>& A, int K) {
        int n=A.size();
        for(int i=1;i<=n;i++)//前缀和数组
            s[i]+=s[i-1]+A[i-1];
        
        int hh=0,tt=-1,res=n+2;
        for(int i=0;i<=n;i++)
        {
            while(hh<=tt&&s[i]<=s[q[tt]])
                --tt;
            q[++tt]=i;
            while(hh<=tt&&s[i]-s[q[hh]]>=K)
            {
                res=min(res,i-q[hh]);
                ++hh;
            }
        }
        if(res==n+2)return -1;
        return res;
    }
};
```

# 复杂度分析
