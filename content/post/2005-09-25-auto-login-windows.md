---
author: Palfans
date: 2005-09-25 21:05:03+00:00
draft: false
title: Windows系统自动登录
type: post
url: /auto-login-windows/
categories:
- 闲聊
tags:
- win2000
- win2003
- winxp
- 技术栈
- 电脑
- 自动登录
---

WIN 2000


打开控制面板-->用户和密码：把要使用本机，用户必须输入用户名和密码前面的复选框去掉，然后点击确定，系统提示必须有一个自动登陆的用户与密码。这时候输入自动登陆的用户名和密码，点击确定即可。对于Windows2000和NT操作系统，如果想自动登陆的话，此法通用：打开开始菜单-->运行，regedit，找到winlogon主键，在右边窗口新键字符串值：autoadminlogon：键值为1defaultusername：键值为自动登陆的用户名defaultpassword：键值为自动登陆用户的密码defaultdomainname：键值为自动登陆的计算机名称或者登陆网络的域名。重新启动计算机，一切OK。

<!--more-->WIN XP

单击“开始/运行”，输入“rundll32 netplwiz.dll,UsersRunDll”，按回车键后弹出“用户帐户”窗口，这和“控制面板”中打开的“用户账户”面板窗口不同。然后取消选定“要使用本机，用户必须输入用户名和密码”选项，单击确定，在弹出的对话框中输入你想让电脑每次自动登录的账户和密码即可。

WIN2003

与WIN XP的方法相同。也可采用WIN 2000的注册表方式。在系统启动时按住shift，就可以避免自动登录而使用其他的用户名登录了。
