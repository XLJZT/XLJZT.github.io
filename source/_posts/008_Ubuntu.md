---
title: Ubuntu命令学习及大数据平台安装问题
description: 
date: 2022-10-26
top_image: https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2022/10/28_16_34_28_%E5%9B%BE%E7%89%871.png
cover: https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2022/10/28_16_34_28_%E5%9B%BE%E7%89%871.png
categories: 
- Ubuntu
- Hadoop
tag: 
- Ubuntu
- Hadoop
- Scala




---

## Linux的命令

#### ubuntu技巧

**自动补全**

[TAB] 输入文件名时输入前面部分，后自动补全，再次按下会显示文件子目录

#### **刷新文件**

令其立即生效，而不必注销

> 可能有些情况下还是需要重新启动

```
source /etc/profile
```

#### **移动文件**

将文件1移动到文件2的位置

```
sudo mv [文件1] [文件2]
//example
sudo mv scala.tgz /app //将当前文件夹下的scala.tgz文件移动到/app文件夹下
```

#### **查看防火墙状态**

- inactive 关闭
- active 开启

```
sudo ufw status
```

#### **Tar打包命令**

在Linux平台，tar是主要的打包工具。tar命令通常用来把文件和目录压缩为一个文件（ tarball 或 tar, gzip 和 bzip）。

Tar选项：

- c – 创建压缩文件
- x – 解压文件
- v – 显示进度.
- f – 文件名.
- t – 查看压缩文件内容.
- j – 通过bzip2归档
- z –通过gzip归档
- r – 在压缩文件中追加文件或目录
- W – 验证压缩文件

#### 设置主机名hostname

**查看主机名**

```
hostname
```

如果修改当前机器名称，需修改/etc/hostname，并需要修改权限

```
//在/etc文件夹下
chmod 777 hostname
//修改hostname文件
vim hostname
//修改之后重启生效
```

#### 移动文件

移动

```
sudo mv [文件] [路径]
```

移动并命名

```
sudo mv [文件] [路径]/命名
```

#### 复制文件

复制

```
cp [文件] [路径]
```

复制并命名

```
cp [文件] [路径]/命名
```

>当前路径下有一个workers文件，复制到当前路径下并命名
>
>`cp workers work`

#### 修改用户及用户组

> hd为用户名及用户组名
>
> 用户名和用户组名是相同的，因为是系统的第一个用户

```
sudo chown -R hd:hd [文件]/
```



## Vim命令

**替换**

:s 命令用来查找和替换字符串，语法为：

```
:{作用范围}s/{目标}/{替换}/{替换标志}
```

例如`:%s/foo/bar/g`会在全局范围(`%`)查找`foo`并替换为`bar`，所有出现都会被替换（`g`）。

> 替换选取里的文字，选中后会自动补全，输入命令后替换选取里查找到的文字

当前行`.`与接下来两行`+2`：

```bash
:.,+2s/foo/bar/g
```

空替换标志表示只替换从光标位置开始，目标的第一次出现：

```ruby
:%s/foo/bar
```



## ubuntu 20.04 国内源



直接在 software&updates 选择国内源进行apt-get update



## 共享文件夹

**创建**

1. 虚拟机 > 设置
2. 选项（硬件选项卡旁）
3. 共享文件夹设置为总是启用
4. 按照向导设置本地文件夹地址

**使用**

虚拟机下的位置：`/mnt/hgfs/[设置的文件夹名称]/`

**问题**

如果在解压文件的时候莫名报错，把文件从文件里移到ubuntu里解压就好了。

## 文件创建

在当前工作目录底细新建一个文件

```
touch [文件名.后缀]
```

**vim**命令

```
vim [文件名.后缀]
```

> 目前遇到的问题：
>
> 如果共享文件夹遇到不能用或找不到的问题，在选项卡里 禁用 确定之后，再次开启就可以使用了

## ssh三台机器互联

> 用户名为 hd
>
> 三台机器名为:hadoop1(2/3)
>
> 每台机器的hosts文件都已经被修改，记录了机器的ip

**每台机器都生成一个密钥**

```
ssh-keygen -t rsa
```

> 输入命令之后一路回车
>
> 存储位置会显示在第一个回车处

