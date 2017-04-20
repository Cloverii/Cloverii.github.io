---
title: 从源码编译安装 Linux 内核之一（虚拟机）
date: 2017-04-19 19:19:25
categories: Tech
tags: [Linux, kernel]
---

## 版本

Ubuntu 14.04, 32位（VirtualBox 下）
GCC 4.4
kernel 2.6.26

<!--more-->

## 准备工作
1. 构建编译环境
   安装编译器套件
```bash
apt-get install build-essential
```
2. 从[官网](www.kernel.org)下载并解压源代码
   我下载的是 2.6.26 版本。
```bash
wget https://www.kernel.org/pub/linux/kernel/v2.6/linux-2.6.26.tar.gz
cp linux-2.6.26.tar.gz /usr/src/
cd /usr/src
tar zxvf linux-2.6.26.tar.gz 
```

3. 安装 ncurses 库
   我选择从源码安装，版本是5.9。
```bash
cd
mkdir programs
cd programs
wget http://ftp.gnu.org/pub/gnu/ncurses/ncurses-5.9.tar.gz
tar zxvf ncurses-5.9.tar.gz
cd ncurses-5.9/
./configure #配置
make
make install
```
## 配置、编译并安装
我 copy 了当前内核的 .config 文件来配置 2.6.26 的内核。如果不是第一次配置这个内核，记得先 make mrproper 一下清除配置。

```bash
make mrproper
cd /usr/src/linux-2.6.26/
make menuconfig
```
接下来慢慢编译了。反正我第二天早上起来就好了[滑稽]。
```bash
make
make modules install
make install
```
## 高潮来了……
这一部分我始终非常迷惑，看到有的地方说 make install 完成后会自动修改 grub，重启默认以新内核启动，但我这里 grub 更新过了，但重启默认还是旧内核，不清楚是虚拟机的原因还是怎么回事。

我的虚拟机是 VirtualBox，重启之后看到 Oracle 的图标立刻长按 shift 键，进入 grub 再松手。选择 2.6.26，然后……（请忽略屏幕里的我好吗）
![](http://ooie9cjod.bkt.clouddn.com/17-4-19/12492029-file_1492614042952_ac49.jpg)
我先是搜到了[这个帖子](https://ubuntuforums.org/showthread.php?t=1018403&s=ba8403c91a818c327ad7c520c672200f)，把1 - 4 页的方法试了个遍。然后看到了[这个](https://forums.linuxmint.com/viewtopic.php?t=47594)，也是受该帖的启发。不过对我都没有用。

列举一下方法：

1. 等待几分钟，在 initramfs 里输入 exit 或 return，就可以正常 boot 了。
2. 找到系统 / 目录挂载点，例如 /dev/sda5。打开 boot 目录，（例如 /boot/grub/grub.cfg），将 `UUID=***` 改成 `/dev/sda****` 。
   -`root=UUID=9c05139c-b5bb-4683-a860a7bdf456ccda ro quiet splash`
   +`root=/dev/sda5 ro quiet splash`
3. 将  /etc/default/grub 文件中的 `GRUB_DISABLE_LINUX_UUID=true` 注释取消。
```
# Uncomment if you don’t want GRUB to pass “root=UUID=xxx” parameter to Linux
# GRUB_DISABLE_LINUX_UUID=true
```

试了这么多方法之后，我终于能冷静下来观察错误信息了。
```
Gave up waiting for root device. Common problems:

-Boot args (cat /proc/cmdline)
-Check rootdelay= (did the system wait long enough?)
-Check root= (did the system wait for the right device?)
-Missing modules (cat /proc/modules; ls /dev)
```
这些信息提醒我们可以从四个方面来排查。而上面的方法1显然是排查问题2 `rootdelay` 的，而2和3似乎都针对问题3 ` root=`。就在这时，我又找到了一篇相见恨晚的好文。[这位朋友](http://www.dedoimedo.com/computers/ubuntu-initrd-bug.html) 总结得非常好。我已经排除了2和3问题了那剩下的只有1和4了。这位朋友又说 Boot args 没有修改过的话一般不会出问题，那就是模块缺失了。除了重新编译内核我也想不出来其他办法了。

人生真是艰难啊……

第二三次尝试请看下一篇。