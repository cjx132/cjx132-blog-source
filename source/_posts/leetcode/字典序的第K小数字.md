---
title: "字典序的第K小数字"
date: 2021-02-21T21:14:34+08:00
categories:
  - leetcode
tags:
  - 字节跳动
  - 字典树
---

# 题目

![image-20210221220429258](https://cjx132.github.io/picture-bed/img/image-20210221220429258.png)

# 综述

[参考](https://zhuanlan.zhihu.com/p/61661191)

# Code

```c++
class Solution {
public:
    int findKthNumber(int n, int k) {
        int cur=1;
        --k;

        while(k>0)
        {
            long long left=cur,right=cur+1;
            long long node_sum=0;
            while(left<=n)// 统计树中每一层的节点个数
            {
                node_sum+=min(right,(long long)n+1)-left;
                left*=10;
                right*=10;
            }
            if(node_sum<=k)// 向后查找
            {
                k-=node_sum;
                cur++;
            }
            else// 进入子树查找
            {
                k--;
                cur*=10;
            }
        }
        return cur;
    }
};
```

# 复杂度分析