**合成三个公钥**

> 第一台机器：hadoop1

```
 ssh hadoop1 cat /home/hd/.ssh/id_rsa.pub>authorized_keys
```

![image-20221026172000350](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2022/10/26_17_20_6_image-20221026172000350.png)

**复制authorized_keys到其他节点**

```
scp authorized_keys hd@hadoop2:/home/pf/.ssh
scp authorized_keys hd@hadoop3:/home/pf/.ssh
```

**无密码登录**

在每台机器上使用命令 进行登录连接

```
ssh hadoop2 date
ssh hadoop3 date
ssh hadoop1 date
```

> 每台机器都使用一次这三台命令，会有提示建立链接

![image-20221026185848317](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2022/10/26_18_58_52_image-20221026185848317.png)

每条命令成功后都会返回被链接机器的时间

**重点**

之后需要将其他机器的公钥全部添加到authorized_keys中

```
cat id_rsa.pub >> authorized_keys
```

> 第一台机器生成`authorized_keys`之后，将其发给第二台机器，第二台添加上公钥之后，发给第三台，直到最后一台添加上公钥之后，将所有的已经添加好的公钥文件发给其他的机器上

```
scp authorized_keys hd@hadoop3:/home/pf/.ssh
```

最后达到免输密码的效果。

**scp问题**

scp 出现 Permission denied, please try again 的解决办法

这是 ssh 的权限问题，修改权限即可，进入到目标主机，如：hadoop2 或 hadoop3 的/etc/ssh 文件夹下，用 root 权限修改文件 sshd_config，将PermitRootLogin prohibit-password 改为 PermitRootLogin yes，然后重启sshd 服务。

> 进入/etc/ssh文件夹下

```
sudo vim sshd_config
```

修改为`PermitRootLogin yes`,如果没有则添加上这一行

重启服务

```
sudo service ssh restart
```



## Java的安装

> Java对大小写的要求比较严格，大小写错误将会导致不可用

**配置环境变量**

root权限执行命令 `vim /etc/profile` ,加上以下命令

```
#set java environment
export JAVA_HOME=/app/jdk1.8.0_221 //为解压好的文件地址
export JRE_HOME=/app/jdk1.8.0_221/jre
export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$PATH:/sbin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
```

**命令行使配置生效**

```
//root下
source /etc/profile
```

**命令行查看是否生效**

```
Java -version
//显示版本则说明正常生效
which java
//显示java安装路径
```

> **注意：** **/etc/profile** **是所有用户的环境变量，** **/etc/enviroment** 是系统的环境变量。 登陆系统时**shell** **读取的顺序应该是 ：`/etc/profile ->/etc/enviroment -->$HOME/.profile -->$HOME/.env`

## Scala的安装

**环境变量的配置**

```
export SCALA_HOME=/app/scala-2.12.14
export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$SCALA_HOME/bin:$PATH:/bin:$PATH
```

## Hadoop安装

**问题**

解压文件时报错误为：tar: Exiting with failure status due to previous errors

**解决**

检查解压的目录是否为共享文件夹，转移文件到ubuntu的其他文件夹下，用sudo权限进行解压即可

## Hadoop开启

**启动HDFS**

>hadoop目录：
>
>/app/hadoop-3.3.4目录下

```
sbin/start-dfs.sh
```

> 输入jps查看是否启动相应服务
>
> 输出：
>
> - SecondaryNameNode
> - Jps
> - DataNode
> - NameNode
>
> 子节点输出为：
>
> - Jps
> - DataNode

**启动yarn**

```
sbin/start-yarn.sh
```

> 输入jps查看是否启动相应服务
>
> 输出：
>
> - ResourceManager
> - 以及之前启动的服务
>
> 子节点输出为：
>
> - NodeManager
> - 以及之前启动的服务

## Hadoop关闭

> hadoop的目录下：
>
> /app/hadoop-3.3.4目录下

第一种：

```
sbin/stop-all.sh
```

第二种：

```
sbin/stop-yarn.sh
sbin/stop-dfs.sh
```

> 输入jps进行检查：
>
> 输出：
>
> - Jps
>
> 关闭成功
