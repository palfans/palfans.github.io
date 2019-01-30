---
author: Palfans
date: 2005-08-22 10:14:26+00:00
draft: false
title: 开机自动启用Num Lock
type: post
url: /automatically-enable-num-lock/
categories:
- 闲聊
tags:
- numlock
- winxp
- 技术栈
- 电脑
- 自动
---

前两天接到朋友的一个求助电话，说新装的winxp，每次启动完成需要输密码的时候小键盘的Num Lock灯都是灭的，很不方便，问我有没有什么比较好的方法可以一劳永逸。

这种问题我记得以前在网上见过，打开Google，发现可以将注册表中的[HKEY_USERS.DefaultControl PanelKeyboard]中 InitialKeyboardIndicators 的值从 0 更改为 2就能解决问题。

<!--more-->结果朋友说自己电脑盲，要搞注册表太难。我就帮他导出一个现成的注册表文件，只要双击就可以了。朋友又说传文件太麻烦，有没有更简单的问题。我彻底服了，这么懒的人都有。

于是只好在自己机器上试验，不一会儿，发现果然有个简单的方法，只要在winxp启动后需要输密码时，将Num Lock灯打开，然后直接在选项中选择重启机器，以后输密码时Num Lock灯就会自动打开了。

感想：任何事情果然都有简单的方法，另外，懒惰果然是人类进步的原动力。
