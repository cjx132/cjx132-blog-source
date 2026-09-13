---
title: "打家劫舍 III"
date: 2020-09-08T19:13:27+08:00
categories:
  - Top-100
tags:
  - 动态规划
---

# 题目

![](/images/Top100/%E6%89%93%E5%AE%B6%E5%8A%AB%E8%88%8D3.png)

# 综述

-   树的问题，很多时候需要先知道叶子结点的信息，然后才能决定当前结点的信息是什么，所以这种遍历顺序就是后序遍历，这道题还有第 236 题：[二叉树的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/) 都是后序遍历。二叉树里的问题，不外乎就是前序、中序、后序遍历、还有广度优先遍历，后序遍历其实出现的概率更多一点。这种遍历的思想比较重要，在解决一些问题的时候很有用。
-   [https://leetcode-cn.com/problems/house-robber-iii/solution/shu-xing-dp-ru-men-wen-ti-by-liweiwei1419/](https://leetcode-cn.com/problems/house-robber-iii/solution/shu-xing-dp-ru-men-wen-ti-by-liweiwei1419/)

# Code

```c++
class Solution {
    //记忆化递归方法
public:
    unordered_map<TreeNode*,int>mp;
    int rob(TreeNode* root) {
        if(root==NULL)return 0;
        if(mp.count(root))return mp[root];
        //偷四个孙子
        int do_=root->val+(root->left?rob(root->left->left)+rob(root->left->right):0)+(root->right?rob(root->right->left)+rob(root->right->right):0);
        //偷两个孩子
        int undo=rob(root->left)+rob(root->right);
        int res=max(do_,undo);
        mp[root]=res;
        return res;
    }
};
```
```c++
class Solution {// 树的后序遍历
public:
    int rob(TreeNode* root) {
        vector<int>ans=dfs(root);
        return max(ans[0],ans[1]);
    }
    vector<int> dfs(TreeNode*node){
        vector<int>dp(2,0);
        if(node==NULL)return dp;
        // 分类讨论的标准是：当前结点偷或者不偷
        // 由于需要后序遍历，所以先计算左右子结点，然后计算当前结点的状态值
        vector<int>left=dfs(node->left);
        vector<int>right=dfs(node->right);
        //dp[0]代表不偷当前节点时的最大值
        dp[0]=max(left[0],left[1])+max(right[0],right[1]);
        //dp[1]代表偷当前节点时的最大值
        dp[1]=node->val+left[0]+right[0];
        return dp;
    }
};
```

# 复杂度分析

时间复杂度：O(n)

空间复杂度：O(1)
