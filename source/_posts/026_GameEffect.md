---
title: UE Game Effect的数值计算
description: 
date: 2023-8-25
top_image: https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/08/25_9_59_25_image-20230825092950426.png
cover: https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/08/25_9_59_25_image-20230825092950426.png
categories: 
- UE
tag: 
- UE
- C++
- 游戏

---



# Game Effect的数值计算

主要会记录以下的几个数值

![image-20230825092950426](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/08/25_9_59_25_image-20230825092950426.png)

# Duration Policy

- Instant：GameEffect 立刻生效，且只生效一次
- Infinite：GameEffect 一直生效，生效周期取决于 Catage:Period::Period(单位秒)
- HasDuration：GameEffect生效一段时间，生效周期取决于Catage:Period::Period(单位秒)，同时生效时长可根据DurationMagnitude来决定，若Duration=0，则类似于Infinite，表示该效果一直存在。

文章记录使用 Instant，测试拾取物品的效果

# Modifiers：

> 可以在一个 Game Effect 中添加多个 Modifiers

- Attribute 选择需要设置的属性
- Modifier OP 修饰方法
  - Add
  - Multiple
  - Divide
  - Override
  - Invalid

## Modifier Maginitude

Maginitude Calculation Type： 计算的类型

	- Scalable Float ：根据值进行计算
	- Attribute Base

**当 Maginitude Calculation Type 为 Attribute Base时**：

**Attribute Based Maginitude**

- Coefficient
- Pre Multiply Additive Value
- Post Multiply Additive Value
- Backing Attribute
  - Attribute to Capture ：采用的属性
  - Attribute Source ：属性值的来源，使用目标的属性值还是自身的属性值 （图中的数字就是值）

计算过程：

![image-20230825095916589](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/08/25_9_59_38_image-20230825095916589.png)

当有多个Modifier 时：

![image-20230825100407934](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/08/25_10_4_12_image-20230825100407934.png)

> 图中的 Health = 10 ，是目标要修改的值，最后得到的值 7.26 就是 Health 拾取物品后的最终值。

# 参考文章｜视频

[图片中的视频](https://www.bilibili.com/video/BV12m4y1W76X?p=66&vd_source=cb5794df42f8077181bc5f31958ae7df)
