---
layout: post
title:  TBB安装流程(ubuntu)
date:   2017-08-19 11:22:42 +0800
categories: linux
tags: 安装
author: CherieLi
---
* content
{:toc}

#### 1.下载最近的src .tgz文件，文件名样例：tbb* oss_src.tgz。
#### 2.解压安装包
#### 3.进入tbb* oss_src文件夹，依次执行
``
make     
cd build     
cd linux_intel64_gcc_cc6.2.0_libc2.24_kernel4.8.0_release     
source tbbvars.sh  
``
#### 4. 然后就可以用-ltbb编译程序啦！例：
	 g++ file.cpp -o test –ltbb
