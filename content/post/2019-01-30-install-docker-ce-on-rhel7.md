---
title: "RHEL 7 上安装 Docker CE"
date: 2019-01-30T16:55:29-06:00
draft: false
author: Palfans
url: /install-docker-ce-on-rhel7/
categories: 
- 技术栈
tags: 
- RHEL7
- Docker
---

最近注册了 RedHat 的 Developer 账号，可以免费订阅一个账号用于开发测试。但是，RHEL 7 默认只能安装 Docker EE，是需要花钱的。如何才能安装社区版本的 Docker 呢？

首先提一下， 开发者计划提供的订阅账号，本身也**不带技术支持**，万事靠自己，所以就不用顾忌安装第三方源会失去技术支持的问题。

按照以前的经验，RHEL 可以安装 CentOS 的对应源。但是，参照 [Get Docker CE for CentOS](https://docs.docker.com/install/linux/docker-ce/centos/)，这次会出现一个错误

```
Requires: container-selinux >= 2.9
```
多次尝试后，终于把 Docker CE 装上了。
```
<!--more-->
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo yum makecache fast
sudo yum install -y http://mirror.centos.org/centos/7/extras/x86_64/Packages/container-selinux-2.74-1.el7.noarch.rpm
sudo yum install -y docker-ce
```
比官方流程多了一步，单独安装`container-selinux`。这个安装包更新挺频繁的，如果链接失效，需要前往<http://mirror.centos.org/centos/7/extras/x86_64/Packages/>查找最近打包的安装包。

然后就是正常使用了。
```
[palfans@home ~]$ sudo docker version
Client:
 Version:           18.09.1
 API version:       1.39
 Go version:        go1.10.6
 Git commit:        4c52b90
 Built:             Wed Jan  9 19:35:01 2019
 OS/Arch:           linux/amd64
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          18.09.1
  API version:      1.39 (minimum version 1.12)
  Go version:       go1.10.6
  Git commit:       4c52b90
  Built:            Wed Jan  9 19:06:30 2019
  OS/Arch:          linux/amd64
  Experimental:     false
  ```