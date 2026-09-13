---
title: "关于计算1至N中X出现的个数"
date: 2020-03-25T12:40:45+08:00
categories:
  - 剑指offer
tags:
  - 数学
---

# 题目

![](/images/%E5%89%91%E6%8C%87offer/%E5%85%B3%E4%BA%8E%E8%AE%A1%E7%AE%971%E8%87%B3N%E4%B8%ADX%E5%87%BA%E7%8E%B0%E7%9A%84%E4%B8%AA%E6%95%B0.png)

# 综述

[https://www.leetao94.cn/post/%E8%AE%A1%E7%AE%971-866278171%E4%B8%AD%E5%A5%87%E6%95%B0%E5%88%97%E4%B8%AD3%E5%87%BA%E7%8E%B0%E7%9A%84%E4%B8%AA%E6%95%B0](https://www.leetao94.cn/post/计算1-866278171中奇数列中3出现的个数)

# Code

```c++
class Solution {
public:
    int countDigitOne(int n) {
        int num=n;
        long res=0;
        long weight=1;//当前位的权重
        while(num){
            long a=(num/10)*weight;//当前位左边数字乘上权重
            if(num%10<1){
                res+=a;
            }else if(num%10==1){
                res+=a+n%weight+1;
            }else if(num%10>1){
                res+=a+weight;
            }
            num/=10;
            weight*=10;
        }
        return res;
    }
};
```

# 复杂度分析
