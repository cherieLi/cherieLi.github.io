---
layout: post
title:  google bench
categories: linux
tags: linux
author: CherieLi
---

* content
{:toc}
### 使用

#### 基本使用

Benchmark: 程序名称

Time: 迭代一次的平均用户时间

CPU：迭代一次的平均CPU时间

Iterations: 总迭代次数

#### 输出格式

允许三种格式的输出，console、json、csv， 默认为console， 使用--benchmark_format=<console|json|csv>指定。

使用--benchmark_out=\<filename\>可将结果输出到指定的文件中。

#### 传递参数

传递和获取参数：

1.传递参数使用BENCHMARK宏生成的对象的Arg方法

2.传递进来的参数会被放入state 对象内部存储，通过range方法获取，调用时的参数0对应第一个参数

3.Arg 方法一次只能传递一个参数，使用Args方法传递多个参数

#### 计时器控制

在benchmark里面，如果每个迭代会有一些额外的setup, 可能会需要在循环里面做。但是一般来说在benchmark时间统计里面把这部分去掉。有两个函数可以做这个事情：PauseTiming()和ResumeTiming()事实上在循环里面使用这两个函数，输出结果里面时间额外多了很多。

Time是一次操作的用户时间
CPU是一次操作的CPU时间









### 参考文档

https://www.cnblogs.com/apocelipes/p/10348925.html

c++性能测试工具：google benchmark入门（一）