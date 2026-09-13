---
title: "数组中的第K个最大元素"
date: 2020-03-25T16:09:13+08:00
categories:
  - 剑指offer
tags:
  - 字节跳动
  - 排序
---

# 题目

![](/images/%E5%89%91%E6%8C%87offer/%E6%95%B0%E7%BB%84%E4%B8%AD%E7%9A%84%E7%AC%ACK%E4%B8%AA%E6%9C%80%E5%A4%A7%E5%85%83%E7%B4%A0.png)

# 综述

-   随机选择算法：算法笔记149页；写之前需参考：**快排全解**这篇笔记。、
-   小顶堆写法
    1.  把数据依次送入小顶堆，当堆内元素个数为K+1时就弹出堆顶元素。
    2.  最终得到的元素个数为K的小顶堆的**堆顶元素**就是答案。

# Code

## 随机选择算法

### 双路partition写法

```c++
class Solution {
public:
    int findKthLargest(vector<int>& arr, int k) {
        int nk=arr.size()-k+1;//第k个最大的元素相当于排序数组的第nk个元素
        return randselect(arr,nk,0,arr.size()-1);
    }

    int randselect(vector<int>& arr,int nk,int left,int right){
        swap(arr[left],arr[rand()%(right-left+1)+left]);
        int temp=arr[left];
        int low=left;
        int high=right;
        while(low<high){
            while(low<high&&arr[high]>=temp)high--;
            arr[low]=arr[high];
            while(low<high&&arr[low]<=temp)low++;
            arr[high]=arr[low];
        }
        swap(temp,arr[low]);
        //以上代码与快排写法完全一致
        //下面代码是快速选择算法特有

        //arr[low]是整个区间的第low-left+1个数:用M表示
        int M=low-left+1;

        if(nk<M){//答案在区间[left,low-1]中
            return randselect(arr,nk,left,low-1);
        }else if(nk>M){//答案在区间[low+1,right]中
            return randselect(arr,nk-M,low+1,right);
        }else {//找到答案，即arr[low]
            return arr[low];
        }
    }
};
```

### 三路partition写法

```c++
class Solution {
public:
    int findKthLargest(vector<int>& arr, int k) {
        int nk=arr.size()-k+1;//第k个最大的元素相当于排序数组的第nk个元素
        return randselect(arr,nk,0,arr.size()-1);
    }

    int randselect(vector<int>& arr,int nk,int left,int right){
        swap(arr[left],arr[rand()%(right-left+1)+left]);
        int temp=arr[left];
        int i=left;
        int lt=left-1;
        int gt=right+1;
        while(i<gt){
            if(temp==arr[i]){
                ++i;
            }else if(temp>arr[i]){
                swap(arr[i++],arr[++lt]);
            }else{
                swap(arr[i],arr[--gt]);
            }
        }

        //以上代码与快排写法完全一致
        //下面代码是快速选择算法特有

        /*
        经过一次partition:划分得到的区间如下
        区间：arr[low...lt] < temp
        区间：arr[gt...high] > temp
        区间：arr[lt+1...i) == temp 也即  arr[lt+1...gt-1] == temp
        */

        //arr[lt+1]是整个区间的第lt-left+2个数:用al表示
        //arr[gt-1]是整个区间的第gt-left个数:用ar表示
        int al=lt-left+2;
        int ar=gt-left;

        if(nk<al){//答案在区间[left,lt]中
            return randselect(arr,nk,left,lt);
        }else if(nk>ar){//答案在区间[gt,right]中
            return randselect(arr,nk-ar,gt,right);
        }else {//找到答案，即arr[lt+1...gt-1] 区间的任意一个数
            return arr[lt+1];
        }
    }
};
```

## 小顶堆写法

```c++
class Solution {
public:
    struct cmp{//小顶堆写法牢记
        bool operator() (int a,int b){//不要漏写括号
            return a>b;
        }
    };
    int findKthLargest(vector<int>& arr, int k) {
        priority_queue<int,vector<int>,cmp>pq;//不要漏写cmp
        for(int num:arr){
            pq.push(num);
            if(pq.size()==k+1){
                pq.pop();
            }
        }
        return pq.top();
    }
};
```

# 复杂度分析

-   时间复杂度：O(n)
-   空间复杂度：O(1)
