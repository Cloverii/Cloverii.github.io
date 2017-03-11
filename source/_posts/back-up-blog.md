---
title: Hexo 博客备份
date: 2017-02-26 15:11:09
categories: TECH
tags: [Hexo, Git]
toc: true
---

博客虽然搭好了，但 Github 上同步的只有生成的静态文件，所以为了备份 Hexo 本地文件，还要把它们也丢进 Github 博客仓库的其他分支~

<!--more-->

灵感来自[这篇博客](http://jinyanhuan.github.io/2015/03/30/hexo-build-four/)。

本篇的前提是已经搭建好博客并且将静态文件部署到 Github 仓库 username.github.io 的 master 分支~

### 新建本地仓库

以下操作在本地 Hexo 根目录进行：初始化仓库，添加远程库，新建 backup 分支，并 add 所有文件。由于 Hexo 的安装目录下有 `.gitignore` 文件，所以可以直接 `add -A`。

```bash
git init
git remote add origin git@github.com:Cloverii/Cloverii.github.io.git
git checkout -b backup
git add -A
```

这个时候出问题了 - - 具体描述没记，总之是提示主题目录 maupassant/ 不能被 add，因为它也是一个项目。解决方法是将其作为 submodule 添加到主项目里。

### 添加主题 submodule

把主题目录删掉，重新 fork 了一份主题，再添加 submodule。

```bash
rm -rf themes/mapassant
git submodule add git@github.com:Cloverii/maupassant-hexo.git
```

主题修改完后，push 一下。

```bash
git add -A // 主题目录下操作
git commit -m "modify _config.yml & delete duoshuo share to"
git push origin master:master 
```

以后每次更新主题也是一样的套路。

之后再回到 Hexo 根目录，就能成功 add 新主题了。

### 删除主题 submodule

如果需要删除 submodule的话：

1. 删除主目录下 .gitmodules 文件中对应 submodule 的部分。

2. 删除 .git/config 中对应 submodule 的部分。

3. `git rm -- cached [submodule_path]`，**注意 path 末尾不要有 `/`**。我第一次删除没注意这点,结果删除完准备重新添加的时候报了奇怪的信息 - -

### 推送到远程主机

我本地只有 backup 分支，至于放静态网页的 master 分支没有丢到本地，反正每次 deploy 就好。

**注意： 先 commit 子项目，再 commit 父项目。push 同理。**

```bash
git add -A
git commit -m "new theme"
git push origin backup:backup
```

### 恢复备份

换电脑后，将 backup 分支clone 到本地。

```bash
git clone -b backup git@github.com:Cloverii/Cloverii.github.io.git
cd Cloverii.github.io
ls themes/mapassant
```

这时发现主题目录为空。

[文档](https://git-scm.com/book/zh/v1/Git-%E5%B7%A5%E5%85%B7-%E5%AD%90%E6%A8%A1%E5%9D%97)告诉我们，应该执行下面的操作：

```bash
git submodule init
git submodule update
```

**(有人提到1.6以后版本也可以直接用 git clone –recursive 代替)**

但执行 `init` 时提示“子模组 'themes/maupassant' (git@github.com:Cloverii/maupassant-hexo.git) 未对路径 'themes/maupassant' 注册
”，解决方法：先执行 `sync`。

```bash
git submodule sync
git submodule init
git submodule update
```

update 时显示如下：

```bash
正克隆到 'themes/maupassant'...
remote: Counting objects: 1325, done.
remote: Compressing objects: 100% (6/6), done.
remote: Total 1325 (delta 0), reused 0 (delta 0), pack-reused 1319
接收对象中: 100% (1325/1325), 560.90 KiB | 61.00 KiB/s, done.
处理 delta 中: 100% (754/754), done.
检查连接... 完成。
子模组路径 'themes/maupassant'：检出 '103112dc4d4a322611638af467ebfc6d41c1ec16'
```

### 碎碎念

因为对 Github 不熟，- - 折腾了大概一天吧。

submodule 简直有毒 QAQ

### 参考
http://jinyanhuan.github.io/2015/03/30/hexo-build-four/
http://www.leyar.me/backup-your-blog-to-github/
http://blog.csdn.net/feelingjun/article/details/52301548
http://blog.k-res.net/archives/1595.html
http://blog.csdn.net/bailyzheng/article/details/50825215
