---
title: "通过 VPN 远程办公"
date: 2021-03-19T10:42:24-05:00
draft: false
author: Palfans
url: /work_from_home_via_vpn/
categories: 
- 技术栈
tags: 
- vpn
- 虚拟机
---

## 需求 ##
作为一名实施人员，通常情况下需要通过各种 VPN 连接到公司和客户的工作环境来完成任务。尤其是疫情的影响，完全在家工作，对 VPN 的利用就更重了。

但是，VPN 装多了，各种虚拟网卡驱动相互之间不一定能好好兼容，即使是 Windows 10 也很容易蓝屏，要是不幸发生在工作过程中，尤其是更新生产系统，那就有得事情做了。

## 解决方案 ##
我能想到的解决方案，还是虚拟机。把所有的 VPN 都装在虚拟机里边，即使不幸虚拟机挂了，也可以在几分钟内恢复一份快照，而且这个异常完全不会影响到宿主机的系统。

在 Windows 上，常用的虚拟机解决方案有
- 商用的 VMWare
- 免费的 Virtualbox
- Windows 10 自带的 Hyper-V

在 Windows 10 ver20H2 之前，Hyper-V 和其他两个解决方案是互斥的，如果启用了 Hyper-V，那么 VMWare 和 Virtualbox 都不能正常启动。更新到 20H2 之后，它们就可以共存了。
<!--more-->
## 虚拟机设置 ##

为了充分利用宿主机已经安装的程序和各类文件，这套虚拟机只是用来连接到工作网络，宿主机以其为跳板完成工作任务。所以虚拟机选择 Linux 作为操作系统，内存分配 1GB 完全足够。

网络设置方面，虚拟机需要能访问互联网，同时也被宿主机访问，所以网卡类型选择 `bridge` 或者 `nat + host-only`，通过 `ip a` 命令查看虚拟机的网络地址，测试宿主机能否 ping 通这个地址。

接下来就是安装 VPN 的客户端和相关配置，这里以 CentOS 7 为例。
### OpenVPN ###

#### 安装 ####
```shell
# 安装 epel
sudo yum install -y epel-release

# 安装 openvpn
sudo yum install -y openvpn
```

#### 配置 ####
创建一个 Shell Script 脚本
```shell
#!/bin/bash
# start OpenVPN client daemon
sudo openvpn --daemon --cd <work-folder> --config <ovpn-file> --log-append /var/log/openvpn.log

# add traffic forward firewall rule
MASQUERADE=$(sudo firewall-cmd --zone=public --query-masquerade)
if [[ "x$MASQUERADE" != "xyes" ]]; then
    sudo firewall-cmd --zone=public --add-masquerade
fi
```

#### 运行 ####
直接运行这个脚本

### Cisco VPN Client ###

#### 安装 ####
```shell
# 安装 epel
sudo yum install -y epel-release

# 安装 vpnc
sudo yum install -y vpnc
```

#### 配置 ####
创建一个 Shell Script 脚本
```shell
#!/bin/bash
# start Cisco VPN Client
sudo vpnc $(dirname $(readlink -f $0))/<cisco-legacy-vpn-config-file>

# add traffic forward firewall rule
MASQUERADE=$(sudo firewall-cmd --zone=public --query-masquerade)
if [[ "x$MASQUERADE" != "xyes" ]]; then
    sudo firewall-cmd --zone=public --add-masquerade
fi
```
参考下列例子，准备好 `cisco-legacy-vpn-config-file`，文件名随意，放置在脚本的相同目录

Cisco VPN Client Config Sample
```ini
IPSec gateway <VPN server IP>
IPSec ID <Group ID>
IPSec secret <Group Password>
Xauth username <Username>
Xauth password <Password>
Local Port 0
```

#### 运行 ####
直接运行这个脚本

### Cisco AnyConnect Secure Mobility Client ###

#### 安装 ####
```shell
# 安装 epel
sudo yum install -y epel-release

# 安装 openconnect
sudo yum install -y openconnect
```

#### 配置 ####
创建一个 Shell Script 脚本
```shell
#!/bin/bash

# add traffic forward firewall rule
MASQUERADE=$(sudo firewall-cmd --zone=public --query-masquerade)
if [[ "x$MASQUERADE" != "xyes" ]]; then
    sudo firewall-cmd --zone=public --add-masquerade
fi

# start openconnect
# automatically input password
PASSWORD='<password>'
(
    echo $PASSWORD
    read -p "" X
    echo $X
) |
    sudo openconnect \
        --user <username> \
        --os=win \
        --csd-wrapper=<bogus-csd-wrapper-script> \
        <VPN server IP>
```
Cisco AnyConnect 对客户端的要求比较多，需要检查客户端是否符合服务器安全设置，有些服务端还配置了 2FA，所以不能做到脚本完全自动连接。首先参考下列例子，准备好 `bogus-csd-wrapper-script`，文件名随意，放置在脚本的相同目录，以此提交一个符合服务端安全设置的连接请求。

