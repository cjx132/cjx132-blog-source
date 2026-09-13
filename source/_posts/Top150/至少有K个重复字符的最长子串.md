---
title: "至少有K个重复字符的最长子串"
date: 2020-11-26T00:41:18+08:00
categories:
  - Top150
tags:
  - 递归
---

# 题目

![image-20201126004258647](https://cjx132.github.io/picture-bed/img/image-20201126004258647.png)

# 综述

分治：分解为完全相同的子问题，最后合并子问题求解最终答案。可理解为后序遍历

思路：先用hash表统计s中每个字符出现的次数，显然如果字符c出现的次数小于k，c必然不在最长子串里面，根据这个特性可以将原始s分割成多个子串递归地求解问题，我们用一个split数组依次来存放每个分割点的索引，对每个分割区间同样求解该问题(多路的分治问题)，并取结果的最大值保存在变量ans中，此处有一个小trick（如果当前求解的子串长度比已存在的ans还要小，则没有必要求解该区间，这样可以减少不必要的计算），最后递归的结束点就是当前求解的字符串s符合最长子串的要求。

# Code

```c++
class Solution {
public:
    int longestSubstring(string s, int k) {
        unordered_map<char,int>mp;
        for(char c:s){
            mp[c]++;
        }

        vector<int>split;//分割点，划分点
        for(int i=0;i<s.size();i++){
            if(mp[s[i]]<k){
                split.push_back(i);
            }
        }

        int ans=0;//默认是0
        if(split.size()==0)return s.size();//元素全部大于等于k，返回结果（递归结束条件）
        
        int left=0;
        int len=0;
        split.push_back(s.size());//与下面注释相呼应
        for(int i=0;i<split.size();i++){
            len=split[i]-left;
            if(len>ans){//一个剪枝操作
                ans=max(ans,longestSubstring(s.substr(left,len),k));
            }
            left=split[i]+1;
        }
        //可以直接在split中加入s.size(),效果一样
        //ans=max(ans,longestSubstring(s.substr(left,s.size()-left),k));
        return ans;
    }
};
```

# 复杂度分析
