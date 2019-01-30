---
title: "从 WordPress 迁到 Hugo"
date: 2019-01-25T21:46:00-06:00
draft: false
url: /moving_wordpress_hugo/
categories:
- 技术栈
tags:
- github
- hugo
- wordpress
---

![](/img/migrate_to_hugo/migration.png)

## 起因 ##

说起来，都不记得这是第几次折腾 Blog 的服务器了。当初为了提高从国内访问的速度，我把搭梯子的 VPS 放在了香港的数据中心，顺便把 Blog 也放在同一台服务器以求节约成本。转眼自己肉翻这么多年，再也没有机会用那把梯子。但从美国家里访问 Blog 就慢得不能忍受，而且因为 VPS 性能不好，时不时就会出现 503 错误。思考再三，决定把站点搬回美国，反正流量可以忽略，至少要满足自己的。

## 选型 ##

### WordPress 的问题 ###

继续使用 WordPress 吗？我有点犹豫了。

1. WordPress 体量越来越大，新版本我都不太会用了。
2. 网站运行速度慢。更新版本以后，常常需要等好几分钟才能打开后台。
3. 离线编辑器不好用。Windows Live Writer 以及后续的 [Open Live Writer](http://openlivewriter.org/) 都不再更新了。
4. 太流行，也就意味着目标大。总是要担心各种来自 PHP，WordPress 以及 MySQL 的安全漏洞。
5. 动态网站遭遇 DDoS 的时候，开销也大大高于静态网站。
6. 备份也不省心，定期备还占地方。

<!--more-->

### Github Pages + Hugo ###

突然有一天，我发现大量的码农网站都是由 [Hugo](https://gohugo.io/) 驱动的，这是个啥？

看了一圈，它就是个轻量（只有一个文件）的静态页面生成器，读取 Markdown 文件，生成静态的 HTML 文件。把这些 HTML 文件提交到 Github Pages，就能访问了。

#### 优势 ####

1. Github Pages 免维护。整个站点全是纯静态的页面，唯一的交互就是评论系统，也是利用了 Github 自己的 Issues。再也不用纠结服务器问题。
2. Github Pages 速度快。还是因为纯静态网页的优势，至少在美国的网络环境我能秒开各个托管在 Github 的网站。
3. 自带 SSL 证书。Github 自动给所有 Pages 网站加上小绿锁，包括自定域名。虽然仍是基于 Let's Encrypt 的免费证书，但每三个月自动续期，完全不用操心。
4. Hugo 足够快。我试了下我总共需要生成 976 个页面，耗时 2 秒钟。
5. Hugo 自带服务器端。调试非常容易。
6. 熟悉 git 操作。公司体量较大，现在终于开始慢慢转向使用 git 做版本控制。我也可以借此熟悉 git 的各种操作。
7. 熟悉 markdown 语法。
8. 感觉网站放在 Github 是码农的认证标识。

#### 劣势 ####

1. 之前 WordPress 的很多标配功能没有了，花时间调试的模板也不能直接用。
2. 上手曲线不够平缓，有学习门槛。
3. 相当依赖命令行。WordPress 可以利用在线后台更新内容，但现在就必须本地安装 Git，Hugo，Markdown 编辑器，写一篇文章就需要提交一次。虽然可以利用 Travis CI 来自动生成静态页面，但免费用户只能读取 Github 的公有库，读不了私有库，暂时没考虑。
4. 没想好。。。

## 开干

### 搭环境

这个就比较简单了，安装 Git，Hugo 和 Typora，连接到远端的 Github 库，Google 一下，各种教程应有尽有。

### 数据迁移

网上都在推荐一款插件 [wordpress-to-hugo-exporter](https://github.com/SchumacherFM/wordpress-to-hugo-exporter) ，实测是个**坑**。在最新的 WordPress 上报函数重复定义的错误，自己段位低，解决不了。我用了一个 python 脚本 [exitwp-for-hugo](https://github.com/wooni005/exitwp-for-hugo)，需要先导出 XML，再转成 Markdown，不过执行效率非常高，几百篇文章，几秒搞定。每篇文章转换后都自带了 URL 属性，直接就能用，最后的链接保持了 domain/title 的形式，完全不影响 Google 的搜索结果。

## 最后 ##

其实这篇文章是试水，看看新建文章到底好不好使。

就这。

