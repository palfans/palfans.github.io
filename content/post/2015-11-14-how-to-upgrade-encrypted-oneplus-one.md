---
author: Palfans
date: 2015-11-14 06:24:00+00:00
draft: false
title: Oneplus One （一加手机）加密后如何升级系统
type: post
url: /how-to-upgrade-encrypted-oneplus-one/
categories:
- 技术栈
tags:
- oneplus
- 一加
- 手机
---

一年多以前从红米换成一加Oneplus One 64G版本，高通骁龙801的CPU，2G的运存，使用很顺手，终于体验了一把旗舰机的感觉。一加公司比较厚道，机器可以很轻松的通过[Bacon Root Toolkit](http://www.wugfresh.com/brt/)工具一键解锁和Root。

因为公司邮件系统的强制安全策略，所有接入系统的移动终端都会要求加密，但是一加国内版用的是ColorOS系统，加密功能貌似被取消了，而国际版的CyanogenMod系统却能正常加密，毫不犹豫刷成CM并成功加密。

但是问题来了，CM系统更新非常频繁，经常收到OTA升级通知，每次点击更新会在下载完升级包之后重启到Recovery的机器人界面，然后升级失败，怎么回事？

![](/img/how-to-upgrade-encrypted-oneplus-one/OnePlusOneAndroid5.1.1LollipopCyanogen.jpg)

<!--more-->

仔细看了下错误日志，问题应该出在加密上。安卓系统加密时把/data分区进行加密，而其他的分区都没有加密。手机在正常开机状态下，升级包文件都下载到/data分区保存，但不知道什么原因，进入Recovery后并不能正确的解密该分区，CM11S和CM12S都是这个问题，于是/data加载失败，升级肯定也就不能继续。

通过反复尝试，我发现有三种方法可以成功升级。

1. adb sideload update  
把升级包下载到电脑上，把手机和电脑用数据线连接起来，adb sideload就能从电脑读取升级文件后直接写入手机的/system分区，完全不访问/data，就不再有加密分区的限制。Bacon Root Toolkit提供了一键adb sideload的功能，点击下图按钮后，按照软件提示进行操作即可写入数据。  
![](/img/how-to-upgrade-encrypted-oneplus-one/sideload.jpg)

2. usb otg update  
将升级包复制到优盘中，通过otg线连接到手机，重启进入Recovery后，加载usb-otg分区。因为usb-otg也是未加密状态，可以直接访问，所以可能正常升级。

3. Custom Recovery  
给手机刷一个Custom Recovery，我选的是twrp 2.8.7.0，输入密码后能正确解密/data分区，就不需要电脑、数据线和otg线，直接手机下载升级包并升级。在这里我走了一些弯路。Bacon Root Toolkit提供刷写一个临时Custom Recovery的功能，重启自动恢复为官方Recovery。看起来挺美好，但是，虽然这个Custom Recovery也是twrp 2.8.7.0，它却不能正常解密/data，即使密码正确，也只能反复让你输入密码。所以一定要刷一个永久的Custom Recovery。当然，通过Bacon Root Toolkit随时可以刷回官方Recovery。

似乎很完美？其实并不全是。如果是rooted手机，升级会失败……

按常理root并不会影响OTA升级，但rooted的一加手机/system分区的校验码发生变化，升级包的脚本校验后，造成无法升级。所以如果手机root了，就没有办法使用增量包升级。很多人推荐的方法是等新版本的全量包发布，不过CM的全量包要比增量包晚很久才出，每天看着状态栏的升级提示却不能升级会很烦心。这里有个小技巧，下载前一版本的全量包和新版本的增量升级包，用前面的三种方法先刷全量包恢复成官方版本，再刷增量升级包，然后再一键root，就能提前升级到最新系统。
