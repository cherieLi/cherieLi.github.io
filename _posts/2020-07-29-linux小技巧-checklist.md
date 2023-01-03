---
layout: post
title:  linux小技巧-checklist
categories: linux
tags: linux
author: CherieLi
---

* content
{:toc}
lsof -i 显示系统端口使用情况

ipcs -m 查看共享内存

crontab -l 查看定时任务

lscpu 查看cpu相关信息


cat /dev/random| od -x查看并产生真正的随机数

arch命令用于显示当前主机的硬件架构类型


设置空闲等待时间： TMOUT=0

环境变量 https://www.jianshu.com/p/ac2bc0ad3d74



#### 确定机器类型（arm/x86）

```
arch
uname -r
uname -m
```

#### 确定操作系统类型

```
cat /etc/*release | head -1 |awk -F " " '{print $1}'
```



#### 检查内存容量

```
free -h
cat /proc/meminfo
```



#### 检查磁盘容量

```
df -h /目录
```

#### 查看LVM卷组信息

```
vgdisplay
```

#### 查看磁盘IO性能

```
iostat -xdm 1
iostat -kdx 1
```

#### 查看进程对应的线程

```
top -p `pidof 进程名` -Hb -n 1
```

#### 操作系统页大小查看

```
getconf PAGE_SIZE
```

#### 查询网卡类型

```
lspci | grep Eth 
```



查看网卡数据流量

```
sar -n DEV 1
sar -n DEV 1 100
sar -n DEV 1 1000

```

1 表示间隔1秒统计

sar命令包含在sysstat工具包中，提供系统的众多统计数据。其在不同的系统上命令有些差异，某些系统提供的sar支持基于网络接口的数据统计，也可以查看设备上每秒收发包的个数和流量。

-n参数很有用，他有6个不同的开关：DEV | EDEV | NFS | NFSD | SOCK | ALL ，其代表的含义如下：

DEV显示网络接口信息。

EDEV显示关于网络错误的统计数据。

NFS统计活动的NFS客户端的信息。

NFSD统计NFS服务器的信息

SOCK显示套接字信息

ALL显示所有5个开关

#### 查看线程或进程的cpu核

```
taskset -pc [thread_id/process_id]
```

#### 检查磁盘是否存在

```
fdisk -l
```

#### 列出所有块设备信息

```
lsblk
```

#### 查看nvme驱动版本号

```
cat /sys/module/nvme/version
cat /sys/module/nvme/version 和modinfo nvme查看驱动版本为1.0为开源
```

#### 查看端口链接情况

```
netstat -anop|grep 199
```

#### 查看防火墙状态

```
查询 systemctl status firewalld.service
禁用 systemctl disable firewalld.service
关闭 systemctl stop firewalld.service
service iptables status
```

#### 查看已删除空间却没有释放的进程
```
lsof -n |grep deleted
```
解决办法：
找到对应的进程号，kill掉即可；
https://blog.csdn.net/allway2/article/details/102546095

#### 参考文档
https://linuxtools-rst.readthedocs.io/zh_CN/latest/index.html
