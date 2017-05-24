---
title: Python 小工具 —— BalloonSender(又名AC提醒机)
date: 2017-05-06 22:56:21
categories: TECH
tags: Python
---
一个小工具，从 CQUOJ 抓取某场比赛的 AC 记录并显示在屏幕上，提示志愿者发气球。

代码重构中，先占坑。

<!--more-->

### 漫天飞的 bug

校赛当天真是爆炸啊，刚开场发现抓下来的不是 AC 记录，而是全部记录！参数肯定是没错的，实在搞不懂情况只好临时加了一行 `if item[3] != 'Accepted':`判 AC。代码还是不够鲁棒……

还有一个非常尴尬的情况是，OJ 服务器不堪重负，挂了好多次。而我根本没考虑过这种情况，服务器一挂，因为代码里没设定时器，它会一直一直等在那里等服务器响应，基本就废了……重启之后，所有已 check 记录会一股脑涌出来，因为我的备份机制由于奇妙的编码问题也挂掉了……真的心疼发气球的工作人员。

目前大修代码中，采用新的备份方法，计划添加定时器以应对服务器超时的状况。

### 参考

https://groups.google.com/forum/#!topic/python-cn/UXbIoLaWDxw
http://effbot.org/tkinterbook/tkinter-index.htm#class-reference
http://blog.csdn.net/hewei0241/article/details/31370487
https://docs.python.org/2/library/stdtypes.html#set