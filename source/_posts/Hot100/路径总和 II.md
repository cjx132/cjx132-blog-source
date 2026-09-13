---
title: "路径总和 II"
date: 2020-03-14T20:53:24+08:00
categories:
  - 剑指offer
tags:
  - 字节跳动
  - 回溯
---

# 题目

![](/images/%E5%89%91%E6%8C%87offer/%E4%BA%8C%E5%8F%89%E6%A0%91%E4%B8%AD%E5%92%8C%E4%B8%BA%E6%9F%90%E4%B8%80%E5%80%BC%E7%9A%84%E8%B7%AF%E5%BE%84.png)

# 综述

-   递归函数参数中的**引用变量**和**全局变量**都会随着递归全局变化，因此对于这些变量的改变要在递归语句之后进行回溯操作，恢复这些变量之前的值。
-   递归函数中的普通变量则不用。
-   **本题的小细节：dfs方法保证每次进入的节点都是非空的，不然会出错**

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
    vector<vector<int> >res;
    vector<int>t;
    vector<vector<int>> pathSum(TreeNode* root, int sum) {
        if(!root)return  res;
        dfs(root,sum);
        return res;
    }
    void dfs(TreeNode* root,int sum){
        t.push_back(root->val);
        sum-=root->val;
        if(sum==0&&root->right==NULL&&root->left==NULL){
            res.push_back(t);
            return;
        }
        if(root->left){
            dfs(root->left,sum);
            t.pop_back();
        }
        if(root->right){
            dfs(root->right,sum);
            t.pop_back();
        }
    }
};
```

# 复杂度分析
