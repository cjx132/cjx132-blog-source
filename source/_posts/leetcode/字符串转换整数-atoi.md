---
title: "字符串转换整数 (atoi)"
date: 2020-10-01T10:22:44+08:00
categories:
  - leetcode
tags:
  - 字节跳动
  - 字符串
---

# 题目

![image-20201119174239169](https://cjx132.github.io/picture-bed/img/image-20201119174239169.png)

# 综述

-   简单模拟，看注释就行

# Code

```java
class Solution {
public:
    int myAtoi(string str) {
        int n=str.size();
        int i=0,j=n-1;
        while(i<n&&str[i]==' ')i++;
        while(j>=0&&str[j]==' ')j--;
        if(i>j)return 0;//空字符串或者字符串全是空格，直接结束

        if(!(isdigit(str[i])||str[i]=='-'||str[i]=='+'))return 0;//首位不合法直接结束

        bool f=0;
        if(str[i]=='-')
        {
            f=1;
            i++;
        }
        else if(str[i]=='+')i++;
        long num=0;
        while(isdigit(str[i]))
        {
            num=num*10+str[i]-'0';
            if(!f&&num>=INT_MAX)return INT_MAX;
            if(f&&-num<=INT_MIN)return INT_MIN;
            i++;
        }
        return f?-num:num;
    }
};
```

# 复杂度分析

-   时间复杂度：O(n)
    
-   空间复杂度：O(n)
