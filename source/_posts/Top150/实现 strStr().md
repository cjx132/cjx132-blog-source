---
title: "实现 strStr()"
date: 2020-10-13T13:13:47+08:00
categories:
  - Top150
tags:
  - 字符串
---

# 题目

![](/images/Top150/strStr.png)

# 综述

-   KMP，解释见算法笔记455页
-   可从有限状态自动机的角度理解
-   next数组的含义以及优化

# Code

```c++
class Solution {
public:
    int strStr(string haystack, string needle) {
        int m=haystack.size();
        int n=needle.size();
        if(n==0)return 0;//本题特判

        vector<int>next=getNext(needle,n);
        int j=-1;

        for(int i=0;i<m;i++){
            if(j!=-1&&haystack[i]!=needle[j+1]){
                j=next[j];
            }
            if(haystack[i]==needle[j+1]){
                j++;
            }
            if(j==n-1){//匹配结束
                return i-n+1;//i此时为haystack匹配串中的最后一位，i-n+1是匹配串的开始位置下标

                //以下可用于统计匹配的次数
                // ans++;
                // j=next[j];//让j回退到next[j]位置继续匹配
            }
        }
        return -1;
    }

//求next数组
    vector<int> getNext(string str,int len){
        vector<int> next(len,0);
        next[0]=-1;
        int j=-1;

        for(int i=1;i<len;i++){
            if(j!=-1&&str[i]!=str[j+1]){
                j=next[j];
            }
            if(str[i]==str[j+1]){
                j++;
            }
            //以下为next数组的优化，可快速回退，此时代码中的两个while均只执行一次，可以换为if
            if(j==-1||str[i+1]!=str[j+1]){
                next[i]=j;
            }else{
                next[i]=next[j];
            }
            
        }
        
        return next;
    }
};
```

# 复杂度分析

-   时间复杂度：O(m+n)
-   空间复杂度：O(n)
