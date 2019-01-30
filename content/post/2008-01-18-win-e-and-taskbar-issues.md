---
author: Palfans
date: 2008-01-18 15:01:38+00:00
draft: false
title: Win+E不能打开资源管理器，右击任务栏也打不开属性
type: post
url: /win-e-and-taskbar-issues/
categories:
- 闲聊
tags:
- Win+E
- 任务栏
- 快捷键
- 电脑
- 资源管理器
---

问题：偶然发现，我的HP笔记本不能通过组合键 Win + E 打开资源管理器，却可以通过右击我的电脑选择资源管理器来打开，而且 Win 的其他快捷键都能正常工作，后来又发现对着任务栏单击右键，不能打开属性项，这两种操作都会报错，内容为“This operation has been cancelled due to restrictions in effect on this computer. Please contact your system administrator”，大致意思是“本次操作由于这台计算机的限制而被取消。请与您的系统管理员联系”。

过程：今天之前都是一切正常的，这个问题很奇怪，google了一下，发现出现这个问题的人很多，但是真正得到解决的却没有几个。首先回忆了一下从今天早上到发现问题为止，一共干了哪些事情。通过分析发现，除了正常的收发邮件、写代码、看文档以及msn之外，唯一有疑点的就是把前两天下载的Windows优化大师 8周年纪念版装上看了看。于是又打开Windows优化大师，一项一项的检查里边的优化设置，都没发现有和这个问题相关的。先看看别人怎么说吧。

<!--more-->网上最多的解答是：组策略(Group Policy)限制。运行 gpedit.msc -> 用户配置(User Configuration) -> 管理模板(Administrative Templates) -> windows组件(Windows Components) -> windows资源管理器(Windows Explorer) -> 关闭windows+X热键(Turn off Windows+X hotkeys) -> 设为已禁用(Disabled)。不过这样只是针对所有的Win组合快捷键都不起作用时使用，不符合我的问题描述。然后发现一个回答不一样，[via](http://bbs2.nnsky.com/dispbbs_60_3422597.html)


>以前可能装过什么软件，后来我最常用的打开资源管理器的快捷键不能用了，用了各种方法查病毒，搜索插件，杀木马都没查出问题，真是见鬼了其他快捷键都能用，就是win+e，网上搜了一下还有位仁兄解决了。原来是有个dll注入到了exploer进程，但这个dll不是病毒，所以任何杀毒软件都搜不出用icesword查看进程的模块，强制关掉iedetect.dll，然后到 文件 强制删除那个dll，运行regedit，把搜到iedetect.dll的键都删除，然后重启，可爱的快捷键又来了，爽啊
参考http://bbs.360safe.com/viewthread.php?tid=19041http://tonyzhu.spaces.live.com/Blog/cns!34FE454F7CD8E179!382.entry


对照查了一下，我电脑上没有iedetect.dll，explorer也没有被注入什么陌生的dll，而文章参考的两条链接都失效，无从考证。没找到出路，只好自己来，打开注册表编辑器(regedit)，在里边慢慢查找和权限、策略有关的项目，最后看到HKEY_CURRENT_USERSoftwareMicrosoftWindowsCurrentVersionPoliciesExplorer，有两个键：NoSetFolders 和 NoSetTaskbar，键值都为1。心中一阵窃喜，将它们改为0，测试。任务栏属性问题已经解决，快捷键打开资源管理器还是不行，Ctrl + Shift + Esc，弹出任务管理器，杀掉explorer，再重新打开一个explorer，OK，问题解决。

总结：网上信息相当丰富，但是不一定就是现成的，有时候还是需要自己动动脑子。另外Windows优化大师确实有问题，我重装后这个问题又重现了，也可能是我某个地方设置没选对。同时，使用Windows优化大师清理注册表之后重启电脑，HP ProtectTools(主要为了指纹识别) 会频繁报错，大致是丢失DLL，从Windows优化大师恢复注册表后问题消失。

附，因为操作系统是英文的 Windows XP，所以文中的中文提示和选项，都是我临时翻译的，可能和中文版的XP有差异，请随机应变，谢谢。
