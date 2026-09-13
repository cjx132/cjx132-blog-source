---
title: "路径总和 III"
date: 2020-09-07T19:48:33+08:00
categories:
  - 剑指offer
tags:
  - 树
---

# 题目

![](/images/%E5%89%91%E6%8C%87offer/%E8%B7%AF%E5%BE%84%E6%80%BB%E5%92%8C3.png)

# 综述

方法1：双递归

方法2：前缀和

# Code

```c++
// 首先先序递归遍历每个节点，再以每个节点作为起始点递归寻找满足条件的路径。
class Solution {
public:
    int count=0;
    int pathSum(TreeNode* root, int sum) {
        if(root==NULL)return 0;//终止本轮递归
        dfs(root,sum);
        pathSum(root->left,sum);
        pathSum(root->right,sum);
        return count;
    }
    //对一个结点进行路径的判断
    void dfs(TreeNode* root, int sum){
        if(root==NULL)return;
        sum-=root->val;
        if(sum==0)count++;//不应终止本轮递归 因为可能某条路径的子路径符合要求
        dfs(root->left,sum);
        dfs(root->right,sum);
    }
};
```
```c++
class Solution {
public:
    //mp中key为前缀和，value为前缀和的个数，mp初始化为0，同时key可以为负数
    unordered_map<int,int>mp;
    int res=0;
    int pathSum(TreeNode* root, int sum) {
        if(root==NULL)return 0;
        mp[0]=1;
        dfs(root,sum,0);
        return res;
    }
    void dfs(TreeNode* root, int sum,int presum){
        //presum代表root路径之前的和
        if(root==NULL)return;
        //root->val更新为包括当前节点的前缀和
        root->val+=presum;
        //这里不用判断是否root->val-sum>=0因为可能前缀和是负数
        //接下来两行代码的顺序不能错，先利用mp的值再更新mp的值
        res+=mp[root->val-sum];
        mp[root->val]++;
        dfs(root->left,sum,root->val);
        dfs(root->right,sum,root->val);
        //回溯必须要有的
        mp[root->val]--;
    }
};
```

# 复杂度分析

-   时间复杂度：O(n)
    
-   空间复杂度：O(n)
