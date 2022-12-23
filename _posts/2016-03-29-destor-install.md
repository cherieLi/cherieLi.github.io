---
layout: post
title:  destor安装流程(ubuntu)
date:   2016-03-29 09:35:45 +0800
categories: linux
tags: 安装
author: CherieLi
---

* content
{:toc}

点击下载安装包[destor](https://github.com/cherieLi/destor)

https://github.com/fomy/destor  

http://blog.csdn.net/zmxiangde_88/article/details/8024223  

## 安装步骤

 1.安装libssl, 否则后期会出现缺少“crypto”的错误
```
apt-get install libssl-dev
```

2.安装glib, 否则后期会出现缺少“glib”的错误(我下载的是 glib-2.32.0)
然后在glib-2.32.0文件夹下依次执行
```
./config
make
make install
```

3.安装build essential, 否则后期会出现缺少“gcc”的错误
```
apt-get install build-essential
```

 4.安装zlib, 否则后期会出现缺少“zlib”的错误
```
apt-get install zlib1g.dev
```

 5.安装libffi, 否则后期会出现缺少“libffi”的错误
```
apt-get install libffi-dev.dev
```

 6.将/usr/local/include/glib-2.0和/usr/local/lib/glib-2.0/include下的所有文件复制到/usr/local/include下
```
cp /usr/local/include/glib-2.0/* /usr/local/include/
cp –r /usr/local/include/glib-2.0/ /usr/local/include/
cp /usr/local/lib/glib-2.0/include/* /usr/local/include
```

 7.将/usr/local/lib下的libglib-2.0.so复制为libglib.so
```
cp /usr/local/lib/libglib-2.0.so /usr/local/lib/libglib.so
```

 8.在destor文件夹下依次执行
```
./config
make
make install
```

没有出错，说明安装成功，然后就可以执行
```
destor –h 
```
 观察destor 如何使用啦~
 
如果出现“glib not found”回到步骤2查看glib是否安装成功

如果出现“glib.h not found”回到步骤6,7查看文件是否复制成功

如果出现缺少msgfmt的问题 
```
apt-get install gettext
```

如果出现aclocal-1.14 is missing on your system
解决办法见参考文献：
>http://blog.csdn.net/lusehu/article/details/6415213   
>http://blog.csdn.net/wwt18946637566/article/details/46602305
```
apt-get install autoconf
apt-get install autoreconf -ivf
```	

最后我在运行 destor 的时候老是显示container有问题，于是我执行了一下 
```
./rebuild 
```
重新运行destor 就成功啦~


Destor是一个数据去重评估平台  
1.基于容器的存储  
2.块级流水线  
3.固定大小的分开，基于内容的分块，近似文件级去重  
4.指纹索引 DDFS, Extreme Binning, Sparse Index, SiLo  
5.改写算法 CFL, CBR, CAP, HAR  
6.恢复算法 LRU，优化的替代算法，向前滚动组件  

分块算法对应的文章：  
    a) A Low-bandwidth Network File System @ SOSP'02.  

    b) A Framework for Analyzing and Improving Content-Based Chunking Algorithms @ HP technical report.  

    c) AE: An Asymmetric Extremum Content Defined Chunking Algorithm for Fast and Bandwidth-Efficient Data Deduplication @ IEEE Infocom'15.  

指纹索引对应的文章：  
    a) Avoiding the Disk Bottleneck in the Data Domain Deduplication File System @ FAST'08.  

    b) Sparse Indexing: Large Scale, Inline Deduplication Using Sampling and Locality @ FAST'09.  

    c) Extreme Binning: Scalable, Parallel Deduplication for Chunk-based File Backup @ MASCOTS'09.  

    d) SiLo: A Similarity-Locality based Near-Exact Deduplicatin Scheme with Low RAM Overhead and High Throughput @ USENIX ATC'11.  

    e) Building a High-Performance Deduplication System @ USENIX ATC'11.  

    f) Block Locality Caching for Data Deduplication @ SYSTOR'13.  

    g) The design of a similarity based deduplication system @ SYSTOR'09.  

分片：  
    a) Chunk Fragmentation Level: An Effective Indicator for Read Performance Degradation in Deduplication Storage @ HPCC'11.  

    b) Assuring Demanded Read Performance of Data Deduplication Storage with Backup Datasets @ MASCOTS'12.  

    c) Reducing impact of data fragmentation caused by in-line deduplication @ SYSTOR'12.  

    d) Improving Restore Speed for Backup Systems that Use Inline Chunk-Based Deduplication @ FAST'13.  

    e) Accelerating Restore and Garbage Collection in Deduplication-based Backup Systems via Exploiting Historical Information @ USENIX ATC'14.  

    f) Reducing Fragmentation for In-line Deduplication Backup Storage via Exploiting Historical Information and Cache Knowledge @ IEEE TPDS.  

恢复算法：  
    a) A Study of Replacement Algorithms for a Virtual-Storage Computer @ IBM Systems Journal'1966.  

    b) Improving Restore Speed for Backup Systems that Use Inline Chunk-Based Deduplication @ FAST'13.  

    c) Accelerating Restore and Garbage Collection in Deduplication-based Backup Systems via Exploiting Historical Information @ USENIX ATC'14.  

垃圾回收：  

    a) Building a High-Performance Deduplication System @ USENIX ATC'11.  

    b) Cumulus: Filesystem Backup to the Cloud @ FAST'09.   

    c) Accelerating Restore and Garbage Collection in Deduplication-based Backup Systems via Exploiting Historical Information @ USENIX ATC'14.  

