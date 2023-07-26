---
title: Typora 设置
description: 
date: 2023-7-26
top_image: https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/07/26_20_57_30_%E6%88%AA%E5%B1%8F2023-07-26%2020.57.24.png
cover: https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/07/26_20_57_30_%E6%88%AA%E5%B1%8F2023-07-26%2020.57.24.png
categories: 
- Hexo
tag: 
- Typora

---

# Typora 设置

每次换电脑都需要重新下载一个 Typora，并且根据不同的情况进行重新配置，尤其是这次更换到了 MAC之后，接下来记录的是我安装的步骤

（省略了失败的过程）

[Typroa 下载地址](https://typora.io/)

![截屏2023-07-26 20.57.24](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/07/26_20_57_30_%E6%88%AA%E5%B1%8F2023-07-26%2020.57.24.png)

# Node && Picgo Core

首先我们需要安装好 Node 和 Picgo Core，在 Mac 和 win 的环境下我都更加喜欢 Core ，上穿图片时比较无感知，用起来更舒服。

在 Mac 平台上推荐大家使用 homebrew 进行安装

[Homebrew国内如何自动安装（国内地址）（Mac & Linux）](https://zhuanlan.zhihu.com/p/111014448)

按照步骤很快便可以安装上 homebrew。

通过 homebrew 安装 Node 和 Picgo Core

```shell
brew install node
npm install picgo -g
```

> 记得使用命令行查看一下自己是否安装完成

# Picgo Core 配置

使用下面命令行进行模板配置

```
picgo set uploader
```

```
$ picgo set uploader
? Choose a(n) uploader (Use arrow keys)
  smms
❯ tcyun
  github
  qiniu
  imgur
  aliyun
  upyun
(Move up and down to reveal more choices)
```

因为我是使用 gitlab 所以随便选了一个。

> picgo 的默认配置文件为`~/.picgo/config.json`。其中`~`为用户目录。不同系统的用户目录不太一样。
>
> linux 和 macOS 均为`~/.picgo/config.json`。
>
> windows 则为`C:\Users\你的用户名\.picgo\config.json`。

# 插件安装

命令行

```
picgo install picgo-plugin-gitlab-files
```

瞬间就能下载好

在配置文件中配置

```
{
  "picBed": {
    "current": "gitlab-files-uploader",
    "github": {
      "repo": "XLJZT/img",
      "token": "xxxxxxxxxxxxxxxxxxxx",
      "path": "blog/",
      "customUrl": "",
      "branch": "main"
    },
    "gitlab-files-uploader": {
      "gitUrl": "https://gitlab.com",
      "projectId": "xxxxxxxxxxx",
      "branch": "main",
      "gitToken": "xxxxxxxxxx",
      "gitVersionUnderThirteen": false,
      "fileName": "/blog/pictures/{year}/{month}/{day}_{hour}_{minute}_{second}_{fileName}",
      "commitMessage": "Upload {fileName} By PicGo gitlab files uploader at {year}-{month}-{day}",
      "deleteRemote": false,
      "deleteMessage": "Delete {fileName} By PicGo gitlab files uploader at {year}-{month}-{day}",
      "deleteInform": false,
      "authorMail": "",
      "authorName": ""
    },
    "uploader": "gitlab-files-uploader",
    "transformer": "path"
  },
  "picgoPlugins": {
    "picgo-plugin-gitlab-files": true
  }
}
```

# 最后一坑

在 Typora 中需要选择图像上传服务，需要选择自定里命令这一选项

在终端中获取 node 和 picgo 的位置

```shell
where node
where picgo
```

然后将两个路径，中间空一格，加上 upload 填入命令行内，比如我的是：

```
/opt/homebrew/opt/node@18/bin/node /opt/homebrew/bin/picgo upload
```

验证图片上传就可以上传图片了

至此，安装完毕！
