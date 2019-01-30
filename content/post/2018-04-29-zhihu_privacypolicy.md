---
author: Palfans
date: 2018-04-29 03:05:45+00:00
draft: false
title: 不能不同意的知乎隐私政策
type: post
url: /zhihu_privacypolicy/
categories:
- 技术栈
tags:
- javascript
---

最近知乎上线了一个新功能，打开任何问答页面，都会弹出一个隐私政策对话框，你有同意或者不同意两个选择，但是，如果你想继续阅读内容，不同意是不行的。

![](/img/zhihu_privacypolicy/zhihu_privacypolicy.gif)




<!--more-->

那知乎都想拿些什么东西呢？大致看了下，各种社交信息，身份证号以及手机设备号等等只要能弄到的隐私信息，知乎都想要。而且，它不会“主动”公开披露和转让隐私，但没说它不会公开披露和转让隐私。这个就比较尴尬了。

不是很理解知乎想干什么，打算做个隐私贩子？

不过目前有可用的方法在不点击同意按钮的前提下，强制跳过这个窗口，已测试，好使。


> 
> ### [zhihu_blockPrivacyPolicy](https://github.com/QingGo/zhihu_blockPrivacyPolicy)
> 
> 
>破解知乎网页端不同意隐私协议就没法用的油猴脚本。
>
>The Tampermonkey script to block privacy policy of the web site Zhihu.
>
>用法：安装油猴插件，然后新建油猴脚本。把“zhihu_blockPrivacyPolicy.js”里的内容复制进去保存即可。
>
>Usage: Install Tampermonkey Browser plug-in, then create a new Tampermonkey script. Copy the contents of "zhihu_blockPrivacyPolicy.js" into the script and save it.
