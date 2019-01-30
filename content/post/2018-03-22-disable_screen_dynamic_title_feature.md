---
author: Palfans
date: 2018-03-22 21:10:38+00:00
draft: false
title: 禁用 screen 的动态标题功能
type: post
url: /disable_screen_dynamic_title_feature/
categories:
- 技术栈
tags:
- linux
- screen
---

GNU screen 是 Linux 上面一个非常有用的程序。它默认启用了窗口动态标题的功能，每次切换目录时，会自动用当前目录的全路径作为窗口的标题。一般情况下，这个功能很贴心，但有时候路径很长，窗口名的 tab 会显示不全而陷入混乱，我们只需要一个简明扼要的自定义标题来区分不同的窗口。

<!--more-->
一个临时的解决方案是在当前窗口中运行 “unset PROMPT_COMMAND”。因为 bash 通过变量 PROMPT_COMMAND 将定制的路径内容传递给 screen，把这个变量 unset 窗口名就不会再变化。如果想默认禁用这个功能，可以把这条命令加入当前用户的 ~/.bashrc，这样所有新开窗口都会生效。

禁用动态标题后，只需要 ctrl + a + A 就能自定义窗口名。
