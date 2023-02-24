---
title: Ubuntu下Mysql安装记录
description: 
date: 2022-11-02
top_image: https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2022/10/28_19_9_55_image-20221028190952227.png
cover: https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2022/10/28_19_9_55_image-20221028190952227.png
categories: 
- Ubuntu
tag: 
- Mysql
- Install
- Ubuntu



---



Ubuntu系统下MySQL的安装及所遇问题的解决办法

## 安装

直接命令行安装

```sh
sudo apt-get install mysql-server
```

根据提示完成安装。安装查询是否成功

```sh
mysql -V
```

> 大写的V

查看mysql服务状态

```sh
systemctl status mysql.service
```

## 登录

我安装的版本为 `8.0.31`

![image-20221028190952227](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2022/10/28_19_9_55_image-20221028190952227.png)

**问题：**

由于在安装过程中没有提示输入root的密码，导致进入mysql时无法进入。

**解决：**

1. 使用Ubuntu的root权限进入

   ```mysql
   sudo mysql -u root -p
   ```

2. 提示输入命令时，直接回车，然后输入以下命令行修改root密码

   ```mysql
   //切换到mysql表
   use mysql
   //修改密码
   alter user 'root'@'localhost' identified with mysql_native_password by "[密码]";
   ```

   > 惊喜：
   >
   > 如果密码为空，登录的时候直接回车就可以进入MySQL。

3. 可以通过下面密码查看自己加过密的密码

   ```mysql
   select user,authentication_string from user;
   ```

   ![image-20221028191619566](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2022/10/28_19_16_22_image-20221028191619566.png)

#### **用户创建**

由于MySQL8.0的创建用户的权限问题，创建用户分为两步：

- 创建用户名和密码
- 授予权限

**创建用户名和密码**

```mysql
create user 'username'@'host' identified by 'password'; 
```

其中`username`为自定义的用户名；`host`为登录域名，`host`为`'%'`时表示为 任意IP，为`localhost`时表示本机，或者填写指定的IP地址；`paasword`为密码。

**授予权限**

```mysql
grant all privileges on *.* to 'username'@'%' with grant option; 
```

其中 \*.\* 第一个*表示所有数据库，第二个\*表示所有数据表，如果不想授权全部那就把对应的\*写成相应数据库或者数据表；username为指定的用户；%为该用户登录的域名。

**刷新权限**

```mysql
flush privileges; 
```

**收回权限**

```mysql
#收回权限(不包含赋权权限)
REVOKE ALL PRIVILEGES ON *.* FROM user_name;
REVOKE ALL PRIVILEGES ON user_name.* FROM user_name;
#收回赋权权限
REVOKE GRANT OPTION ON *.* FROM user_name;

#操作完后重新刷新权限
flush privileges;
```

## 连接

使用idea连接虚拟机的步骤

### 1.修改虚拟机虚拟网络编辑器

![image-20221030185139574](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2022/10/30_18_51_42_image-20221030185139574.png)

虚拟机的ip地址为 **192.168.38.129**

点击右下角更改设置可以对nat设置进行更改

选择VMnet8，点击NAT设置

添加端口转发 主机端口为1200以上，虚拟机端口为22或80

![image-20221030185445761](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2022/10/30_18_54_48_image-20221030185445761.png)

修改后保存设置。

使用cmd或Terminal对ip地址进行ping命令，看是否正常通信

![image-20221030185634577](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2022/10/30_18_56_37_image-20221030185634577.png)

>ping 的时候别忘了打开虚拟机！！！

### 2.构建项目

修改maven源为国内源。

maven安装地址/conf/settings.xml，将其中的源修改为以下

```xml
<mirror>
        <id>alimaven</id>
        <name>aliyun maven</name>
        <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
     <mirrorOf>central</mirrorOf>
   </mirror>
```

> 如果使用的maven是idea自己创建的maven路径的话，直接修改就结束了
>
> 如果不是，需要修改idea里的路径

![image-20221030190440132](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2022/10/30_19_4_42_image-20221030190440132.png)

点击 All settings

需要修改一下图中的三个路径，点击后面的Override才能修改

![image-20221030190552579](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2022/11/12_10_21_26_image-20221030190552579.png)

>1. 为下载的maven路径
>2. 为maven里的setting.xml路径
>3. 是存放maven resposity的文件夹路径，存放下载的依赖包

**之后就是写代码的操作了**

>可能出现的**问题**
>
>连接mysql连不上的几个可能原因：
>
>- 检查输入的配置信息是否出错
>- 是否正确引入依赖
>- 查看虚拟机mysql是否激活（如果是刚开机，虽然查询mysql状态为 active ，仍需要使用用户名和密码进入mysql一次，然后就可以解决连不上的问题）

## 基本操作

**显示数据库**

```mysql
show databases;
```

**显示数据表**

```mysql
//切换数据库
use [hd];//数据库名
show tables;
```

**创建数据库**

```mysql
create database [库名];
```

**创建表**

```mysql
create table [表名] ([列名] [类型],[列名] [类型]);
```

**显示表结构**

```mysql
describe [表名]
```

**删除数据库**

```mysql
drop database [库名];
```

**删除表**

```mysql
drop table [表名];
```

**清除表中记录**

```mysql
delete from [表名];
```

**向表中添加记录**

```
//向所有列添加记录
insert into [表名] values([数据1]，[数据2]),([数据1]，[数据2]);
//指定列添加记录
insert into [表名]([列1],[列2]) values([数据1]，[数据2]),([数据1]，[数据2]);
```

## 技巧

**select显示技巧**

如下操作将会按照非结构化形式显示，去掉碍眼的---

```
select * from [table name] \G
```

**数据统计**

```sql
select count([列名]) from [表名];
```

**数据查询Limit**

场景1：

数据增量更新，只需要第1100条之后的数据显示出来，使用总体数据量减去已有数据量，得到所需数据量

```sql
//起始数从零开始
select * from [表名] limit [起始数],[所需长度];
select * from news limit 1110,1;
```

