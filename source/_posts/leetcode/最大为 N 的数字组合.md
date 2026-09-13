---
title: "最大为 N 的数字组合"
date: 2021-01-01T00:08:54+08:00
categories:
  - leetcode
tags:
  - 动态规划
---

# 题目

![image-20210101163924569](C:%5CUsers%5CAUSU%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20210101163924569.png)

# 综述

-   数位dp
-   和windy数的去掉前导0的思路类似

# Code

```c++
class Solution {
public:
    int flag[10]={0};
    const int N=15;
    long long f[15][10]={0};
    void init(){
        for(int i=0;i<=9;i++)f[1][i]=flag[i]==1;
        for(int i=2;i<N;i++){
            for(int j=0;j<=9;j++){
                if(!flag[j])continue;
                for(int k=0;k<=9;k++){
                    if(!flag[k])continue;
                    f[i][j]+=f[i-1][k];
                }
            }
        }
    }
    int dp(int n){
        if(!n)return 0;
        
        vector<int>nums;
        while(n)nums.push_back(n%10),n/=10;

        int res=0;

        for(int i=nums.size()-1;i>=0;i--){
            int x=nums[i];
            for(int j=0;j<x;j++){
                if(flag[j]){
                    res+=f[i+1][j];
                }
            }
            if(!flag[x])break;
            if(!i)res++;
        }
        for(int i=1;i<nums.size();i++){
            for(int j=0;j<=9;j++){
                if(flag[j]){
                    res+=f[i][j];
                }
            }
        }
        return res;
    }
    int atMostNGivenDigitSet(vector<string>& digits, int n) {
        for(int i=0;i<digits.size();i++)flag[digits[i][0]-'0']=1;
        init();
        return dp(n)-dp(0);
    }
};
```

# 复杂度分析
