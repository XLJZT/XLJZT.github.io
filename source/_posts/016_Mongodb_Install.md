---
title: Mongodb 的安装
description: Mongodb 的安装
date: 2023-3-12
top_image: https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/03/12_10_20_41_image-20221125151939853.png
cover: https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/03/12_10_20_41_image-20221125151939853.png
categories: 
- Ubuntu
tag: 
- Mongodb
- Ubuntu


---

# Mongodb 的安装

系统为`ubuntu22.04`

# 安装

官网上的教程为https://mongodb.net.cn/manual/tutorial/install-mongodb-on-ubuntu/，但是这个教程好像很老的样子，给的安装代码的一些数值也需要修改才能够正常的安装在自己的Ubuntu系统中。

在安装的过程中遇到报错，最终找到这个网站解决问题，直接使用一下代码即可安装`mongodb`

```shell
wget -qO - https://www.mongodb.org/static/pgp/server-6.0.asc |  gpg --dearmor | sudo tee /usr/share/keyrings/mongodb.gpg > /dev/null
echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list
sudo apt update
sudo apt install mongodb-org
```

> 报错内容
>
> The following packages have unmet dependencies: mongodb-org-mongos : Depends: libssl1.1 (>= 1.1.1) but it is not installable mongodb-org-server : Depends: libssl1.1 (>= 1.1.1) but it is not installable mongodb-org-shell : Depends: libssl1.1 (>= 1.1.1) but it is not installable

# 启动

您可以`mongodb`通过发出以下命令来启动该过程：

```
sudo systemctl start mongod
```

如果在启动时收到类似于以下内容的错误 `mongodb`：

```
Failed to start mongod.service: Unit mongod.service not found.
```

首先运行以下命令：

```
sudo systemctl daemon-reload
```

然后再次运行上面的启动命令。

#### 验证MongoDB已成功启动。

```
sudo systemctl status mongod
```

您可以有选择地通过发出以下命令来确保MongoDB将在系统重启后启动：

```
sudo systemctl enable mongod
```

![image-20221125151939853](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/03/12_10_20_41_image-20221125151939853.png)

# 使用

命令行输入`mongo`是无效的，会告诉说没有安装这个包

```shell
mongosh //进入shell界面
```

# 配置文件的使用

scala语言的版本为`2.12`

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>Recommend</artifactId>
        <groupId>org.example</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>MysqlConnect</artifactId>
    <packaging>jar</packaging>

    <name>MysqlConnect</name>
    <url>http://maven.apache.org</url>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <!-- Spark的依赖引入 -->
        <dependency>
            <groupId>org.apache.spark</groupId>
            <artifactId>spark-core_2.12</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.spark</groupId>
            <artifactId>spark-sql_2.12</artifactId>
        </dependency>
        <!-- 引入Scala -->
        <dependency>
            <groupId>org.scala-lang</groupId>
            <artifactId>scala-library</artifactId>
        </dependency>
        <!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.28</version>
        </dependency>
		
        <!--用来驱动新版mongoDB-->
        <!-- https://mvnrepository.com/artifact/org.mongodb/mongo-java-driver -->
        <dependency>
            <groupId>org.mongodb</groupId>
            <artifactId>mongo-java-driver</artifactId>
            <version>3.12.11</version>
        </dependency>

        <!-- 加入MongoDB的驱动 -->
        <dependency>
            <groupId>org.mongodb</groupId>
            <artifactId>casbah-core_2.12</artifactId>
            <version>3.1.1</version>
        </dependency>
        <dependency>
            <groupId>org.mongodb.spark</groupId>
            <artifactId>mongo-spark-connector_2.12</artifactId>
            <version>3.0.2</version>
        </dependency>

    </dependencies>
</project>

```

