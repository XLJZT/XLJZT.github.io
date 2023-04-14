---
title: wsl设置代理
description: 
date: 2023-4-12
top_image: https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/04/12_15_18_54_image-20230412151852465.png
cover: https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/04/12_15_18_54_image-20230412151852465.png
categories: 
- WSL
tag: 
- Clash

---



#  原因

当我在使用 wsl 进行跑 python 代码的时候，有许多的源码和数据是需要从 github 或者一些其他的网站去获取，所以需要使用代理去访问。

windows 电脑是使用 clash 开的代理，所以使用 wsl 去直接访问 windows 的代理是最简单直接且容易的方法。

#  配置

> WSL2 基于 Hyper-V 运行，导致 Linux 子系统和 Windows 在网络上是两台各自独立的机器，从 Linux 子系统访问 Windows 首先需要找到 Windows 的 IP。

**获取指向 windows 的 ip**

由于 Linux 子系统也是通过 Windows 访问网络，所以 Linux 子系统中的网关指向的是 Windows，DNS 服务器指向的也是 Windows，基于这两个特性，我们可以将 Windows 的 IP 读取出来。

```shell
vim /etc/resolv.conf
```

打卡文件之后，会有一个指向的 ip

![image-20230412151544992](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/04/12_15_15_49_image-20230412151544992.png)

> 每个人的ip会有不同

我的 ip 就是 172.19.64.1

**配置代理**

通过环境变量 `ALL_PROXY` 配置代理

```shell
export ALL_PROXY="http://172.19.64.1:7890"
```

**允许局域网请求**

在 windows 下的clash客户端开启 lan

![image-20230412151852465](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/04/12_15_18_54_image-20230412151852465.png)

恭喜你，wsl 可以使用代理了

#  参考文章

[为 WSL2 一键设置代理](https://zhuanlan.zhihu.com/p/153124468)