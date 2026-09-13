---
title: "复原IP地址"
date: 2021-02-24T13:13:46+08:00
categories:
  - leetcode
tags:
  - 字节跳动
  - 回溯
---

# 题目

![image-20210224022438730](https://cjx132.github.io/picture-bed/img/image-20210224022438730.png)

# 综述

-   `if(x==s.size())res.push_back(t);//只有x起始位置到最后才是最终的答案`这段代码将符合题意的答案保存下来，说明每个字符都使用了

# Code

```c++
class Solution {
public:
    vector<vector<string>>res;
    vector<string>ans;
    vector<string>t;
    vector<string> restoreIpAddresses(string s) {
        dfs(s,0,0);
        for(auto item:res)//处理答案
        {
            string str="";
            for(int i=0;i<item.size();i++)
            {
                if(i)str+=".";
                str+=item[i];
            }
            ans.push_back(str);
        }
        return ans;
    }
    void dfs(string &s,int u,int x)
    {
        if(x>s.size())return;//x==s.size()不是结束条件
        if(u==4)//形成4段则可以剪枝，从其中筛选出最终的答案
        {
            if(x==s.size())res.push_back(t);//只有x起始位置到最后才是最终的答案
            return;
        }
        for(int i=1;i<=3;i++)
        {
            if(x+i-1>=s.size())break;
            string str=s.substr(x,i);
            if(stoi(str)<=255&&!(str.size()>1&&str[0]=='0'))
            {
                t.push_back(str);
                dfs(s,u+1,x+i);
                t.pop_back();
            }
        }
    }
};
```

# 复杂度分析
