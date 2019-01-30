---
author: Palfans
date: 2005-12-30 13:41:32+00:00
draft: false
title: 通过javascript加密和解密
type: post
url: /javascript-encrypt-decrypt/
categories:
- 闲聊
tags:
- javascript
- Unicode
- 加密
- 解密
---

单位和家里都是用的移动的宽带，我常常去那里的动感影院看电影，不过有时候觉得有点卡，就想通过下载软件全部下下来再看，一开始可以通过real的实际路径去下载，后来real也不行了，只好去看它的网页代码，如下：

```Javascript
<script language=Javascript>
<!--var Words ="%3Cframeset%20border%3D%220%22%20frameborder%3D%220%22%20framespacing%3D%220%22%20rows%3D%22*%22%3E%0D%0A%20%20%3Cnoframes%3E%0D%0A%20%20%3C/noframes%3E%0D%0A%20%20%3Cframe%20frameborder%3D%22NO%22%20marginwidth%3D%220%22%20name%3D%22upperframe%22%20noresize%20src%3D%222233d86.asp%3Ffilm1%3D7113%26film2%3D2848%26film3%3D%22%20scrolling%3D%22auto%22%3E%0D%0A%3C/frameset%3E"
function SetNewWords()
{
    var NewWords;
    NewWords = unescape(Words);
    document.write(NewWords);
}
SetNewWords();
// -->
</script>
```

<!--more-->网页通过escape和unescape来实现加密和解密，仔细观察，可以发现，其实加密后的内容就是符号的unicode编码，所以很容易就能得到未加密的内容。专门弄了一段代码来简单实现


将目标内容复制到下面的文本框中，点击加密/解密按钮，即可得到加密/解密后的结果。





