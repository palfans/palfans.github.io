---
author: Palfans
date: 2007-01-03 23:59:01+00:00
draft: false
title: 系统蓝屏并提示BIOS有问题,代码:0x000000A5
type: post
url: /system-blue-screen-0x000000a5/
categories:
- 闲聊
tags:
- acpi
- bios
- 内存
- 技术栈
- 电脑
- 蓝屏
---

新购电脑，使用4天时间，期间一直未关机。突然在使用中蓝屏，提示建议重新启动。重启后在winxp进度条时再次蓝屏，提示BIOS有问题。


>*** STOP: 0x000000A5 (0x00010003 , 0x85E37CD0 , 0X0000000B , 0x85E885E4)The ACPI BIOS in this system is not fully compliant with the ACPI specification.please read the README.TXT for possible workarounds you can also contact your system's mamufacturer for an update BIOS,or visit http://www.hardware-update.com to see if a new BIOS is aviailable.The BIOS in this system is not fully ACPI compliant.please comtact your system vendor or visit http://www.hardware-update.com for an updated BIOS if you are unable to obtain an updated BIOS or the latest BIOS supplied by your vendor is not ACPI compliant,you can turn off ACPI mode durring text mode setup.to do this,simple press the F7 key when you are prompted to install storage drivers.the system will not notify you that the F7 key was pressed.it will silently disable ACPI and allow you to continue your installation.



<!--more-->0x000000A5: ACPI_BIOS_ERROR

错误分析: 通常是因为主板BIOS不能全面支持ACPI规范.

 解决方案:如果没有相应BIOS升级, 那么可在安装Windows 2000/XP/时, 当出现"press F6 if you need to install a third-party SCSI or RAID driver"提示时, 按下F7键, 这样Windows便会自动禁止安装ACPI HAL, 而安装 Standard PC HAL。

使用ghost恢复系统仍然蓝屏，在bios中将acpi选项关闭，系统则不断重启。因已经使用了4天时间，排除主板bios不支持acpi的可能性，开机蓝屏大多数情况，要么是主板坏了，要么是内存有问题。于是更换一条新的ddr2（533）512M内存，问题解决，连续开机，使用正常。
