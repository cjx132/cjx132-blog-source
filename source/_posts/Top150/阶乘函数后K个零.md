---
title: "阶乘函数后K个零"
date: 2020-11-20T14:33:19+08:00
categories:
  - Top150
tags:
  - 数学
---

# 题目

![image-20201120143640763](https://cjx132.github.io/picture-bed/img/image-20201120143640763.png)

# 综述

-   [题解参考](https://leetcode-cn.com/problems/factorial-trailing-zeroes/solution/liang-dao-lei-si-de-jie-cheng-ti-mu-xiang-jie-by-l/)
-   二分的实现确定上下限之后，使用二分模板十分简单
-   重点是本题中`trailingZeroes`函数实现的数学原理需要理解

# Code

```c++
class Solution {
public:
    int preimageSizeFZF(int K) {
        int low=search_up(K);
        int high=search_down(K);
        return high-low;
    }   

    long search_up(int K){
        long left=0;
        long right=LONG_MAX;//区间上限为LONG_MAX
        while(left<right){
            long mid=left+(right-left)/2;
            if(trailingZeroes(mid)>=K){
                right=mid;
            }else{
                left=mid+1;
            }
        }
        return left;
    }
    long search_down(int K){
        long left=0;
        long right=LONG_MAX;
        while(left<right){
            long mid=left+(right-left)/2;
            if(trailingZeroes(mid)>K){
                right=mid;
            }else{
                left=mid+1;
            }
        }
        return left;
    }
    long trailingZeroes(long n) {
        //也就是题目中的f(x)函数,是一个单调递增函数
        //因此使用二分法，确定区间上下限之后，找到满足条件的上下边界，即可求解
        long div=5;
        long ans=0;
        while(div<=n){
            ans+=n/div;
            div*=5;
        }
        return ans;
    }
};
```

# 复杂度分析

-   时间复杂度：O(logK)
