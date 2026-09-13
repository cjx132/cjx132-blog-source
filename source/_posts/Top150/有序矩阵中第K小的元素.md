---
title: "有序矩阵中第K小的元素"
date: 2020-10-16T13:31:01+08:00
categories:
  - Top150
tags:
  - 二分
---

# 题目

![](/images/Top150/%E6%9C%89%E5%BA%8F%E7%9F%A9%E9%98%B5%E4%B8%AD%E7%AC%ACK%E5%B0%8F%E7%9A%84%E5%85%83%E7%B4%A0.png)

# 综述

-   [题解参考](https://leetcode-cn.com/problems/kth-smallest-element-in-a-sorted-matrix/solution/you-xu-ju-zhen-zhong-di-kxiao-de-yuan-su-by-leetco/)

# Code

```c++
class Solution {
public:
    int kthSmallest(vector<vector<int>>& matrix, int k) {//参见二分模板理解
        int m=matrix.size();
        int n=matrix[0].size();
        int low=matrix[0][0];//左上角
        int high=matrix[m-1][n-1];//右下角
        while(low<high){//二分模板
            int mid=low+(high-mid)/2;
            if(check(matrix,k,m,n,mid)){
                high=mid;
            }else {
                low=mid+1;
            }
        }
        return low;
    }

    bool check(vector<vector<int>>& matrix, int k,int m,int n,int target){//参见二维数组中的查找
        int num=0;
        int i=m-1;
        int j=0;//从左下角开始统计
        while(i>=0&&j<n){
            if(matrix[i][j]<=target){
                num+=i+1;//i+1为当前列的所有满足条件的个数
                j++;
            }else{
                i--;
            }
        }
        return num>=k;
    }
};
```

# 复杂度分析

-   时间复杂度：O(nlog(high−low))，二分查找进行次数为 O(log(high-low))，每次操作时间复杂度为 O(n)
-   空间复杂度：O(1)
