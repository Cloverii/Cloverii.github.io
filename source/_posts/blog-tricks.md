---
title: 博客主题的定制
date: 2017-05-14 09:44:03
categories:
tags:
---
我使用的主题是[Maupassant](https://github.com/tufu9441/maupassant-hexo), 跟它的名字一样，小而美。不过作者本人表示这个主题更适合生活博客来着，具体表现在比如引用的样式上？抽空尝试研究下改一改好了。

要更改配置，必须关注两份重要的配置文件，站点根文件夹里的 `_config.yml`，下面称为**站点配置文件**，和 `themes/maupassant/_config.yml`，称为**主题配置文件**。
<!--more-->

## 注释 RSS 订阅页面
```bash
menu:
  - page: home
    directory: .
    icon: fa-home
  - page: archive
    directory: archives/
    icon: fa-archive 
  - page: about
    directory: about/
    icon: fa-user
#  - page: rss
#    directory: atom.xml
#    icon: fa-rss
```
## 添加 About 页面
在source目录下建立相应名称的文件夹，然后在文件夹中建立index.md文件，并在index.md的front-matter中设置layout为layout: page。若需要单栏页面，就将layout设置为 layout: single-column。
```
title: About
layout: page
comments: false
```

## 修改网站图标

嗯……图标不知道有木有人知道出处，是某灵异漫画里御姐女主的[花押](https://zh.wikipedia.org/wiki/%E8%8A%B1%E6%8A%BC)。印象里是贴吧的好孩子修复的，不过我已经联系不到人了。

用 [Background Burner](https://burner.bonanza.com/) 抠的图，[](https://iconverticons.com/online/) 转的 .ico。感谢以上工具的开发者。

## 删除首页显示文章评论数
换 disqus 之后不知为何首页文章评论数总有几篇文章不能正常显示，disqus 访问的问题？干脆删了。
`/themes/maupassant/layout/index.jade` 中删除下面的语句。

```jade
      if theme.duoshuo
        a.ds-thread-count(data-thread-key=post.path, href=url_for(post.path) + '#comments')
	  if theme.disqus
        a.disqus-comment-count(data-disqus-identifier=post.path, href=url_for(post.path) + '#disqus_thread')
```

## 存档和标签页面不分页
需要用到 `hexo-generator-archive` 和 `hexo-generator-tag` 两个插件（理论上来说默认应该安装好了的），并在站点配置文件下加入以下字段：
```bash
archive_generator:
  per_page: 0
  yearly: true
#  monthly: true
#  daily: false

tag_generator:
  per_page: 0
```
我使用的主题 `Maupassant` 修改 `monthly` 和 `daily` 字段无效，猜测是主题本身做了限制，所以直接注释掉了。

## 添加 404 页面
在站点根目录下新建 `source/404.md`, 内容如下：（首页记得改成自己的）
```
---
title: 404
layout: page
comments: false
---
### 嗨呀，好像出了什么问题……这个页面不存在了

页面将在<span id="jumpTo"></span>秒内自动跳转回[首页](https://cloverii.github.io)


<script type="text/javascript">
function countDown(secs, surl) {
    var jumpTo = document.getElementById('jumpTo');
    jumpTo.innerHTML = secs;
    if (--secs > 0) {
        setTimeout("countDown(" + secs + ",'" + surl + "')", 1000);
    } else {
        location.href = surl;
    }
}
</script>
<script type="text/javascript">countDown(5,'/');</script>
```

## Todo
1. 所有标题相关的字体和正文字体都不一样，导致文章里最高设二级标题有时候看起来就很奇怪，上次瞅了一下 css, 瞅得眼疼也搞不懂，有空再好好研究。
2. 引用的样式，应该是修改 `themes/maupassant/source/css/style.scss` 里的这一块
```
blockquote:before,.stressed-quote:before {
    content: "\201C";
    display: block;
    font-family: times;
    font-style: normal;
    font-size: 48px;
    color: #444;
    font-weight: bold;
    line-height: 30px;
    margin-left: -50px;
    position: absolute;
}
```

***补充***
多说已宣布将于 2017.06.01 停止服务，改用 disqus。
**~~添加多说评论~~**

参考了[知名主题 `NexT` 的文档](http://theme-next.iissnan.com/third-party-services.html)

**~~删除多说评论框的分享~~**

多说的分享太花了，干脆删除之。

打开 `themes/maupassant/layout/post.jade`，删除下面的代码（36-50行）。
```
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
https://www.haomwei.com/technology/maupassant-hexo.html
https://loyea.com/2017/03/01/hexo-tricks/
https://github.com/hexojs/hexo-generator-archive
http://sobaigu.com/hexo-archives-show-all-in-one-page.html
https://github.com/tufu9441/maupassant-hexo/issues/73