Cisco Anyconnect CSD wrapper Sample
```shell
#!/bin/bash

if ! xmlstarlet --version >/dev/null 2>&1; then
    echo "************************************************************************" >&2
    echo "WARNING: xmlstarlet not found in path; CSD token extraction may not work" >&2
    echo "************************************************************************" >&2
    unset XMLSTARLET
else
    XMLSTARLET=true
fi

DATA='endpoint.os.version="Linux";
endpoint.os.servicepack="4.17.9-200.fc28.x86_64";
endpoint.os.architecture="x64";
endpoint.policy.location="Default";
endpoint.device.protection="none";
endpoint.device.protection_version="3.1.03103";
endpoint.device.hostname="<VPN server hostname/IP>";
endpoint.device.port["9217"]="true";
endpoint.device.port["139"]="true";
endpoint.device.port["53"]="true";
endpoint.device.port["22"]="true";
endpoint.device.port["631"]="true";
endpoint.device.port["445"]="true";
endpoint.device.port["9216"]="true";
endpoint.device.tcp4port["9217"]="true";
endpoint.device.tcp4port["139"]="true";
endpoint.device.tcp4port["53"]="true";
endpoint.device.tcp4port["22"]="true";
endpoint.device.tcp4port["631"]="true";
endpoint.device.tcp4port["445"]="true";
endpoint.device.tcp4port["9216"]="true";
endpoint.device.tcp6port["139"]="true";
endpoint.device.tcp6port["53"]="true";
endpoint.device.tcp6port["22"]="true";
endpoint.device.tcp6port["631"]="true";
endpoint.device.tcp6port["445"]="true";
endpoint.device.MAC["FFFF.FFFF.FFFF"]="true";
endpoint.device.protection_extension="3.6.4900.2";
endpoint.fw["IPTablesFW"]={};
endpoint.fw["IPTablesFW"].exists="true";
endpoint.fw["IPTablesFW"].description="IPTables (Linux)";
endpoint.fw["IPTablesFW"].version="1.6.1";
endpoint.fw["IPTablesFW"].enabled="ok";
'
shift

TICKET=
STUB=0

while [ "$1" ]; do
    if [ "$1" == "-ticket" ]; then
        shift
        TICKET=${1//\"/}
    fi
    if [ "$1" == "-stub" ]; then
        shift
        STUB=${1//\"/}
    fi
    shift
done

PINNEDPUBKEY="-s ${CSD_SHA256:+"-k --pinnedpubkey sha256//$CSD_SHA256"}"
URL="https://$CSD_HOSTNAME/+CSCOE+/sdesktop/token.xml?ticket=$TICKET&stub=$STUB"
if [ -n "$XMLSTARLET" ]; then
    TOKEN=$(curl $PINNEDPUBKEY -s "$URL" | xmlstarlet sel -t -v /hostscan/token)
else
    TOKEN=$(curl $PINNEDPUBKEY -s "$URL" | sed -n '/<token>/s^.*<token>\(.*\)</token>^\1^p')
fi
COOKIE_HEADER="Cookie: sdesktop=$TOKEN"
CONTENT_HEADER="Content-Type: text/xml"
URL="https://$CSD_HOSTNAME/+CSCOE+/sdesktop/scan.xml?reusebrowser=1"
curl $PINNEDPUBKEY -H "$CONTENT_HEADER" -H "$COOKIE_HEADER" --data "$DATA;type=text/xml" "$URL"
```

#### 运行 ####
直接运行这个脚本，如果有 2FA 验证，在提示符后输入验证码

## 宿主机设置 ##
假设创建的虚拟机采用了 `nat + host-only` 的网络模式，其中 `host-only` 的地址为 `192.168.147.188`。在虚拟机创建好 VPN 连接后，按照远程工作环境的网络地址 `10.100.16.5`，为宿主机添加路由。

打开`管理员模式`的命令行提示符，输入以下命令，宿主机在访问 10.100.16.1 - 10.100.16.254 的所有服务器时，会通过虚拟机的网络接口收发数据，以此达到连接远程工作环境的要求。

```bat
route delete 10.100.16.0
route add -p 10.100.16.0 mask 255.255.255.0 192.168.147.188
```