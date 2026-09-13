---
title: "16讲“orderby”是怎么工作的"
date: 2021-04-04T16:13:46+08:00
categories:
  - MySQL45讲
tags:
  - MySQL45讲
---

# 全字段排序

![img](https://static001.geekbang.org/resource/image/6c/72/6c821828cddf46670f9d56e126e3e772.jpg)

# rowid排序

**如果MySQL认为排序的单行长度太大会怎么做呢？**

![img](https://static001.geekbang.org/resource/image/dc/6d/dc92b67721171206a302eb679c83e86d.jpg)

**rowid排序多访问了一次表t的主键索引**

# 全字段排序 VS rowid排序

如果MySQL实在是担心排序内存太小，会影响排序效率，才会采用rowid排序算法，这样排序过程中一次可以排序更多行，但是需要再回到原表去取数据。

如果MySQL认为内存足够大，会优先选择全字段排序，把需要的字段都放到sort\_buffer中，这样排序后就会直接从内存里面返回查询结果了，不用再回到原表去取数据。

这也就体现了MySQL的一个设计思想：**如果内存够，就要多利用内存，尽量减少磁盘访问。**

对于InnoDB表来说，rowid排序会要求回表多造成磁盘读，因此不会被优先选择。

# 如何避免排序

使用联合索引；使用覆盖索引来优化
