---
author: Palfans
date: 2009-09-18 15:42:27+00:00
draft: false
title: 捣腾Google Voice
type: post
url: /google-voice-and-sipsorcery/
categories:
- 技术栈
tags:
- Google
- GV
- voip
- 电话
---

前段时间，流行捣腾Google Voice，Google确实有钱有闲，通过Google Voice可以免费拨打全美所有的电话，固话、手机均可，现在已经发展到可以免费拨打加拿大，更重要的是，还免费提供一个真正无限制使用的美国电话号码。虽然以我的角度来看，Google Voice在最近十年内都不会免费拨通中国，也就是说对于没有国际业务，尤其是北美业务的人来说，这就是个无意义的概念，不过国内的广大人民依旧趋之若鹜，不管有用没用，拿了再说，而且往往还一人多号，估计现在Google也怕了，总共就放100万个号，一大半都在中国人手中。所以现在Google Voice的邀请审核基本上停止，仍然还没拿到邀请的同学，只能耐心等待了。

<!--more-->我最喜欢跟风了，所以我也开始捣腾这玩意儿。拿到邀请需要激活，然后简单配置，就能用了，网上到处都是教程，我也不多说，具体参见这里。玩完简单的，就要想办法搞高级点儿。基于Google Voice的免费通话，其实是voip回拨，按照之前教程的配置，虽说来电能直接转到手机、skype、gmail等等各种PSTN或者SIP终端，但是去电拨号就相当不方便，每次都要打开Google Voice的页面，选择Call才能成功拨号。于是，现在我们需要一部软电话。

第一次接触软电话，是进HP后做的第一个实验项目，涉及到SIP协议，用的软件是x-lite。这个软件的界面就是一部商务手机，居然真的能正常的打电话和发短信，当我的手机开始响起来电铃声的时候，我震惊了。那段时间，我没事就用软电话拨自己的手机，然后自言自语。

我现在用eyebeam，它和x-lite一个妈生的，我个人感觉它俩从界面到功能，都没什么太大区别。除了需要电话软件，还需要Google Voice账号、Gizmo5账号、SipSorcery账号各一个，相互之间的配置也不难，Google Voice转移到Gizmo5，Gizmo5再转移到Sipsorcery，这些步骤在前面那个教程里边都有。这里唯一有点儿麻烦的是Sipsorcery的配置，它是基于一种叫做Ruby的类C语言。软电话能不能正常拨通，取决于用Ruby写的Dial Plan是否正确。我捣腾了一阵子，现在终于能用软电话直接拨打美国的电话了。配置如下：

```ruby
# SIP tracing : true or false
sys.Trace = false

sys.Log("call from #{req.Header.From.FromURI.ToString()} to #{req.URI.User}.")
sys.Log("-------------------------------------------------------------\n")

if sys.In then
    # Do your INCOMING call processing customisations here.
    if sys.IsAvailable() then
        sys.Dial("#{sys.Username}@local",35)
    else
        sys.Respond(480, "#{sys.Username} Not available")
    end
else
    # Do your OUTGOING call processing customisations here.
    sys.Log("== Called Number #{req.URI.User} ")
    case req.URI.User
    when /^1/
        sys.Log(" Dialing USA #{req.URI.User} through Google Voice service\t")
        sys.Log("*****************************************************\n")
        sys.GoogleVoiceCall("myname@gmail.com","mypass","1747XXXXXXX","#{req.URI.User}")
    else sys.Dial("nonoh")
    end
end
end
```

其中myname@gmail.com是Google Voice对应的账户，mypass是GV的密码，1747XXXXXXX是GV转移的Gizmo5的号码。这个Dial Plan的目的就是国家代码为1的电话时，使用Google Voice拨号，拨其他号码时，使用nonoh的设置，这个nonoh是另外一个voip服务商，注册一个账号可以免费打一段时间电话，我用它能正常拨通中国的座机和手机，当然纯属测试，我的手机套餐每个月都从来没用完过。
