---
title: "包含min函数的栈"
date: 2020-03-22T00:20:12+08:00
categories:
  - 剑指offer
tags:
  - 字节跳动
  - 栈
---

# 题目

![](/images/%E5%89%91%E6%8C%87offer/%E5%8C%85%E5%90%ABmin%E5%87%BD%E6%95%B0%E7%9A%84%E6%A0%88.png)

# 综述

stk\_min保存前i个stk栈的最小值，只用在push操作的时候，当前元素和stk\_min栈顶元素比较后插入

# Code

```c++
class MinStack {
public:
    /** initialize your data structure here. */
    stack<int>stk,stk_min;
    MinStack() {

    }
    
    void push(int x) {
        stk.push(x);
        if(stk_min.size())x=min(stk_min.top(),x);
        stk_min.push(x);
    }
    
    void pop() {
        stk.pop();
        stk_min.pop();
    }
    
    int top() {
        return stk.top();
    }
    
    int getMin() {
        return stk_min.top();
    }
};

/**
 * Your MinStack object will be instantiated and called as such:
 * MinStack* obj = new MinStack();
 * obj->push(x);
 * obj->pop();
 * int param_3 = obj->top();
 * int param_4 = obj->getMin();
 */
```

# 复杂度分析
