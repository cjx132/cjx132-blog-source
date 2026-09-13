---
title: "用 Rand7() 实现 Rand10()"
date: 2021-02-07T13:13:47+08:00
categories:
  - leetcode
tags:
  - 数学
---

# 题目

![image-20210207012803210](https://cjx132.github.io/picture-bed/img/image-20210207012803210.png)

# 综述

-   核心在于构造等概率分布
-   (rand\_X()-1)_X+rand\_X()可以生成\`X_X\`之间的随机数

# Code

```c++
// The rand7() API is already defined for you.
// int rand7();
// @return a random integer in the range 1 to 7

class Solution {
public:
    int rand10() {
        while(1)
        {
            int res=(rand7()-1)*7+rand7();//实现rand49()
            if(res<=40)return res%10+1;//尽可能使得判断的条件大且是10的倍数
        }
    }
};
```

# 复杂度分析
