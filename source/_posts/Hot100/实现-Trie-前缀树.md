---
title: "实现 Trie (前缀树)"
date: 2020-08-29T17:05:42+08:00
categories:
  - 剑指offer
tags:
  - 前缀树
---

# 题目

![前缀树](https://cjx132.github.io/picture-bed/img/%E5%89%8D%E7%BC%80%E6%A0%91.png)

# 综述

-   [字典树性质及应用](https://juejin.cn/post/6844903750490914829)
-   [本题题解](https://leetcode-cn.com/problems/implement-trie-prefix-tree/solution/trie-tree-de-shi-xian-gua-he-chu-xue-zhe-by-huwt/)
-   [相关代码参考](https://songlee24.github.io/2015/05/09/prefix-tree/)

# Code

```c++
class Trie {

private:
    bool isEnd;
    Trie* next[26];
public:
    /** Initialize your data structure here. */
    Trie() {
       isEnd=false;
       memset(next,0,sizeof(next));
    }
    
    /** Inserts a word into the trie. */
    void insert(string word) {
        Trie *node=this;
        for(char c:word){
            if(node->next[c-'a']==NULL){
                node->next[c-'a']=new Trie();
            }
            node=node->next[c-'a'];
        }
        node->isEnd=true;
    }
    
    /** Returns if the word is in the trie. */
    bool search(string word) {
        Trie* node=this;
        for(char c:word){
            node=node->next[c-'a'];
            if(node==NULL){
                return false;
            }
        }
        return node->isEnd;
    }
    
    /** Returns if there is any word in the trie that starts with the given prefix. */
    bool startsWith(string prefix) {
        Trie* node=this;
        for(char c:prefix){
            node=node->next[c-'a'];
            if(node==NULL){
                return false;
            }
        }
        return true;
    }
    
    string lCPrefix(){
        //求最长公共前缀（思路：将这些字符串存储到Trie树中。那么Trie树的第一个分叉口之前的单分支树的就是所求。）
        string ans;
        Trie* node=this;//调用者
        while(node&&node->isEnd==false){
            char c;
            bool f=help_lCPrefix(node,c);
            if(f){
                ans.push_back(c);
                node=node->next[c-'a'];//满足条件记录字符后，节点后移
            }else{
                break;
            }
        }
        return ans;
    }

    bool help_lCPrefix(Trie* node,char& c){
        //判断一个节点是否只有唯一一个分叉，并拿到这个唯一的分叉对应的字符
        int cnt=0;
        for(int i=0;i<26;i++){
            if(node->next[i]!=NULL){
                cnt++;
                c='a'+i;
            }
            if(cnt>1){
                return false;
            }
        }
        return true;
    }
};

/**
 * Your Trie object will be instantiated and called as such:
 * Trie* obj = new Trie();
 * obj->insert(word);
 * bool param_2 = obj->search(word);
 * bool param_3 = obj->startsWith(prefix);
 * string param_4=obj->lCPrefix();
 */
```

# 复杂度分析
