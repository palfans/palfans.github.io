---
author: Palfans
date: 2011-01-29 09:19:49+00:00
draft: false
title: Windows编译OpenVPN客户端（保存密码，修改日志字体）
type: post
url: /compile-openvpn-gui-n-client/
categories:
- 技术栈
tags:
- openssl
- OpenVPN
- VPN
- 字体
- 密码
---

OpenVPN客户端一般由两部分组成，OpenVPN和OpenVPN GUI。官方提供的OpenVPN客户端 for Windows为了提高用户账号的安全性，默认不能保存密码。安全性确实提高了，但是遇到网络不好，VPN意外中断时，每次都要手工输入密码，也很烦心。所以我们需要重新编译客户端代码，让其能够保存密码。而OpenVPN GUI的问题在于状态窗口输入的日志字体小得惊人，像我这种戴眼镜后矫正视力才0.6的人，想看清连接状态几乎是不可能的，所以也得改。

** **

**<!--more-->搭建编译环境**

我很久没有碰过C语言了，所以除了官方推荐的环境，我不确定其他环境是否能顺利编译。

NSIS [下载链接](http://sourceforge.net/projects/nsis/files/NSIS%202/2.46/nsis-2.46-setup.exe/download)，建议完全安装。

MinGW [下载链接](http://sourceforge.net/projects/mingw/files/Automated%20MinGW%20Installer/)，现在MinGW只提供在线安装程序，安装时再下载相关文件，耗时较长。也可以Google一下，有[完整版](http://data.99pan.com/download/7621_43_7840485476281515222.html)可下，假设安装路径为 C:\MinGW

MSYS [下载链接](http://downloads.sourceforge.net/mingw/MSYS-1.0.11.exe)，假设安装路径为 C:\msys\1.0

msysDTK [下载链接](http://downloads.sourceforge.net/mingw/msysDTK-1.0.1.exe)，安装路径和MSYS相同，C:\msys\1.0

配置环境变量，如果变量PATH中已有其他路径，则新增“;C:\MinGW\bin;C:\msys\1.0\bin”，如果变量PATH不存在，则新建一个变量，命名为PATH，内容为“C:\MinGW\bin;C:\msys\1.0\bin”。注意，变量内容都不包含引号，新增变量时，变量内容最前面没有分号。

配置完成后，打开命令提示符，输入bash，如果界面显示“bash-3.1$”，则编译环境搭建完成。

** **

**获取预编译文件**

OpenVPN编译过程中需要openssl，tap驱动，lzo压缩等文件，我们可以下载已经编译好的文件 [下载链接](http://openvpn.net/prebuilt/2.1_rc22-prebuilt.tbz)，这里的预编译文件版本不是2.1.1，但不影响实际编译效果。

新建一个工作目录 C:\work，将预编译文件解压后置于工作目录下，应包含以下四个目录：

```
gen-prebuilt
lzo-2.02
openssl-0.9.8l
pkcs11-helper
```

**编译OpenVPN GUI**

获取OpenVPN GUI的源代码 [下载链接](http://openvpn.se/files/source/openvpn-gui-1.0.3.zip)，解压缩到C:\work\openvpn-gui目录下。

首先修改openvpn.c，找到函数StatusDialogFunc，修改

```
charformat.yHeight = 100;
strcpy(charformat.szFaceName, "MS Sans Serif");
```

其中100是字体大小，MS Sans Serif是字体名称。

接下来需要修改Makefile，

OPENSSL = /c/OpenSSL 改为预编译文件中的openssl下include头文件的位置，这里为

`OPENSSL = ../openssl-0.9.8l/include/openssl`

LIB_DIRS = -L${OPENSSL}/lib/MinGW 改为预编译文件中openssl的相关lib目录，这里为

`LIB_DIRS = –L../gen-prebuilt/lib`

运行C:\msys\1.0下的msys.bat，进入类unix环境，切换到C:\work\openvpn-gui目录，执行make，OpenVPN GUI即可编译完成。

**编译OpenVPN客户端**

获取OpenVPN客户端的源代码 [下载链接](http://openvpn.net/release/openvpn-2.1.1.tar.gz)，解压缩到C:\work\openvpn-2.1.1目录下。

修改misc.c文件，在#ifundef ENABLE_PASSWORD_SAVE一行前面加上

#define ENABLE_PASSWORD_SAVE

到install-win32目录，修改openvpn.nsi文件，找到以下代码：

```
; tap-64bit:

DetailPrint “We are running on a 64-bit system.”

SetOutPath “$INSTDIR\bin”

File “${GEN}\tapinstall\amd64\tapinstall.exe”

SetOutPath “$INSTDIR\driver”

File “${GEN}\driver\amd64\OemWin2k.inf”
File “${GEN}\driver\amd64\${PRODUCT_TAP_ID}.cat”
File “${GEN}\driver\amd64\${TAPDRV}”

goto tapend

tap-32bit:

DetailPrint “We are running on a 32-bit system.”

SetOutPath “$INSTDIR\bin”
File “${GEN}\tapinstall\i386\tapinstall.exe”

SetOutPath “$INSTDIR\driver”
File “${GEN}\driver\i386\OemWin2k.inf”
File “${GEN}\driver\i386\${PRODUCT_TAP_ID}.cat”
File “${GEN}\driver\i386\${TAPDRV}”

tapend:

SectionEnd
```

在这段代码最前面插入：

```
!undef GEN
!define GEN “C:\work\gen-prebuilt\”
```

在这段代码最后面插入：

```
undef
!define GEN “..”
```

打开命令提示符，执行如下命令：

```
cd C:\work\openvpn-2.1.1

bash domake-win
```

编译结束后，安装文件会生成在C:\work\openvpn-2.1.1\gen目录下。

**修改OpenVPN配置文件**

修改当前使用的ovpn配置文件，将auth-user-pass改为auth-user-pass pass.txt，同时在相同路径下新增一个名为pass.txt的文本文件，内容为两行，第一行为vpn用户名，第二行为密码。

重新运行OpenVPN，即可不输密码登录vpn，而且此时status窗口的日志输出，也终于能看得清了。

顺便提一句，如果有需要vpn的同学，可以和我联系，目前有合租计划，人均每月6元，联系方式如下：

Twitter: @palfans

OpenVPN客户端编译部分，参考yegle[相关文章](https://yegle.net/2010/05/19/compile-openvpn-2-1-1-in-windows/)，致谢。
