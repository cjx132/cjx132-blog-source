---
title: "23讲“orderby”是怎么工作的"
date: 2021-04-09T16:13:46+08:00
categories:
  - MySQL45讲
tags:
  - MySQL45讲
---

只要redo log和binlog保证持久化到磁盘，就能确保MySQL异常重启后，数据可以恢复。

# binlog的写入机制

事务执行过程中，**先把日志写到binlog cache，事务提交的时候，再把binlog cache写到binlog文件中。**

![img](https://static001.geekbang.org/resource/image/9d/d4/9d057f61d3962407f413deebc80526d4.png)

每个线程有自己binlog cache，但是共用同一份binlog文件。

-   write，指的就是指把日志写入到文件系统的page cache，并没有把数据持久化到磁盘，所以速度比较快。
-   fsync，才是将数据持久化到磁盘的操作。一般情况下，我们认为fsync才占磁盘的IOPS。

write 和fsync的时机，是由参数sync\_binlog控制的：

1.  sync\_binlog=0的时候，表示每次提交事务都只write，不fsync；
2.  sync\_binlog=1的时候，表示每次提交事务都会执行fsync；
3.  sync\_binlog=N(N>1)的时候，表示每次提交事务都write，但累积N个事务后才fsync。

因此，在出现IO瓶颈的场景里，将sync\_binlog设置成一个比较大的值，可以提升性能。在实际的业务场景中，考虑到丢失日志量的可控性，一般不建议将这个参数设成0，比较常见的是将其设置为100~1000中的某个数值。

但是，将**sync\_binlog设置为N，对应的风险是：如果主机发生异常重启，会丢失最近N个事务的binlog日志。**
