---
author: Palfans
date: 2008-05-22 23:31:15+00:00
draft: false
title: IIS不能运行ASP问题的解决
type: post
url: /iis-asp-can-not-run-issue/
categories:
- 闲聊
tags:
- asp
- http
- iis
- 网络
---

重装系统后，一直没有安装IIS，安装后发现不能正常运行ASP代码，报“http 500内部服务器错误”，而HTML文件能正常运行，卸载IIS并重装也不能解决问题。

在系统服务中，发现DTC服务不能正常启动，报错，运行msdtc -resetlog，再次检查，DTC能正常启动。重启机器，IIS依旧不正常。执行rundll32 wamreg.dll, CreateIISPackage；regsvr32 asptxn.dll，IIS终于能执行ASP代码。

<!--more-->总结操作步骤：

1. 右击“我的电脑”，选择管理，服务和应用程序，服务，手工启动DTC服务（全名为Distributed Transaction Coordinator）

2. 如果正常启动，跳过此步骤。如果启动失败，弹出对话框，则在开始菜单的运行中，输入 msdtc -resetlog，用来创建日志文件。

3. 重启机器，检查IIS能否正常执行ASP，如果不行，见下步

4. 在命令提示符状态下（开始菜单，运行，cmd）执行


>cd %windir%system32inetsrv
rundll32 wamreg.dll, CreateIISPackage
regsvr32 asptxn.dll


5. 检查IIS，应该已经恢复正常

其实上面第四步，可以通过卸载IIS并重装来达到同样的目的，不过这样比较简便。参阅了网上很多文章，都需要比较复杂的处理过程，不如这个方法来的快。如果IIS遇到问题，可以优先尝试上面的办法，不行再做他想。
