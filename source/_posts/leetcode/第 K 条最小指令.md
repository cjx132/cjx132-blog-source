---
title: "第 K 条最小指令"
date: 2021-01-01T13:13:46+08:00
categories:
  - leetcode
tags:
  - 动态规划
---

# 题目

[题目链接](https://leetcode-cn.com/problems/kth-smallest-instructions/)

# 综述

-   初始化组合数
-   学习本题的编码手法，和数位dp的模板有所区别

# Code

```c++
class Solution {
public:
    const int N=35;
    long long f[35][35];
    void init(){
        for(int i=0;i<N;i++){
            for(int j=0;j<=i;j++){
                if(!j){
                    f[i][j]=1;
                    continue;
                }
                f[i][j]=f[i-1][j]+f[i-1][j-1];
            }
        }
    }
    
    string kthSmallestPath(vector<int>& destination, int k) {
        init();
        int h=destination[1];
        int v=destination[0];
        string ans;

        int n=h+v;
        for(int i=0;i<n;i++){
            if(h>0){
                int cnt=f[h+v-1][h-1];
                if(cnt<k){
                    ans+='V';
                    k-=cnt;
                    v--;
                }
                else {
                    ans+='H';
                    h--;
                }
            }
            else {
                ans+='V';
                v--;
            }
        }
        return ans;
    }
};
```

# 复杂度分析
