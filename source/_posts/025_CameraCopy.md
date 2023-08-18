---
title: 数字孪生Camera基础功能
description: 
date: 2022-8-17
top_image: https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/08/18_10_54_4_image-20230818105105275.png
cover: https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/08/18_10_54_4_image-20230818105105275.png
categories: 
- UE
tag: 
- 游戏
- C++
- UE
---

# 数字孪生Camera基础功能

 整体功能仅包含了：

1. 左键拖动旋转
2. 右键拖动移动
3. 滑轮缩放

整个项目来源于：[ArchVizExplorer](https://www.unrealengine.com/marketplace/zh-CN/product/archviz-explorer?sessionInvalidated=true)

这些基础功能时从之中拆出来的一小部分，而且并没有将多余的功能蓝图删除干净，只是将报错的部分删除解决掉。

如果需要更多功能请自行研究（我之后需要其他功能的话也会进行更新）

# 使用手册

文件链接：https://github.com/XLJZT/UE_Tool/tree/main/Camera

> 如果链接发生更改，可前往 github 自行寻找

![image-20230818104750257](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/08/18_10_53_46_image-20230818104750257.png)

Camera 文件夹包含这样两个文件

- CameraCopy 包含 CameraPawn 和 一些 Curve 文件
- Input Backup 这是导出的原工程的 Input 文件

## 一、导入 Input 文件



![image-20230818105105275](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/08/18_10_54_4_image-20230818105105275.png)

进入项目的 project settings ，找到 input ，点击右上角的 import ，将下载的文件导入进去。

成功之后类似截图这些按键映射。

## 二、Pawn 参数调整

正常情况下，将文件整体保存到项目的 Content 目录下，其中的一些绑定的参数可能会失效，在参数中最主要的就是关于他们的 Curve，并且如果不进行绑定也会发生报错。

![image-20230818105627276](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/08/18_10_56_40_image-20230818105627276.png)

进入 BP_Explorer_Pawn 文件，替换类似图中的几个变量，变量名和需要绑定的文件名相同，可以进行搜索绑定。

绑定检查之后，运行测试三个功能是否完整，如不完整请检查 log ，将几个 Curve 进行绑定之后，正常时不会输出报错 log 的。

# 参考文章｜视频

[ArchVizExplorer](https://www.unrealengine.com/marketplace/zh-CN/product/archviz-explorer?sessionInvalidated=true)

[虚幻引擎 建筑浏览器 ArchViz_Explorer 介绍和使用方法 教程 UE4/UE5](https://www.bilibili.com/video/BV18r4y127x6/?vd_source=cb5794df42f8077181bc5f31958ae7df)

[UE4 智慧城市可视化 ArchVizExplorer项目剖析 视角操控----鼠标滚轮操控视角丝滑缩放](https://www.cnblogs.com/HHW-Development/p/16457708.html)
