---
author: Palfans
date: 2018-08-10 22:48:09+00:00
draft: false
title: Xfce4 failed to execute default Terminal Emulator
type: post
url: /xfce4_failed_to_execute_default_terminal_emulator/
categories:
- 技术栈
tags:
- linux
- xfce
---

Xfce 是一款针对 Linux 系统的现代化轻型开源桌面环境，基于 GTK+2.x 开发完成。相对于传统的 GNOME 桌面来说，xfce 足够轻量化，资源占用很低，但使用体验并不差。尤其对于公司的服务器来说，偶尔需要桌面环境，但又不想安装臃肿的 GNOME，Xfce 就是个不错的选择。

最近在公司新装了几台机器，装完发现 Xfce4 自带的终端模拟器一打开就报错。


```
Failed to execute default Terminal Emulator.
Input/output error.
```


从字面上看，I/O 有问题。查看了系统设置，终端设置都是正确的。Google 搜了一圈，有一个说法是终端的编码不是 UTF-8 也会导致这个问题，但我这里已经默认为 UTF-8。看日志发现如下错误：

```
/usr/bin/xfce4-terminal: symbol lookup error: /usr/bin/xfce4-terminal: undefined symbol: vte_regex_new_for_match
```


<!--more-->
symbol 找不到？前几个月安装其他机器的时候，没有这个问题。因为每次都是用 yum 在线安装，应该是版本升级造成的。看下当前用的什么版本的 vte。


```
$ ldd /usr/bin/xfce4-terminal | grep vte
libvte-2.91.so.0 => /lib64/libvte-2.91.so.0 (0x00007fdcd8cdd000)

$ rpm -qa|grep vte291
vte291-0.38.4-2.el7.x86_64
```


安装的 Linux 是 RHEL 7.3，vte291 版本是 0.38，有可能新版本的 vte 包含了缺失的 symbol。


```
$ sudo yum install vte291
Loaded plugins: product-id, search-disabled-repos, subscription-manager
Resolving Dependencies
--> Running transaction check
---> Package vte291.x86_64 0:0.38.4-2.el7 will be updated
---> Package vte291.x86_64 0:0.46.2-1.el7 will be an update
--> Processing Dependency: libpcre2-8.so.0()(64bit) for package: vte291-0.46.2-1.el7.x86_64
--> Running transaction check
---> Package pcre2.x86_64 0:10.23-2.el7 will be installed
--> Finished Dependency Resolution
```


新版本的 vte291 连带安装了 pcre2，再次尝试打开终端，还是报同样的错，但日志不同了。


```
/usr/bin/xfce4-terminal: symbol lookup error: /lib64/libvte-2.91.so.0: undefined symbol: gtk_widget_class_set_css_name
```


又有 GTK 的 symbol 找不到，升级 GTK。


```
$ sudo yum install gtk3
Loaded plugins: product-id, search-disabled-repos, subscription-manager
Resolving Dependencies
--> Running transaction check
---> Package gtk3.x86_64 0:3.14.13-20.el7 will be updated
---> Package gtk3.x86_64 0:3.22.10-4.el7 will be an update
--> Processing Dependency: pango(x86-64) >= 1.37.3 for package: gtk3-3.22.10-4.el7.x86_64
--> Processing Dependency: libXrandr(x86-64) >= 1.5.0 for package: gtk3-3.22.10-4.el7.x86_64
--> Processing Dependency: glib2(x86-64) >= 2.49.4 for package: gtk3-3.22.10-4.el7.x86_64
--> Processing Dependency: atk(x86-64) >= 2.15.1 for package: gtk3-3.22.10-4.el7.x86_64
--> Processing Dependency: gtk-update-icon-cache for package: gtk3-3.22.10-4.el7.x86_64
--> Running transaction check
---> Package atk.x86_64 0:2.14.0-1.el7 will be updated
---> Package atk.x86_64 0:2.22.0-3.el7 will be an update
---> Package glib2.x86_64 0:2.46.2-4.el7 will be updated
---> Package glib2.x86_64 0:2.50.3-3.el7 will be an update
---> Package gtk-update-icon-cache.x86_64 0:3.22.10-4.el7 will be installed
---> Package libXrandr.x86_64 0:1.4.2-2.el7 will be updated
---> Package libXrandr.x86_64 0:1.5.1-2.el7 will be an update
---> Package pango.x86_64 0:1.36.8-2.el7 will be updated
---> Package pango.x86_64 0:1.40.4-1.el7 will be an update
--> Processing Dependency: harfbuzz(x86-64) >= 1.0.3 for package: pango-1.40.4-1.el7.x86_64
--> Running transaction check
---> Package harfbuzz.x86_64 0:0.9.36-1.el7 will be updated
--> Processing Dependency: harfbuzz(x86-64) = 0.9.36-1.el7 for package: harfbuzz-icu-0.9.36-1.el7.x86_64
---> Package harfbuzz.x86_64 0:1.3.2-1.el7 will be an update
--> Running transaction check
---> Package harfbuzz-icu.x86_64 0:0.9.36-1.el7 will be updated
---> Package harfbuzz-icu.x86_64 0:1.3.2-1.el7 will be an update
--> Processing Conflict: gtk-update-icon-cache-3.22.10-4.el7.x86_64 conflicts gtk2 < 2.24.29 --> Restarting Dependency Resolution with new changes.
--> Running transaction check
---> Package gtk2.x86_64 0:2.24.28-8.el7 will be updated
---> Package gtk2.x86_64 0:2.24.31-1.el7 will be an update
--> Finished Dependency Resolution
```


终于又看到黑色的终端。
