---
author: Palfans
date: 2010-10-22 20:10:22+00:00
draft: false
title: vpn三合一安装包
type: post
url: /3-in-1-vpn-package/
categories:
- 技术栈
tags:
- VPN
- 互联网
---

这个安装包其实就是一个shell脚本，帮助大家尽量节约些时间。已经写好有一段时间了，一直没时间写blog，今天做了个review，修修补补就放出来吧。大概写下这个安装包的一些特点。



	  1. 三合一，指的是pptp / openvpn / l2tp，另外还安装了nginx / php / mysql / phpMyAdmin / Freeradius<del> / daloradius</del>，基本上把一个vpn系统需要的基本程序安装完了。
	  2. 安装包默认是完全安装，当然也可以只安装其中的某一种vpn，脚本提供定制选项
	  3. 脚本自动获取服务器ip、相关文件路径等，也可定制，说是一键安装有点儿夸张，不过按几下回车就能装完还是可以实现的。
	  4. 现在有CentOS<del>和Ubuntu两个</del>版本，分别在CentOS 5.5 <del>和 Ubuntu 10.04 LTS</del>上测试通过。Linux的发行版太多了，搞得我头都大了好几圈，对其他版本有爱的同学请自行修改。

<!--more-->

安装方法：

在命令行界面根据不同的版本，输入<del>wget http://20ne.com/vpn/ubuntu.sh 或 wget http://20ne.com/vpn/centos.sh</del>，取得文件后直接运行脚本，中间有少许参数需手工确认，无需修改的话，直接回车即可。【update】20ne.com这个域名被我卖了，主机被人攻击，备份代码全丢。这里郑重推荐[逸山的改进版](http://www.yishanhome.com/archives/307)，添加很多功能，安装更加人性化，自动化。

IT民工业余制作，测试环境恶劣，所以问题肯定很多，不能保证在所有平台都一帆风顺。大家用的时候可以自己斟酌修改，也可以和我交流。

【update 2010-10-28】
使用方法：

安装完成后，脚本会自动在数据库中添加一个测试账号test，密码为1234。大家可以通过自动安装的phpMyadmin访问mysql数据库进行修改和新增，对应的表为radius库里的radcheck，<del>也可以通过自动安装的daloradius来管理vpn账号，初始用户名为administrator，密码为radius，可以在radius库的operators中修改和新增管理用户</del>。
默认链接：
phpMyAdmin: http://你的VPS域名或IP/phpmyadmin/
<del>daloradius: http://<your vps ipaddress>/dr/</del>
对于openvpn，在安装目录下会生成一个名为openvpn的新目录，里边的client目录包含对应该服务端的客户端配置文件和证书，需要分发给客户端，并使用用户名和密码进行登录。
对于pptp，直接新建一个基于pptp的网络连接就行，具体请google “[如何设置pptp vpn](https://encrypted.google.com/search?q=%E5%A6%82%E4%BD%95%E8%AE%BE%E7%BD%AEpptp+vpn&hl=zh-CN)”
对于l2tp，和pptp类似，不过需要改为l2tp方式，并填写IpSec密钥，此密钥默认为palfans，可在执行脚本前，修改secret_key的值。具体如何新建连接请google “[如何设置l2tp vpn](https://encrypted.google.com/search?hl=zh-CN&newwindow=1&q=%E5%A6%82%E4%BD%95%E8%AE%BE%E7%BD%AE+l2tp+vpn&aq=f&aqi=&aql=&oq=&gs_rfai=)”

【update 2012-06-01】
欢庆六一儿童节，针对各位在留言中碰到的问题，统一做了更新，希望能提升安装成功率。本次更新取消了Ubuntu版本，因为我找不到测试环境，时间也不够，sigh~~~~~

1. 使用军哥的LNMP安装脚本来安装Nginx，PHP和MySQL，内存控制好，系统稳定性高。感谢军哥，有关LNMP的问题，请点[这里](http://www.lnmp.org/)。
2. OpenSwan改用自行编译方式，兼容性更高。
3. 取消Daloradius，账号管理用PhpMyAdmin已经比较方便，如没有复杂要求，没必要搞这个。有兴趣的同学可以自行安装。
4.  修正一堆弱智问题。

如有其他问题，欢迎留言或直接给我发邮件。
