---
title: "最小的k个数"
date: 2020-03-26T01:23:57+08:00
categories:
  - 剑指offer
tags:
  - 字节跳动
  - 排序
---

# 题目

![](/images/%E5%89%91%E6%8C%87offer/%E6%9C%80%E5%B0%8F%E7%9A%84k%E4%B8%AA%E6%95%B0.png)

# 综述

找到第k小之后，第k小左边的就是答案

# Code

```c++
class Solution {
public:
    vector<int>ans;
    vector<int> getLeastNumbers(vector<int>& arr, int k) {
        if(k==0)return ans;
        quick_sort(arr,0,arr.size()-1,k);
        return ans;
    }

    void quick_sort(vector<int>& arr, int l,int r,int k)
    {
        if(l>=r)
        {
            for(int i=0;i<=l;i++)ans.push_back(arr[i]);
            return;
        }

        int i=l-1,j=r+1;
        int x=arr[(l+r)>>1];

        while(i<j)
        {
            do i++;while(arr[i]<x);
            do j--;while(arr[j]>x);
            if(i<j)swap(arr[i],arr[j]);
        }
        if(k<=j-l+1)quick_sort(arr,l,j,k);
        else quick_sort(arr,j+1,r,k-(j-l+1));
    }
};
```
```c++
class Solution {
public:
    vector<int>res;
    vector<int> getLeastNumbers(vector<int>& arr, int k) {
        if(k==0||arr.size()==0)return res;
        srand((unsigned)time(NULL));//记住用法(一般在主函数中使用，产生随机种子))
        randselect(arr,0,arr.size()-1,k-1);//注意这里是k-1，代表下标
        for(int i=0;i<k;i++){
            res.push_back(arr[i]);
        }//最后返回的这几个数不一定是排好序的，但是是满足答案的
        return res;
    }
    void randselect(vector<int>& arr,int left,int right,int k){
        int p=partition(arr,left,right);
        if(p==k){//三个分支只能走一个
            return;
        }else if(p<k){//这个相当于经过之前的划分，p左边的都比p小；但是还不到k个，在右边再划分一次，找到k这个下标时就满足了
            randselect(arr,p+1,right,k);
        }else if(p>k){//这个好理解
            randselect(arr,left,p-1,k);
        }
    }
    int partition(vector<int>&nums,int left,int right){
        //partition模板，死记
        int p=(int)round(1.0*rand()/RAND_MAX*(right-left)+left);
        //产生[left,right]之间的随机数
        swap(nums[left],nums[p]);
        int temp=nums[left];
        while(left<right){
            while(left<right&&nums[right]>temp)right--;
            nums[left]=nums[right];
            while(left<right&&nums[left]<=temp)left++;
            nums[right]=nums[left];
        }
        nums[left]=temp;
        return left;
    }
};
```

# 复杂度分析
