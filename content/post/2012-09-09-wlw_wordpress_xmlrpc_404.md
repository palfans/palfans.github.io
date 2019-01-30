---
author: Palfans
date: 2012-09-09 07:03:45+00:00
draft: false
title: WLW发布Wordpress时xmlrpc.php 404问题
type: post
url: /wlw_wordpress_xmlrpc_404/
categories:
- 技术栈
tags:
- WLW
- wordpress
- xmlrpc
---

用WLW连接Wordpress发布文章时，总是遇到找不到xmlrpc.php的问题，直接在浏览器中输入完整地址，也是得到404错误。但是通过FTP能看到这个文件就放在那里。怎么回事？

很自然开始怀疑主机有策略，阻止了对xmlrpc的访问。于是把xmlrpc.php随便改个稀奇古怪的名字，再次测试，成功了。
