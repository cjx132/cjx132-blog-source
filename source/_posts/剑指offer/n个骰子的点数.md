---
title: "n个骰子的点数"
date: 2020-03-15T23:22:46+08:00
categories:
  - 剑指offer
tags:
  - 动态规划
---

# 题目

![](/images/%E5%89%91%E6%8C%87offer/n%E4%B8%AA%E9%AA%B0%E5%AD%90%E7%9A%84%E7%82%B9%E6%95%B0.png)

# 综述

[https://leetcode-cn.com/problems/nge-tou-zi-de-dian-shu-lcof/solution/nge-tou-zi-de-dian-shu-dong-tai-gui-hua-ji-qi-yo-3/](https://leetcode-cn.com/problems/nge-tou-zi-de-dian-shu-lcof/solution/nge-tou-zi-de-dian-shu-dong-tai-gui-hua-ji-qi-yo-3/)

# Code

```plain
class Solution {
public:
    vector<double> twoSum(int n) {
        // //二维数组写法
        // vector<vector<int> >dp(12,vector<int>(70,0));
        // for(int i=1;i<=6;i++)dp[1][i]=1;
        // for(int i=2;i<=11;i++){
        //     for(int j=i;j<=6*i;j++){
        //         for(int cur=1;cur<=6;cur++){
        //             if(j-cur<i-1)break;//i-1为前一轮和的最小值
        //             dp[i][j]+=dp[i-1][j-cur];
        //         }
        //     }
        // }
        // double all=pow(6,n);
        // vector<double>res;
        // for(int i=n;i<=n*6;i++){
        //     res.push_back(1.0*dp[n][i]/all);
        // }
        // return res;
        //一维数组写法
        vector<int>dp(70,0);
        for(int i=1;i<=6;i++)dp[i]=1;
        for(int i=2;i<=n;i++){
            for(int j=6*i;j>=i;j--){//这里注意倒序，否则会出现后边的累加本轮计算过的结果
                dp[j]=0;//这里注意初始化为0，否则会累加dp[j]位置存在过的值
                for(int cur=1;cur<=6;cur++){
                    if(j-cur<i-1)break;
                    dp[j]+=dp[j-cur];
                }
            }
        }
        double all=pow(6,n);
        vector<double>res;
        for(int i=n;i<=n*6;i++){
            res.push_back(1.0*dp[i]/all);
        }
        return res;
    }
};
```

# 复杂度分析
