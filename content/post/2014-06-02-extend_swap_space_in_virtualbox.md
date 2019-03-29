---
author: Palfans
date: 2014-06-02 18:30:56+00:00
draft: false
title: 为VirtualBox中的CentOS扩展Swap空间
type: post
url: /extend_swap_space_in_virtualbox/
categories:
- 技术栈
tags:
- centos
- linux
- swap
- virtualbox
---

在VirtualBox中安装好CentOS后，会发现默认的Swap空间大小是根据初次配置的可用内存来生成的，正常使用足够。但是，如果需要安装Oracle，它要求最少2GB的交换空间，这时就需要扩展交换空间。

首先运行swapon命令查看交换空间大小，如果得到的大小少于**2097152**，就需要增加交换空间。

    
    swapon -s
    


![](https://res.palfans.net/img/1.png)

1\. 首先，在VirtualBox中新增一块硬盘

![](https://res.palfans.net/img/2.png)



<!--more-->

![](https://res.palfans.net/img/3.png)

![](https://res.palfans.net/img/4.png)

![](https://res.palfans.net/img/5.png)

2\. 格式化新增的硬盘

    
    fdisk -l
    



    
    fdisk /dev/sdb


![](https://res.palfans.net/img/6.png)

![](https://res.palfans.net/img/7.png)

![](https://res.palfans.net/img/8.png)

3\. 转换为Swap space

    
    mkswap /dev/sdb1
    



    
    swapon /dev/sdb1
    



    
    swapon -s


![](https://res.palfans.net/img/9.png)

4\. 让CentOS开机自动加载新增的交换空间

    
    vi /etc/fstab


![](https://res.palfans.net/img/10.png)
