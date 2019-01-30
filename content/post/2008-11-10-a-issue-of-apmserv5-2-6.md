---
author: Palfans
date: 2008-11-10 05:27:18+00:00
draft: false
title: APMServ5.2.6 无法启动Apache的一个问题
type: post
url: /a-issue-of-apmserv5-2-6/
categories:
- 闲聊
tags:
- apache
- APMServ
- mysql
- nginx
- openssl
- php
- 无法启动
- 错误
---

最近APMServ很火，先大致介绍一下：

APMServ 5.2.6 是一款拥有图形界面的快速搭建Apache 2.2.9、PHP 5.2.6、MySQL 5.1.28 ＆4.0.26、Nginx 0.7.19、Memcached 1.2.4、phpMyAdmin 2.11.9.2、OpenSSL、SQLite、ZendOptimizer，以及ASP、CGI、Perl网站服务器平台的绿色软件。无需安装，具有灵活的移动性，将其拷贝到其它目录、分区或别的电脑时，均只需点击APMServ.exe中的启动按钮，即可自动进行相关设置，将Apache和MySQL安装为系统服务并启动。APMServ集合了Apache稳定安全的优点，并拥有跟IIS一样便捷的图形管理界面，同时支持MySQL 5.0 & 4.0两个版本，虚拟主机、虚拟目录、端口更改、SMTP、上传大小限制、自动全局变量、SSL证书制作、缓存性能优化等设置，只需鼠标一点即可完成。具体的介绍，参见[官方网站](http://apmserv.s135.com/)

<!--more-->用这个工具搭建一整套服务器平台相当的快捷、方便。不过看看大家的反应，遇到最多的问题应该还是点了APMServ后发现Apache无法正常启动。我也不例外，运行后报Apache启动失败。在系统管理的服务中，可以看到APMServ一共添加了两个服务，Apache和MySQL，如果选择支持asp，会再多一个ASP相关的服务，名字都是以APMServ开头。MySQL和ASP都能正常启动，但是Apache即使手工启动也会失败。官方的建议是，迅雷、Skype、PPLive、BT等软件启动后默认会占用80端口，导致Apache无法启动。解决方法：先关闭这些软件，启动完APMServ之后，再开启这些软件。我使用netstat -a，发现当前系统中没有软件占用了80端口，不符合官方的一般性解释。

通过事件查看器检查应用程序的日志，会发现一些和Apache Service相关的错误日志，内容为

The Apache service named  reported the following error:
>>> Syntax error on line 199 of D:/Website/APMServ5.2.6/Apache/conf/extra/httpd-ssl.conf:     .

The Apache service named  reported the following error:
>>> <Directory "E:/APMServ5.2.0/www/cgi-bin"> path is invalid. 

原因很明确，D:/Website/APMServ5.2.6/Apache/conf/extra/httpd-ssl.conf 这个配置文件在运行了APMServ之后，没有更新当前的路径，而是使用的软件默认的E盘路径。解决方法很简单，编辑D:/Website/APMServ5.2.6/Apache/conf/extra/httpd-ssl.conf，将其中E盘的那个地址换成当前软件所在的路径，停止APMServ，再启动，或者直接在服务中手工启动。Apache终于能正常运行了。
