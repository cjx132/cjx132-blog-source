---
title: "剪绳子II"
date: 2020-03-11T23:46:04+08:00
categories:
  - 剑指offer
tags:
  - 贪心
---

# 题目

![](/images/%E5%89%91%E6%8C%87offer/%E5%89%AA%E7%BB%B3%E5%AD%90II.png)

# 综述

[https://leetcode-cn.com/problems/jian-sheng-zi-ii-lcof/solution/javatan-xin-si-lu-jiang-jie-by-henrylee4/](https://leetcode-cn.com/problems/jian-sheng-zi-ii-lcof/solution/javatan-xin-si-lu-jiang-jie-by-henrylee4/)

# Code

```c++
class Solution {
public:
    int M=(int)1e9+7;//1e9为浮点型，需要强制转换

    long long binaryPow(long long a,long long b){//快速幂模板
        long long res=1;
        while(b){
            if(b&1)res=res*a%M;
            b>>=1;
            a=a*a%M;
        }
        return res;
    }

    int cuttingRope(int n) {

        if(n==2)return 1;
        if(n==3)return 2;
        //最后每段绳长只能有：3,2（并且先分3，再分2）
        //不能有：1

        int b=n/3;//包含多少个3;
        int m=n%3;

        if(m==0)return binaryPow(3,b);

        else if(m==1)//由于不能出现1，所以拿走一个3，再加上1组成4
        {
            int t= binaryPow(3,b-1)*4%M;
            return t;
        }

        else {
            int t= binaryPow(3,b)*2%M;
            return t;
        }
        
    }
    
};
```

# 复杂度分析
