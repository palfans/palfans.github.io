---
author: Palfans
date: 2010-09-17 20:30:06+00:00
draft: false
title: 利用SipSorcery、Google Voice和PBXes实现由PSTN电话发起的回拨
type: post
url: /pstn-gv-callback/
categories:
- 技术栈
tags:
- Google
- voice
- voip
- 回拨
- 电话
---

Google Voice（简称GV）是Google公司的又一个伟大产品，它其实是一个回拨系统。用户使用时，在GV的网页上选择自己用来接听的电话号码，并输入对端号码，系统就会分别接通双方，开始通话。现在，Google更进一步，Gmail界面中的Google Talk能够直接输入对端号码发起通话。Google已经承诺，至少到2010年底，GV拨打美国、加拿大全免费。如果我们要拨打美加的电话，这是很不错的选择。我们可以利用Gmail直接发起通话，也能通过手机访问Voice Mobile页面或拨号软件发起通话。但是，家里常用的PSTN电话既不能访问网页，也不能运行拨号软件，应该怎么办呢？

<!--more-->



这里就该SipSorcery（简称SS）出场了。SS和PBXes（本文也要用到）、Voxalot等类似，都是提供SIP交换的服务网站，不过SS支持使用Ruby语言自定义拨号计划，这样，就能通过编程实现各种要求。另外，SS的作者Aaron一直在维护和改进这套系统，他新增了一个接口，可以直接拨打GV，而不需要等待回拨，相当方便。再加上PBXes，就能方便的搭建一个PSTN回拨平台，顺利使用GV的免费大餐。

这篇post主要用来总结一下我的设计思路，而不是一篇手把手的教程。



![](/img/pstn-gv-callback/tech_pstn_callback.png)




在这个设计中，除了GV，还引入了Localphone（简称LP）。这是一家老牌的voip运营商，它提供相当丰富的voip相关服务。通过LP拨打中国、印度等非美国号码的资费比GV更廉价，拨到中国的每分钟费率折合人民币仅仅8分钱。将来如果有更廉价的运营商出现，只要它支持SIP拨叫，就可以轻松的在SS中进行替换。在这里，LP的作用就是拨出电话，并落地到PSTN网络中。

通过PSTN电话拨打到SS触发回拨时，不能附加参数，比如身份验证码和需要拨打的对端号码。这时就引入了PBXes，这套交换系统提供了CallThru功能，能在回拨接通后进行身份校验和接收对端号码。最后将数据送回SS进行处理，发起回拨的第二个链路。

主要工作都是由SS来完成，涉及到的代码主要是

1. 判断回拨的电话归属地，并采用最经济的方式进行拨叫

2. sys.Callback(call_leg_1, call_leg_2)命令，用以发起回拨

3. 回拨过程中，再次调用GV回拨接口来拨打美国电话时，需要单独编写一段拨号计划，用来将GV回拨转化为一个SIP代理进行拨叫

4. PSTN电话如果不支持呼叫等待，在触发回拨后，可能来不及挂断电话，这样就不能接收到系统回拨回来的来电信号，导致回拨失败。这时可以考虑让回拨延时30秒，保证触发电话顺利挂机。[early_music@iptel.org](mailto:early_music@iptel.org) 这个early media资源在这里就很有帮助了

5. PSTN电话只需要触发回拨，不需要接通，因此在触发完成后，SS系统要及时回复一个拨叫失败的信号，避免产生费用

6. 至于用来拨打的电话号码，只要是能够转接到SIP服务器的号码都能使用，比如IPKALL，SipGate等等。而且，整个触发过程，这个号码都没有接通，所以不管是哪个国家的号码都没问题，不会产生任何费用。我使用了一个美国号码，一个德国号码和一个英国号码来作为触发号码，用来保证回拨系统随时都能正常工作。

本文涉及到的网站分别是：

[Google Voice](http://voice.google.com)   [SipSorcery](http://www.sipsorcery.com/mainsite/Home/Portal)   [Localphone](http://www.localphone.com/)   [PBXes](http://www.pbxes.com)   [IPKALL](http://www.ipkall.com)   [SipGate](http://www.sipgate.com)
