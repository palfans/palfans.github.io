---
author: Palfans
date: 2006-11-23 14:22:22+00:00
draft: false
title: 让网页上的图片飞起来
type: post
url: /let-your-pic-fly/
categories:
- 闲聊
tags:
- javascript
- 技术栈
- 电脑
- 网页
---

首先，找一个图片比较多的网站，用IE（或基于IE核心）的浏览器打开。然后，在地址栏中敲入下面的代码。你会发现，网页上的图片全部都飞起来了，像是被龙卷风刮过，而且，你敲回车越多，它们飞得越快。经测试，Firefox不支持这堆代码，IE需要不禁用Javascript。


>javascript:R=0; x1=.1; y1=.05; x2=.25; y2=.24; x3=1.6; y3=.24; x4=300; y4=200; x5=300; y5=200; DI=document.images; DIL=DI.length; function A(){for(i=0; i-DIL; i++){DIS=DI[ i ].style; DIS.position='absolute'; DIS.left=Math.sin(R*x1+i*x2+x3)*x4+x5; DIS.top=Math.cos(R*y1+i*y2+y3)*y4+y5}R++}setInterval('A()',5); void(0);
