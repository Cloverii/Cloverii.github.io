---
title: 从源码编译安装 Linux 内核之二（服务器）
date: 2017-04-19 19:52:46
categories: Tech
tags: [Linux, kernel]
---
## 从头再来
上篇说到我第一次编译内核虽然 make 中途没报错但最后还是光荣失败。考虑到本机太渣渣，决定丢到服务器上去搞。准备工作基本同[上篇]()。唯一的区别是这次使用的 menuconfig 的默认配置，一路狂报错，不晓得是不是这个原因……

**环境**：
Ubuntu 14.04,  64位
GCC 4.6.3
kernel 2.6.26

<!--more-->

## 错误们

每次 error 我都以为这是最后一个了，然而现实太爱打脸……

1.  `elf_x86_64: No such file or directory`
```
gcc: error: elf_x86_64: No such file or directory
make[1]: *** [arch/x86/vdso/vdso.so.dbg] Error 1
make: *** [arch/x86/vdso] Error 2
```
**解决**：（参考了实验指导）
将 `arch/x86/vdso/Makefile` 文件中的 '-m elf_x86' 改成 '-m64'，'-m_elf_i386' 改成 '-m32'

2. `duplicate member 'padding'`
```
include/linux/kvm.h:216:9: error: duplicate member 'padding'
arch/x86/kvm/svm.c: In function 'io_interception':
arch/x86/kvm/svm.c:1059:30: warning: variable 'rep' set but not used [-Wunused-but-set-variable]
arch/x86/kvm/svm.c:1059:12: warning: variable 'down' set but not used [-Wunused-but-set-variable]
make[1]: *** [arch/x86/kvm/svm.o] Error 1
make: *** [arch/x86/kvm] Error 2
```

**解决**：（[参考](http://www.cnblogs.com/openusb/archive/2013/03/08/2949346.html)）

```
make mrproper
make menuconfig # 将 Virtualization 选项去掉
```
![](http://ooie9cjod.bkt.clouddn.com/17-4-19/24647608-file_1492604513822_8283.png)

3. `Error: .size expression for copy_user_generic_c does not evaluate to a constant`
```
/tmp/cc2R0hJd.s: Assembler messages:
/tmp/cc2R0hJd.s: Error: .size expression for copy_user_generic_c does not evaluate to a constant
make[1]: *** [arch/x86/lib/copy_user_64.o] Error 1
make: *** [arch/x86/lib] Error 2
```
**解决**：（[参考](http://stackoverflow.com/questions/23194840/linux-2-6-24-kernel-compilation-error-size-expression-for-copy-user-generic-c-d)）
将 `arch/x86/lib/copy_user_64.S` 中的 `END(copy_user_generic_c)` 改为 `END(copy_user_generic_string)`。

4. 几分钟后又一个 error…
   ``undefined reference to `__mutex_lock_slowpath'``
   ``undefined reference to `__mutex_unlock_slowpath'``
```
kernel/built-in.o: In function `mutex_lock':
/usr/src/linux-2.6.26/kernel/mutex.c:92: undefined reference to `__mutex_lock_slowpath'
kernel/built-in.o: In function `mutex_unlock':
/usr/src/linux-2.6.26/kernel/mutex.c:117: undefined reference to `__mutex_unlock_slowpath'
make: *** [.tmp_vmlinux1] Error 1
```
**解决**：（[参考](http://stackoverflow.com/questions/17691736/how-can-i-rectify-error-undefined-reference-to-mutex-lock-slowpath-during-ke)）
这个问题有两个不一样的回答，我的 mutex.c 文件和  Andre 的一致，所以采用了他的方法。

## 一次手残，后悔两天

安装完之后又是见证奇迹的时刻了，然而并没有奇迹发生，于是开始到处找怎么修改 grub。正常的流程如下所示。然而在我还没看到 [DO 的这份良心 tutorial](https://www.digitalocean.com/community/tutorials/how-to-update-a-digitalocean-server-s-kernel) 的时候，一个脑残直接把`/etc/default/grub` 改成了 `GRUB_DEFAULT=2`。- - 然而 reboot 之后服务器直接挂掉了，想起来在 `/boot/grub/grub.cfg` 里看到过  submenu 的字样，猜测是 submenu 造成的后果？于是又傻傻地花了一晚上重新编译+安装了一遍。这次步骤非常合理也关掉了 submenu，然而还是：

![Kernel panic](http://ooie9cjod.bkt.clouddn.com/17-4-19/42962749-file_1492614037636_f29a.png)

我选择死亡……

```
vim /etc/default/grub
# 修改以下参数，没有的话自行添加，但要保证每一个参数只出现一次
#GRUB_DEFAULT=saved
#GRUB_SAVEDEFAULT=true
#GRUB_DISABLE_SUBMENU=y

export GRUB_CONFIG=`sudo find /boot -name "grub.cfg"`
sudo update-grub
grep 'menuentry ' $GRUB_CONFIG | cut -f 2 -d "'" | nl -v 0
# 上面的命令会显示 boot 选项，将下面命令中的数字换成需要的 boot 项的数字
grub-reboot 2(or another number) # 仅修改下次启动
# 或 grub-set-default 2 # 更改默认启动项
reboot
```

欲知后事如何，且听下回分解。