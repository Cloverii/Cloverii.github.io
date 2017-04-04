---
title: 博客主题 maupassant 挂掉了
date: 2017-03-26 21:55:50
tags:
---
03-26 更新：

git reset 居然好了……原来只需要这么简单么？diff 了一下也就是 `package.json`里少了一行 `"hexo-render-sass": "0.2.0"`，迷惑。
***

博客的主题被我玩坏了，虽然我只是想改下代码高亮部分...........坏掉之后把 css 改回去都没有用了，费解。

疑似 `hexo-renderer-sass` 模块的问题，然而重装也不行，暂时换回默认主题 landscape，有空直接重装系统好了。
<!--more-->

```bash
minway@Inspiron-5535:~/Workspace/Blog$ cnpm install hexo-renderer-sass
⠧ [0/1] Installing jsonify@~0.0.0platform unsupported hexo-renderer-sass@0.3.1 › hexo@3.2.2 › hexo-fs@0.1.6 › chokidar@1.6.1 › fsevents@^1.0.0 Package require os(darwin) not compatible with your platform(linux)
[fsevents@^1.0.0] optional install error: Package require os(darwin) not compatible with your platform(linux)
✔ Installed 1 packages
✔ Linked 212 latest versions

> hexo-util@0.6.0 build:highlight /home/minway/Workspace/Blog/node_modules/.0.6.0@hexo-util
> node scripts/build_highlight_alias.js > highlight_alias.json

Downloading binary from https://npm.taobao.org/mirrors/node-sass/v4.5.1/linux-x64-48_binding.node
Download complete
Binary saved to /home/minway/Workspace/Blog/node_modules/.4.5.1@node-sass/vendor/linux-x64-48/binding.node
Caching binary to /home/minway/.npminstall_tarball/node-sass/4.5.1/linux-x64-48_binding.node
Binary found at /home/minway/Workspace/Blog/node_modules/.4.5.1@node-sass/vendor/linux-x64-48/binding.node
Testing binary
Binary is fine
✔ Run 3 scripts
deprecate hexo-renderer-sass@0.3.1 › hexo@3.2.2 › swig@1.4.2 This package is no longer maintained
✔ All packages installed (172 packages installed from npm registry, used 27s, speed 349.68kB/s, json 248(1.78MB), tarball 7.43MB)
Recently updated (since 2017-03-19): 6 packages (detail see file /home/minway/Workspace/Blog/node_modules/.recently_updates.txt)
```
