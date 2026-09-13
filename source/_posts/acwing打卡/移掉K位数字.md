---
title: "移掉K位数字"
date: 2021-01-02T23:24:21+08:00
categories:
  - acwing力扣打卡第二期
tags:
  - 字节跳动
  - 贪心
---

# 题目

![image-20210102170748908](https://cjx132.github.io/picture-bed/img/image-20210102170748908.png)

# 综述

-   **经典贪心问题，常考面试题**
-   删除k位剩下的位数是一定的。分情况讨论一下：1. 如果num字符串是递增的，肯定是要倒着删；2.如果递增过程中出现一个降序的，我需要把字符串前面的**大于**当前这个降序元素的字符全部删掉；3.这个过程就类似维护了一个单调递增栈；4.最后栈中剩下的就是递增的，此时倒着删；5.最后处理一下前导0就行

# Code

```c++
class Solution {
public:
    string removeKdigits(string num, int k) {
        string res;//当作单调栈处理
        for(char c:num){
            while(res.size()>0&&k&&res.back()>c){
                res.pop_back();
                k--;
            }
            res.push_back(c);
        }
        while(res.size()>0&&k--)res.pop_back();
        int i=0;
        while(i<res.size()&&res[i]=='0')i++;//处理前导0
        if(i==res.size())return "0";//特判
        return res.substr(i);
    }
};
```

# 复杂度分析
