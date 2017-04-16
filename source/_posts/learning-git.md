---
title: Git 学习记录
date: 2017-03-17 00:19:40
categories: LEARNING
tags: [Git]
toc: true
---

一不留神把去年学习 Git 时候的笔记翻出来了，稍作整理发出来。半年多后发现自己能记住的还是那几个常用的命令 - - 以及那个时候我的系统语言居然还是中文 233

<!--more-->

## 安装git
 `sudo apt-get install git` 
 `git config --global user.name "Your Name"`
 `git config --global user.email "email@example.com"`  
 参数 `--global` 可选

## 安装版本库
 `git init` 创建一个空目录 打开
  当前目录下会出现一个 .git 的隐藏目录

 **工作区是电脑里能看见的目录。隐藏目录 .git 是版本库，其中有暂存区， HEAD 指针。**
仓库初始化时不会自动创建 master 分支。

**版本控制系统只能跟踪文本文件的改动，二进制文件不行**

### 添加文件到版本库
`git add <file> `  把提交的修改放到暂存区
`git commit -m "This is the description" ` 把暂存区的修改提交到分支
`git commit --amend` 修改最后一次提交的注释（在`.git/COMMIT_EDITMSG`中）

## 修改

`git status` 查看仓库当前状态
`git diff` 查看difference
`git log` 查看提交历史，按时间倒序显示日志 `--prety=oneline` 在一行内显示
`git reset --hard commit_id` reset为某个版本(当前版本用HEAD表示，前一个 HEAD^，前100个 HEAD~100)
`git reflog` 查看命令历史

进行修改后
```bash
minway@Minway:~/leetcode$ git status
位于分支 master
尚未暂存以备提交的变更：
  （使用 "git add <file>..." 更新要提交的内容）
  （使用 "git checkout -- <file>..." 丢弃工作区的改动）

    修改:         readme.txt

修改尚未加入提交（使用 "git add" 和/或 "git commit -a"）
```

执行完 `git add <>` 之后
```bash
minway@Minway:~/leetcode$ git status
位于分支 master
要提交的变更：
  （使用 "git reset HEAD <file>..." 撤出暂存区）

    修改:         readme.txt
```
执行完 `git commit` 之后
```bash
minway@Minway:~/leetcode$ git status
位于分支 master
无文件要提交，干净的工作区
```

## 撤销修改

- 在工作区修改，还未 add 到暂存区
   `git commit checkout -- <file>` 
    `checkout` 命令即为用版本库的版本替换工作区的版本
- 已 add 到暂存区
   `git reset HEAD file`
    `git commit checkout -- <file>`
- 已经提交到版本库
   `git reset ***` 版本回退

## 删除文件
`$ git rm <file>` 从版本库中删除文件
再使用 `git commit` 提交。

## 远程仓库
`git remote add origin git@github.com:Cloverii/***.git` 关联远程库
`git clone git@github.com:Cloverii/***.git` 从远程库克隆
`git://` 使用 ssh 协议，速度较 http 快。

## 分支管理
`git checkout -b <name>` 创建并切换分支
`git branch <name>` 创建分支
`git checkout <name>` 切换分支
`git branch` 查看分支，当前分支用 * 标出
`git merge <name>` 合并指定分支到当前分支
`git branch -d <name>` 删除指定分支

合并分支时，Git通常会尽量使用 Fast forward 模式。此模式下删除分支后会丢掉分支信息。
`git merge --no-ff` 强制禁用 Fast forward 模式。这时会在 merge 时生成一个新的 commit。
有时 Git 无法自动合并冲突，必须先解决冲突后再提交，完成合并。
`git log --graph` 命令可以看到分支合并图。
## 忽略特殊文件
需要忽略某些文件时，可以编写 `.gitignore` 文件。

忽略文件的原则是：
1. 忽略操作系统自动生成的文件，比如缩略图等；
2. 忽略编译生成的中间文件、可执行文件等，也就是如果一个文件是通过另一个文件自动生成的，那自动生成的文件就没必要放进版本库，比如Java编译产生的.class文件；
3. 忽略你自己的带有敏感信息的配置文件，比如存放口令的配置文件。

`git add -f <file>` 强制添加文件
`git check-ignore` 检查 `.gitignore` 文件
## 配置别名
```bash
[alias]
    co = checkout
    cm = commit
    br = branch
    st = status
    lg = log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit
    last = lg -1
```

## 参考
[廖雪峰老师的 Git 教程](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)。
https://segmentfault.com/q/1010000002960891


