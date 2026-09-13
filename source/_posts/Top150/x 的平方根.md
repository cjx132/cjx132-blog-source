---
title: "x的平方根"
date: 2020-10-14T13:13:46+08:00
categories:
  - Top150
tags:
  - 二分
  - 字节跳动
---

# 题目

![](/images/Top150/sqrt.png)

# 综述

-   细节比较多

# Code

```c++
class Solution {
public:
    int mySqrt(int x) {
        if(x==0||x==1)return x;//防止出现除0错误
        int l=0,r=x;
        while(l<r)
        {
            int mid=l+1+(r-l)/2;//防止死循环
            if(mid>x/mid)r=mid-1;//这种判断的做法保证开平方的结果是向下取整的
            else l=mid;
        }
        return l;
    }
};
```

# 复杂度分析

-   时间复杂度：O(log(x))
-   空间复杂度：O(1)
