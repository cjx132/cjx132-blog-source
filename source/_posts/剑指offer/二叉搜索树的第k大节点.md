---
title: "二叉搜索树的第k大节点"
date: 2020-03-05T11:48:49+08:00
categories:
  - 剑指offer
tags:
  - 字节跳动
  - 树
---

# 题目

![](/images/%E5%89%91%E6%8C%87offer/%E4%BA%8C%E5%8F%89%E6%90%9C%E7%B4%A2%E6%A0%91%E7%9A%84%E7%AC%ACk%E5%A4%A7%E8%8A%82%E7%82%B9.png)

# 综述

-   二叉搜索树的逆中序遍历
-   代码注释处的剪枝
-   **因为k之前存在一个递归调用，所以必须要使用引用**。因此重新写一个方法

# Code

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    int ans=0;
    int kthLargest(TreeNode* root, int k) {
        dfs(root,k);
        return ans;
    }
    void dfs(TreeNode* root, int &k)
    {
        if(!root)return;
        dfs(root->right,k);
        k--;
        if(k==0)
        {
            ans=root->val;
            return;
        }
        dfs(root->left,k);
    }
};
```

# 复杂度分析
