---
title: 迷失岛面试题
description: 
date: 2022-6-20
top_image: https://gitlab.com/XLJZT/img/-/raw/main/blog/image-20220620135717244.png
cover: https://gitlab.com/XLJZT/img/-/raw/main/blog/image-20220620135717244.png
categories: 
- Unity
tag: 
- Games
- 游戏制作
- M_studio


---

# Introduce

首先还是要感谢麦扣老师，[视频链接](https://www.bilibili.com/video/BV1vY4y1W7YD/?spm_id_from=333.788&vd_source=cb5794df42f8077181bc5f31958ae7df)。

![image-20220620135717244](https://gitlab.com/XLJZT/img/-/raw/main/blog/image-20220620135717244.png)

这是一道为期一周的面试题，包含了许多功能，我觉得游戏的主要目的并不是说把功能实现的多么完美，当然也是需要的，更多的是可拓展性，在当前的框架下可以延续出更多的功能。这才是这个面试题的精髓，而且也与我目前需要学习的游戏框架搭建和理念算是不谋而合吧。

接下来我会根据我学习的然后对这个游戏框架进行解析，如有缺漏或者是错误，请见谅。

# 1.场景转换

![场景转换](https://gitlab.com/XLJZT/img/-/raw/main/blog/%E5%9C%BA%E6%99%AF%E8%BD%AC%E6%8D%A2.png)在项目中一共有5个场景，通过点击箭头或物体触发碰撞转场。游戏整体还是一个通过鼠标的点击进行操作的游戏，所以各种交互或者事件都是通过鼠标的点击进行完成。比如场景转换，人物交互，物品交互以及小游戏的实现，在项目里几乎用不到键盘。所以需要一个**CursorManager**进行对其管理。

**CursorManager**通过获取鼠标的世界坐标，实时监测鼠标的位置是否有碰撞体` private Collider2D ObjectAtMousePosition()`,如果返回碰撞体存在并同时触发鼠标点击操作，则调用`ClickAction(GameObject clickObject)`并使用Switch语句根据碰撞体的Tag进行不同的代码调用和操作。

场景转换的物体的Tag为**Teleport**，获取碰撞体上的**Teleport.cs**组件，调用方法`teleport?.TeleportToScene();`,这个代码是绑定在点击跳转按钮的物体上，**Teleport.cs**会获取当前的场景名字和想要前往的场景名字，然后将两个场景名传给单例模式下的**TransitionManager**。

> 关于编写Unity的Attribute [SceneName]
>
> 在教程中使用的是麦扣老师提供的打包的工具或者是付费之后的源代码，为变量添加[SceneName]特性之后，设置的**string**变量就会变成可以选择当前已经build setting的Scence的下拉框。有效防止人工输入时候输错的可能。

**TransitionManager**在场景转换下主要实现了两个功能，一个是转场以及数据的保存和加载，另一个就是使用协程进行转场时的黑幕。

`IEnumerator Fade(float targetAlpha)`:更改Panle的alpha值，根据传入的数字达到变黑和变透明

`IEnumerator TransitionToScene(string from, string to)`:首先设置场景黑幕，然后Unload当前场景，加载传进来to的场景，设置激活场景为to场景，最后将黑幕设置为透明的。

# 2.背包逻辑与UI

![背包](https://gitlab.com/XLJZT/img/-/raw/main/blog/%E8%83%8C%E5%8C%85.png)

# 3.保存物品

![保存物品](https://gitlab.com/XLJZT/img/-/raw/main/blog/%E4%BF%9D%E5%AD%98%E7%89%A9%E5%93%81.png)

# 4.互动逻辑

![互动逻辑](https://gitlab.com/XLJZT/img/-/raw/main/blog/%E4%BA%92%E5%8A%A8%E9%80%BB%E8%BE%91.png)

# 5.人物对话

![对话](https://gitlab.com/XLJZT/img/-/raw/main/blog/%E5%AF%B9%E8%AF%9D.png)

# 6.小游戏

![小游戏](https://gitlab.com/XLJZT/img/-/raw/main/blog/%E5%B0%8F%E6%B8%B8%E6%88%8F.png)

# 总结

其实在这之后还有菜单，小程序的保存，不同周目等等。不过由于接下来还有项目要紧接着去做，这个隔了一个月的项目正式被我烂尾了。不过呢，所有的源码均已被我上传至[github](https://github.com/XLJZT/UnityProject/tree/main/%E8%BF%B7%E5%A4%B1%E5%B2%9BIsoland)，所以如果有小伙伴想要看一下的话，可以前往查看。

之前的的六节我也用思维导图的形式给表达了出来，本着最开始的意愿，学习框架的搭建，希望会对阅读的朋友有所帮助。
