---
author: Palfans
date: 2017-06-05 20:49:13+00:00
draft: false
title: Blog 更换服务器
type: post
url: /blog-changed-server/
categories:
- 技术栈
tags:
- lets encrypt
- ssl
---

算起来，从2005年开始写这个Blog，累计换了好几次服务器。从Windows主机到Linux主机，从朋友托管机器到专业空间服务商，从相当业余的myrice到老牌空间hostgator，在不断折腾中慢慢熟悉了域名，DNS，虚拟空间等各种概念和网站知识。2010年搬到[老薛主机](http://www.laoxuehost.com/)，因为不想折腾了。一晃七年，不能说没有遇到问题，比如网站经常会在中国的半夜出现MySQL服务器挂死的问题，为此我提交了好多问题单。不过一旦提交问题单，解决都非常快。马上新一年的账单又要来了，不过鉴于现在打理网站的时间少之又少，Blog基本处于停止更新的状态，一年估计能写一篇，所以即使年费才几十块钱，也不想再续费了。考虑到家里常年开着一台惠普的小服务器，反正闲着也是浪费电，索性在周末把全站从虚拟空间转移到自家的机器上。再见了，老薛。

![](https://res.palfans.net/img/blog_change_server.jpg)

picture from [vpnranks](https://www.vpnranks.com)
<!--more-->

搬家过程没什么挑战，Docker的出现让部署变得异常容易。开了4个容器，分别放数据库，Nginx，PHP-FPM和FTP，直接导入从原站拿到的备份，总耗时不超过2小时。唯一的坑在部署Let's Encrypt的SSL证书。因为web服务器放在容器里，没有公网IP地址，所以不能采用certbot全自动模式来获取证书。看了下文档，certbot支持通过带manual参数来更新DNS TXT记录的方式来验证域名从而获取证书，命令很简单

```
certbot certonly --manual --preferred-challenges=dns --manual-auth-hook /etc/letsencrypt/authenticator.sh --manual-cleanup-hook /etc/letsencrypt/cleanup.sh -d <domain_name>
```

官方文档提供了一个authenticator.sh的[示例](https://certbot.eff.org/docs/using.html#pre-and-post-validation-hooks)，用来调用Cloudflare DNS API。但我在这里被坑了好久。逼不得已一行行阅读代码，终于发现问题。

```    
# Strip only the top domain to get the zone id

DOMAIN=$(expr <span style="color: #0000ff;">match</span> "<span style="color: #8b0000;">$CERTBOT_DOMAIN</span>" '.*\.\(.*\..*\)')
```


这句正则式需要匹配的域名包含两个“.”，否则返回值为空。如果打算为根域名（abc.com）安装证书，certbot没法获取域名信息，会抛异常。找到问题就好办了。计算机技术发展这么多年，以我的水平能遇到的问题都不能算真正的问题。正则式不好用，就换用dig工具来获取SOA地址，然后就能正常验证域名并获得Let's Encrypt签发的证书。

``` 
DOMAIN=$(dig soa "<span style="color: #8b0000;">$CERTBOT_DOMAIN</span>" | grep -v ^\; | grep SOA | awk '{<span style="color: #0000ff;">print</span> $1}';)
```