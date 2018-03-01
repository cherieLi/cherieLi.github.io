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

## 安装步骤

### 1.安装libssl, 否则后期会出现缺少“crypto”的错误
```
apt-get install libssl-dev
```

### 2.安装glib, 否则后期会出现缺少“glib”的错误(我下载的是 glib-2.32.0)
然后在glib-2.32.0文件夹下依次执行
```
./config
make
make install
```

### 3.安装build essential, 否则后期会出现缺少“gcc”的错误
```
apt-get install build-essential
```

### 4.安装zlib, 否则后期会出现缺少“zlib”的错误
```
apt-get install zlib1g.dev
```

### 5.安装libffi, 否则后期会出现缺少“libffi”的错误
```
apt-get install libffi-dev.dev
```

### 6.将/usr/local/include/glib-2.0和/usr/local/lib/glib-2.0/include下的所有文件复制到/usr/local/include下
```
cp /usr/local/include/glib-2.0/* /usr/local/include/
cp –r /usr/local/include/glib-2.0/ /usr/local/include/
cp /usr/local/lib/glib-2.0/include/* /usr/local/include
```

### 7.将/usr/local/lib下的libglib-2.0.so复制为libglib.so
```
cp /usr/local/lib/libglib-2.0.so /usr/local/lib/libglib.so
```

### 8.在destor文件夹下依次执行
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
http://blog.csdn.net/lusehu/article/details/6415213
http://blog.csdn.net/wwt18946637566/article/details/46602305
```
apt-get install autoconf
apt-get install autoreconf -ivf
```	

最后我在运行 destor 的时候老是显示container有问题，于是我执行了一下 
```
./rebuild 
```
重新运行destor 就成功啦~
