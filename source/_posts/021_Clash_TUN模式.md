---
title: Clash的TUN模式
description: 
date: 2023-4-14
top_image: https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/04/14_15_12_5_image-20230414151203202.png
cover: https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/04/14_15_12_5_image-20230414151203202.png
categories: 
- Clash
tag: 
- Clash

---



#  原因

前两天已经发布了一个 wsl 设置代理的一个文章，当天我在使用 wsl 访问网站或者下载东西的时候并没有遇到什么问题。但是第二天当我再次使用 git 命令的时候又出现了问题，出现下载缓慢或者直接就连接不上的情况。

所以选择直接使用 TUN 模式，进行全局代理。（之前一直嫌麻烦，没有搞）

#  TUN Intro

tun 是操作系统上的一种虚拟网络设备，可以让用户处理网络中的三层数据包(例如IP包)。

与此差不多的还有 tap，但 tap 处理的是二层数据包。

只处理三层数据包这就决定了`tun`性能往往比`tap`要好一些。

在`Windows`平台下，系统内核并不默认提供 tun 功能，需要安装 [Wintun](https://www.wintun.net/) 这个第三方驱动才能支持

#  Clash 设置

首先在 Service Mode 安装推荐的方法（点击后面的 Manage）

![image-20230414145304854](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/04/14_14_53_7_image-20230414145304854.png)

然后开启 Tun mode 和 Mixin 的开关

开启 Tun mode 就不需要开启 System Proxy。

#  Tun 配置

## 第一个方法（最简单）

点击 Tun mode 后面的齿轮，会有提供的模板

![image-20230414151203202](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/04/14_15_12_5_image-20230414151203202.png)

可以在里面进行可视化配置

## 第二个方法

>  这个方法不需要修改Tun mode 的默认值

点击 Mixin 后面的齿轮，然后将配置复制（覆盖）进去

```json
mixin:
   dns:
     enable: true
     default-nameserver:
       - 223.5.5.5   # 阿里的DNS服务器
       - 1.0.0.1     # CloudFlare的DNS服务器
     ipv6: false
     enhanced-mode: redir-host #fake-ip
     nameserver:
       - https://dns.rubyfish.cn/dns-query
       - https://223.5.5.5/dns-query
       - https://dns.pub/dns-query
     fallback:
       - https://1.0.0.1/dns-query
       - https://8888.google/dns-query
       - https://public.dns.iij.jp/dns-query
       - https://dns.twnic.tw/dns-query
     fallback-filter:
       geoip: true
       ipcidr:
         - 240.0.0.0/4
         - 0.0.0.0/32
         - 127.0.0.1/32
       domain:
         - +.google.com
         - +.facebook.com
         - +.twitter.com
         - +.youtube.com
         - +.xn--ngstr-lra8j.com
         - +.google.cn
         - +.googleapis.cn
         - +.gvt1.com
   tun:
     enable: true
     stack: gvisor
     dns-hijack:
       - 198.18.0.2:53
     macOS-auto-route: true
     macOS-auto-detect-interface: true # 自动检测出口网卡
```

# 参考文章

[正确配置Clash For Windows的TUN、TAP、系统代理三种模式？](https://clashdingyue.tk/2022/06/%E6%AD%A3%E7%A1%AE%E9%85%8D%E7%BD%AEClash-For-Windows/)

[Clash-tun模式配置指南](https://rainchan.win/2022/05/15/Clash-tun%E6%A8%A1%E5%BC%8F%E9%85%8D%E7%BD%AE%E6%8C%87%E5%8D%97/)
