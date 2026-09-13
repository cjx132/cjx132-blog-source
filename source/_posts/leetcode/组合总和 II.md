---
title: "组合总和2"
date: 2021-02-20T00:08:54+08:00
categories:
  - leetcode
tags:
  - 字节跳动
  - 回溯
---

# 题目

![image-20210220002532820](https://cjx132.github.io/picture-bed/img/image-20210220002532820.png)

# 综述

排序之后可以剪枝`if(target<candidates[i])break;`

排序之后可以去重`if(i>st&&candidates[i]==candidates[i-1]) continue;`

![按照图片搜索](https://cjx132.github.io/picture-bed/img/image-20210221161227139.png)

# Code

```c++
class Solution {
public:
    vector<vector<int>>res;
    vector<int>t;
    vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
        sort(candidates.begin(),candidates.end());
        dfs(candidates,target,0);
        return res;
    }
    void dfs(vector<int>& candidates, int target,int st)
    {
        if(target==0)
        {
            res.push_back(t);
            return;
        }
        for(int i=st;i<candidates.size();i++)
        {
            if(target<candidates[i])break;//排序的剪枝
            if(i>st&&candidates[i]==candidates[i-1])//排序之后的去重
                continue;
            t.push_back(candidates[i]);
            dfs(candidates,target-candidates[i],i+1);//注意这里是i+1
            t.pop_back();
        }
    }
};
```

# 复杂度分析
