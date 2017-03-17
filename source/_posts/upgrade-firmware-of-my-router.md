---
title: 路由器刷 OpenWrt 配置及破解 Dr.com
date: 2017-03-17 19:10:26
categories: TECH
tags: [OpenWrt, Dr.com]
toc: true
---

拖延症又犯了，一个星期之后才下定决心开写……时间太长有些没记下来的细节记不清了，领会精神即可。

<!--more-->

### 刷机
[上篇](/2017/03/11/use-image-generator-to-create-openwrt-firmware/)写到已经把生成的固件放到了本机，接下来就要把固件刷入路由器了。由于我的二手路由器已经被卖家装好了 Breed Web 控制台，所以过程比较无脑。

通电按复位键 4~5 秒进入 Breed Web 控制台，界面很好懂，参考[这里](https://sanwen8.cn/p/185SKcj.html)。

### ssh 登陆
因为固件里添加了LuCI 和 uhttpd，可以直接网页访问 LuCI，浏览器直接登陆 192.168.1.1，System - Administration 打开 ssh 登陆。登陆时候遇到了点问题，按照提示 rm 相应记录即可。

```bash
minway@Minway:~$ ssh root@192.168.1.1
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the RSA key sent by the remote host is
c6:25:a6:a2:90:4e:dc:27:4e:e0:99:b9:0c:56:70:0b.
Please contact your system administrator.
Add correct host key in /home/minway/.ssh/known_hosts to get rid of this message.
Offending RSA key in /home/minway/.ssh/known_hosts:6
  remove with: ssh-keygen -f "/home/minway/.ssh/known_hosts" -R 192.168.1.1
RSA host key for 192.168.1.1 has changed and you have requested strict checking.
Host key verification failed.
```
### 开启 Wi-Fi
参考[这篇文章](http://www.nginx.cn/3071.html)。

OpenWrt 默认是关闭 Wi-Fi的。修改 `/etc/config/wireless` 文件，注释掉 `option disabled 1`，即可开启。
```bash
config wifi-device  radio0
        option type     mac80211
        option channel  11
        option hwmode   11g
        option path     'platform/ar934x_wmac'
        option htmode   HT20
        # REMOVE THIS LINE TO ENABLE WIFI:
        # option disabled 1

config wifi-iface
        option device   radio0
        option network  lan
        option mode     ap
        option ssid     WMW      # Wi-Fi name
        option encryption 'psk2'
        option key '*******'     # password
```
### 修改 ssh 配置文件
`ssh root@192.168.1.1` 实在是有点长，其实可以通过修改 ~/.ssh/config 文件给路由器设置一个“别名”，还可以修改其他配置。
```bash
Host router
    HostName 192.168.1.1
    User root
```
### 破解 Dr.com
参考 [Wiki](https://github.com/drcoms/drcom-generic/wiki/d%E7%89%88%E7%AE%80%E7%95%A5%E4%BD%BF%E7%94%A8%E5%92%8C%E9%85%8D%E7%BD%AE%E8%AF%B4%E6%98%8E) 和 [这篇教程](http://www.newbandeng.com/thread-21832-1-1.html)。

### 问题

- #### WLAN 和 LAN 口顺序反了
刷机之前是 WLAN 口接墙上的端口，一个 LAN 口接网线，一直没动过，刷机之后就爆炸了，电脑连不上路由器，却能联外网。指定 IP 192.168.1.** 后连上路由器，外网又挂了。问了 tb 卖家才知道是 WLAN 和 LAN 口顺序反过来了。应该是路由器型号跟刷入固件的型号不符合？卖家刷的明月的 OpenWrt，可能做过改动所以正常。
- #### 签名检测
执行 `opkg update` 时提示 `Signature check failed`，按照[这篇文章](http://www.openwrtdl.com/wordpress/signature-check-failed%E9%97%AE%E9%A2%98%E8%A7%A3%E5%86%B3%E9%99%84opkg-conf) 解决了。
```bash
Failed to decode signature
Signature check failed.
Remove wrong Signature file.
-------------------
vim /etc/opkg.conf # 注释了 option check_signature 1
```


### 还参考了
http://www.right.com.cn/forum/thread-174525-1-1.html
http://weining.me/2016/11/04/add-ssh-key-to-openwrt-router
http://daemon369.github.io/ssh/2015/03/21/using-ssh-config-file
