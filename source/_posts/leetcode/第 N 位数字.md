---
title: "第 N 位数字"
date: 2021-05-08T13:13:46+08:00
categories:
  - leetcode
tags:
  - 字节跳动
  - 模拟
---

# 题目

![image-20210508101325216](C:%5CUsers%5CAUSU%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20210508101325216.png)

# 综述

![image-20210508101944691](C:%5CUsers%5CAUSU%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20210508101944691.png)

1.  先找到在哪个区间
2.  找到是区间的哪个数
3.  找到是这个数中的第几位

# Code

```c++
class Solution {
public:
    int findNthDigit(int n) {
        if(n<10)return n;

        int digit=1;
        long fac=1;
        long len=digit*fac*9;

        while(n>len)
        {
            n-=len;
            fac*=10;
            digit++;
            len=digit*fac*9;
        }

        long num=fac+(n-1)/digit;
        int index=(n-1)%digit;
        string str=to_string(num);
        return str[index]-'0';
    }
};
```

# 复杂度分析
