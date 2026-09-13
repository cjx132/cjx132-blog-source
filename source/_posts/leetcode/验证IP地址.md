---
title: "验证IP地址"
date: 2021-02-23T13:13:47+08:00
categories:
  - leetcode
tags:
  - 字节跳动
  - 双指针
---

# 题目

![image-20210223225356131](https://cjx132.github.io/picture-bed/img/image-20210223225356131.png)

# 综述

**双指针反转单词的模板快速写出**

之后，注意代码中的注释部分

# Code

```c++
class Solution {
public:
    int ip4or6(string &IP)
    {
        for(int i=0;i<IP.size();i++)
        {
            if(IP[i]=='.')return 0;
            if(IP[i]==':')return 1;
        }
        return 0;
    }

    bool judge4(string &str,int i,int j)
    {
        if(j-i+1>3)return false;//单个字符串的长度要满足要求
        if(i!=j&&str[i]=='0')return false;//有多位并且首位是0才不满足，如果只有1位，并且这一位是0，是满足条件的
        int num=0;
        for(int k=i;k<=j;k++)
        {
            if(!isdigit(str[k]))return false;
            num=num*10+str[k]-'0';
        }
        return num<=255;
    }

    bool judge6(string &str,int i,int j)
    {
        if(j-i+1>4)return false;//单个字符串的长度要满足要求
        for(int k=i;k<=j;k++)
        {
            if(!isdigit(str[k])&&!(str[k]>='a'&&str[k]<='f')&&!(str[k]>='A'&&str[k]<='F'))
                return false;
        }
        return true;
    }

    string validIPAddress(string IP) {
        int n=IP.size();
        if(n==0)return "Neither";//空字符串特判
        char split=ip4or6(IP)==0?'.':':';
        if(IP[n-1]==split)return "Neither";//特殊处理最后一位是分割符的情况

        int cnt=0;//记录有几段
        for(int i=0;i<n;i++)//双指针反转单词的模板
        {
            if(IP[i]==split)return "Neither";//处理IP开头就是分隔符，或者两个分隔符相邻的情况
            int j=i;
            while(j<n&&IP[j]!=split)j++;
            if(split=='.')
            {
                cnt++;
                if(cnt>4)return "Neither";
                if(!judge4(IP,i,j-1))return "Neither";
            }
            else
            {
                cnt++;
                if(cnt>8)return "Neither";
                if(!judge6(IP,i,j-1))return "Neither";
            }
            i=j;
        }
        if(split=='.'&&cnt!=4)return "Neither";//个数不满足要求就结束
        if(split==':'&&cnt!=8)return "Neither";
        return split=='.'?"IPv4":"IPv6";
    }
};
```

# 复杂度分析
