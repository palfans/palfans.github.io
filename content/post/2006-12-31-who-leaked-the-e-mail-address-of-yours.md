---
author: Palfans
date: 2006-12-31 14:59:12+00:00
draft: false
title: Gmail技巧：谁泄露了你的邮箱地址
type: post
url: /who-leaked-the-e-mail-address-of-yours/
categories:
- 闲聊
tags:
- gmail
- Google
- 技术栈
- 电脑
- 邮箱
---

在一个网站使用邮箱注册的时候，我们希望的是邮箱地址不会被泄露给那些垃圾邮件的制造者。如果你使用的是Gmail，这里有个很简单的方法可以知道是哪个网站泄露了你的邮箱地址。

例如你使用 username@gmail.com 的邮箱在 samplesite.com 注册，这时你可以将邮箱换成 username+samplesitecom@gmail.com 。

<!--more-->Gmail 在解析邮箱地址时如果遇到 “+” 会自动将其后面的字符进行标记以确定它所发送的网址。而邮件最后还是会被发送到 username@gmail.com。当用 Gmail 搜索 username+samplesitecom的时候，我们就能看到发到这个邮箱地址的所有消息，是不是垃圾邮件看一下就清楚了。

用这个方法好像可以一个邮箱多次注册用户名哦。
