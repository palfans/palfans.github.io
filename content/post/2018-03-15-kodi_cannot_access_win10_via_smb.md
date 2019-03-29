---
author: Palfans
date: 2018-03-15 21:03:12+00:00
draft: false
title: Kodi 通过 SMB 连接远程 Win 10 失败
type: post
url: /kodi_cannot_access_win10_via_smb/
categories:
- 技术栈
tags:
- kodi
- windows
---

我同时在 Win 10 和 Android 平板安装了 Kodi，通过 SMB 连接到家里的 Win10 NAS 视频媒体库。之前工作正常，这两天突然发现 Android 平板连不上，每次设置好用户名密码就报连接超时（Connection Timed Out），但 Win 10 平板依然正常。

上网搜索一圈，在 Kodi 的[官方维基](https://kodi.wiki/view/SMB/Windows)找到了答案。貌似 Win10 在某次自动升级以后，默认关闭了 SMB 1.0/CIFS File Sharing Support 的功能，需要重新手动打开。
<!--more-->

打开控制面板中的“添加删除程序功能”模块，或者在 Windows 开始旁边的搜索框中输入“Turn Windows feature on or off”并找到控制面板的最佳匹配项。

![](https://res.palfans.net/img/300px-2017-11-14_08-23-47.png)


在 Windows 功能中找到 SMB 1.0/CIFS File Sharing Support 并激活，Windows 会安装相应的文件。重启 Win10 之后，就能重新支持 Kodi 采用的旧版 SMB 协议。

![](https://res.palfans.net/img/OptionalFeatures_2017-11-14_08-24-53.png)


再次打开 Android 平板的 Kodi，通过 SMB 连接就能打开远端的媒体库了。这个问题并没有出现在 Win10 平板的 Kodi 中，估计因为 Android 版本还没有支持 SMB 2.0。

伴随 Win10 自动升级还有另外一个坑在等着 Kodi。SMB 协议问题解决后，输入正确的 Outlook 账号也不能成功连接到远端 NAS的共享文件夹。解决方法是，升级后的 Win10 文件共享不能使用邮箱作为用户名了，需要登录到 Win10，打开 CMD 窗口，输入 whoami，会列出当前的机器名和用户名。使用这个用户名和对应的 Outlook 账号密码就能成功打开共享文件夹。
