---
title: 待解决
date: 2017-03-11 13:36:38
categories: OTHERS
tags: 
---
<!--more-->
[多说已经宣布到170601正式关停服务](http://dev.duoshuo.com/threads/58d1169ae293b89a20c57241)，这两个问题不需要解决了……准备换disqus
~~多说评论框登陆按钮重叠~~
~~重叠大小与多说评论框的主题有关，与博客主题似乎也有关系。目测如果能去掉登陆按钮的边框应该就正常了。~~

~~多说头像的 http~~
~~[2016年6月15日以后使用 Github Pages 创建的站点，并且 使用.github.io 域名的，均会强制使用 https 连接。](https://help.github.com/articles/securing-your-github-pages-site-with-https/)~~

~~然而多说的用户头像是 http，黄色小三角逼死强迫症了 QAQ~~

~~有空研究。~~

### 主题代码高亮显示的不是等宽字体

需求: 等宽 + 正常字号

*070326 补充：*
`/themes/maupassant/source/css/syle.scss` 文件里查看发现 highlight 部分是有 `font-family: Menlo, Consolas, monospace;` 这么一句的，怀疑是电脑上没有这些字体于是在前面加了个 `Monaco` ，这下终于正常了。但是之后再去掉 `Monaco` 似乎也正常了，当然眼拙如我是看不出来显示的究竟是哪个字体的，就想把字体挨个注释掉观察到底是哪个在起作用。然后悲剧就出现了 - - 本地调试的时候页面变成了裸奔，CSS全部丢失不见，把全部修改撤销也没有用……现在迷之尴尬。

### 系统时间莫名提前
总是提前2天零20个小时，Win10 + Ubuntu 双系统均有这个问题。

没找到解决方法，待进一步观察。

*070326 补充：*
重装 Ubuntu 之后大概半个月没碰到这个问题了，结果昨天笔试时候时间突然又提前了，没顾上修改，今天早上自己恢复正常了。