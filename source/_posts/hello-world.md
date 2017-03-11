---
title: Hello World -- 博客搭建记录
date: 2017/2/19 20:46:25
categories:
- Tech
tags:
- Hexo
toc: true
---
博客终于搭好了，QAQ简直一把辛酸泪，还是记录一下。

<!--more-->

## 环境

装环境还是推荐[官方文档](https://hexo.io/docs/)，省得被网上形形色色的教程坑了。

安装 npm 时候奇怪的警告不用在意 - -

要用 Github pages 的可以参考[这篇博客](http://www.cnblogs.com/liulangmao/p/4323064.html)。

## 配置

### 基本信息

站点根目录下的 `_config.yml` 文件包含了 Hexo 本身的配置，此处依然可以参考[官方文档](https://hexo.io/docs/configuration.html)。

最后 deploy 部分，用 ssh 方式的话：

```
deploy:
  type: git
  repo: git@github.com:Username/Username.github.io.git
  branch: master
```
https 方式每次 push 都需要验证用户名密码，不建议。

之后就可以 `init`, `generate`, `server` 啦！

**补充关于站点配置文件里language的写法：对于maupassant主题，应该写 `zh-CN`**

一开始查了[List of ISO 639-1 codes](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes)之后我直接写了`zh`，站点依然是英文且偶有乱码；

后来看到主题文件夹下的 language/ 里只有 `zh-CN` 和 `zh-TW`，改成 `zh-CN`就好了。

再后来看到 [NexT 文档](http://theme-next.iissnan.com/getting-started.html#select-language)里的简体中文写法是`zh-Hans`，就查了查,看到了[这个](https://www.zhihu.com/question/20797118)，挺乱的反正。

### 更换主题

找到了一个[简洁的主题]()，参考[开发者博客](https://www.haomwei.com/technology/maupassant-hexo.html)。

`npm install hexo-renderer-jade --save` 时候的警告不用在意。

**如果要把本地 Hexo 文件也备份到 Github 的话，这里不要直接 clone 作者的版本，参见我的[下一篇文章](/2017/02/26/back-up-blog/)。**

### 修改主题配置

找到站点文件夹里的 `themes/maupassant/_config.yml` 文件，这份是**主题配置文件**。

#### 注释 About 和 RSS订阅页面

```md
menu:
  - page: home
    directory: .
    icon: fa-home
  - page: archive
    directory: archives/
    icon: fa-archive 
#  - page: about
#    directory: about/
#    icon: fa-user
#  - page: rss
#    directory: atom.xml
#    icon: fa-rss
```

#### 添加多说评论

参考了[知名主题 `NexT` 的文档](http://theme-next.iissnan.com/third-party-services.html)

#### 删除多说评论框的分享

多说的分享太花了，干脆删除之。

打开 `themes/maupassant/layout/post.jade`，删除下面的代码（36-50行）。
```jade
    if theme.duoshuo
      div(class='ds-share flat' data-thread-key=page.path, data-title=page.title, data-url=page.permalink)
         .ds-share-inline
            ul.ds-share-icons-16
              li(data-toggle='ds-share-icons-more')
                a(class='ds-more' href='javascript:void(0);') 分享到：
              li
                a(class='ds-weibo' href='javascript:void(0);' data-service='weibo') 微博
              li
                a(class='ds-qzone' href='javascript:void(0);' data-service='qzone') QQ空间
              li
                a(class='ds-qqt' href='javascript:void(0);' data-service='qqt') 腾讯微博
              li
                a(class='ds-wechat' href='javascript:void(0);' data-service='wechat') 微信
            .ds-share-icons-more
```

## 参考
[Hexo 官方文档](https://hexo.io/docs/)
[NexT 主题官方文档](http://theme-next.iissnan.com/getting-started.html)
http://taosama.github.io/2016/03/09/Hello%20World%20%E2%80%94%E2%80%94%20%E5%8D%9A%E5%AE%A2%E6%90%AD%E5%BB%BA%E5%8E%86%E7%A8%8B/#comments
http://theme-next.iissnan.com/third-party-services.html
http://www.cnblogs.com/liulangmao/p/4323064.html
http://www.tuicool.com/articles/2mAfIne
http://www.jianshu.com/p/e99ed60390a8
http://www.jianshu.com/p/465830080ea9
http://www.imooc.com/article/4433
https://www.haomwei.com/technology/maupassant-hexo.html
https://zhuanlan.zhihu.com/p/22191919

