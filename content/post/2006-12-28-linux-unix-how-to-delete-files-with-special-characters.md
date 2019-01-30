---
author: Palfans
date: 2006-12-28 10:30:27+00:00
draft: false
title: Linux/Unix下如何删除具有特殊字符的文件
type: post
url: /linux-unix-how-to-delete-files-with-special-characters/
categories:
- 闲聊
tags:
- linux
- rm
- unix
- 删除
- 技术栈
- 电脑
---

假设Linux/Unix系统中有一个文件名叫“-target”，如果我们想对它进行操作，例如要删除它按照一般的删除方法在命令行中输入rm -target命令，界面会提示我们是“无效选项”(invalid option)，原来由于文件名的第一个字符为“-”，Linux/Unix把文件名当作选项了。

我们可以使用“--”符号来解决这个问题，通过man rm可以知道

<!--more-->

> A - permits the user to mark explicitly the end of any com- mand line options, allowing rm to recognize file arguments that begin with a -. As an aid to BSD migration, rm will accept -- as a synonym for -. This migration aid may disap- pear in a future release. If a -- and a - both appear on the same command line, the second will be interpreted as a file.



输入“rm -- -target”命令便可顺利删除名为“-target”的文件。如果是其他特殊字符的话可以在特殊字符前加一个“”符号，或者用双引号把整个文件名括起来。如果是远程主机，还可以通过在windows系统使用ftp工具连接，在windows下能正常对这些文件名进行操作。
