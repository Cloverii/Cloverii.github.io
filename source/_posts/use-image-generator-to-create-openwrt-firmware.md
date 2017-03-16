---
title: 使用 Image Generator 工具生成 OpenWrt 固件
date: 2017-03-11 14:11:08
categories: TECH
tags: [OpenWrt]
toc: true
---
某宝买了个二手硬改路由器，TL-WR842N，刷好明月永在的 OpenWrt 固件和 Breed Web 控制台。本来作为没玩过路由器的弱弱我是懒得刷机希望到手就能用的，结果 `opkg update` 报错，有些包已经找不到了，不得不重刷原版 OW。又一次偷懒失败了QAQ。由于是硬改机器，16M 闪存，只能自己编译。

### 环境
Ubuntu 14.04

我是在自己的 VPS 上完成的整个过程，因为 openwrt.org 正常访问奇慢无比。

### 下载 Image Generator
Image Generator ["is a pre-compiled OpenWrt build environment suitable for creating custom images without the need for compiling. "](https://wiki.openwrt.org/doc/howto/obtain.firmware.generate) 从[恩山某贴](http://www.right.com.cn/forum/thread-172507-1-1.html)看到的，总之方便快捷，值得拥有。

下载按照文档来就好，链接要根据自己路由器的架构修改。

```bash
$ cd ~
$ mkdir openwrt && cd openwrt
$ wget https://downloads.openwrt.org/chaos_calmer/15.05.1/ar71xx/generic/OpenWrt-ImageBuilder-15.05.1-ar71xx-generic.Linux-x86_64.tar.bz2
$ tar -xvjf OpenWrt-ImageBuilder-15.05.1-ar71xx-generic.Linux-x86_64.tar.bz2
$ cd OpenWrt-ImageBuilder-15.05.1-ar71xx-generic.Linux-x86_64
```

### 配置软件包仓库
`$ vim repositories.conf`

存放软件包的地址，可以根据需要自行修改换源。

### 修改为 16M
我的路由器型号是 TL-WR842N，卖家说刷 841 v8就行，看了下 SoC 是一样的。

打开 target/linux/ar71xx/image/Makefile 文件，查找 841。

这里跟网上教程有分歧。找不到教程里说的文件最后一处:
 `$(eval $(call SingleProfile,TPLINK-LZMA,$(fs_64kraw),TLWR841NV8,tl-wr841n-v8,TL-WR841N-v8,ttyS0,115200,0x08410008,1,8Mlzma))`

经我验证，将下面的 `4mlzma` 改成 `16mlzma` 即可。

```bash
define Device/tl-wr841n-v8
    $(Device/tplink-4mlzma)
    BOARDNAME := TL-WR841N-v8
    DEVICE_PROFILE := TLWR841
    TPLINK_HWID := 0x08410008
endef
```

### 准备生成
语法：
`$ make image PROFILE=XXX PACKAGES="pkg1 pkg2 pkg3 -pkg4 -pkg5 -pkg6" FILES=files/`

- PROFILE 为硬件型号。支持的型号可以运行 `make info` 查询。我的是 TLWR841。

- PACKAGES 可以自定义需要 include 的软件包。-pkg 表示去除该包。

- FILES 是用自定义的文件替换默认配置。听说可以替换 wireless 文件让 wifi 默认开启。

我把[两篇](http://www.right.com.cn/forum/thread-172507-1-1.html)[教程](http://demon.tw/hardware/image-generator-image-builder-openwrt.html)里的 PACKAGES 混搭了一下：

`$ makeimage PROFILE=TLWR841 PACKAGES="-dnsmasq dnsmasq-full python ipset iptables-mod-nat-extra luci-app-sqm libpolarssl luci uhttpd lua" `

（其中 `-dnsmasq dnsmasq-full ipset iptables-mod-nat-extra libpolarssl shadowsocks-libev-polarssl` 是为了科学上网，`python` 是运行 drcom 客户端，`luci uhttpd lua luci-i18n-chinese` 是LuCI 界面，`luci-app-sqm` 是限速的。但出现了一些问题：
```bash
Collected errors:
 * opkg_install_cmd: Cannot install package luci-i18n-chinese.
 * opkg_install_cmd: Cannot install package shadowsocks-libev-polarssl.
make[2]: *** [package_install] Error 255
make[2]: Leaving directory `/root/openwrt/OpenWrt-ImageBuilder-15.05.1-ar71xx-generic.Linux-x86_64'
make[1]: *** [_call_image] Error 2
make[1]: Leaving directory `/root/openwrt/OpenWrt-ImageBuilder-15.05.1-ar71xx-generic.Linux-x86_64'
make: *** [image] Error 2
```
所以舍弃了 `luci-i18n-chinese` 和 `shadowsocks-libev-polarssl`，LuCI的中文支持不是大事，ss也可以稍后再装。）

### 完毕
如果没有问题的话，在 `bin/ar71xx/` 目录下就可以看到:
 `openwrt-ar71xx-generic-tl-wr841nd-v8-squashfs-sysupdate.bin` 
 和 `openwrt-ar71xx-generic-tl-wr841nd-v8-squashfs-factory.bin` 文件了。
 第一次安装 OW 选 `factory.bin`，已有 OW 选 `sysupdate.bin`。

我还要把文件 scp 回本机：

```bash
scp root@138.197.222.50:/root/openwrt/OpenWrt-ImageBuilder-15.05.1-ar71xx-generic.Linux-x86_64/bin/ar71xx/openwrt-15.05.1-ar71xx-generic-tl-wr841n-v8-squashfs-sysupgrade.bin ./home/minway 
```
(root 权限执行会报错)

接下来就是刷入啦。
