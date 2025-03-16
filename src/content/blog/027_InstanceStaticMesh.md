---
title: UE系统的ISM使用
description: UE系统的ISM使用
pubDate: 2023-8-28
heroImage: https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/08/28_10_29_45_image-20230828101232352.png
tags: 
- 游戏
- C++
- UE
---

# UE系统的ISM使用

 面临的需求：需要在场景中渲染成千甚至上万个可以点击交互的 Actor ，而且每个 Actor 均绑定着不同的信息，交互之后需要将这些信息展示在 UHD 的面板上，信息条目为 15 条至 30 条不等。另外需要场景中的 Actor 可以按照一定的形式进行排列，比如矩阵排列或者螺旋排列等。

完成这个需求对于一个新建的项目来说可拆分为几个点：

1. 物体生成
2. 物体世界位置生成算法
3. 信息绑定
4. 鼠标点击交互
5. 信息展示

其中的一些点我会一笔带过，因为大家的实现方式不同，我会主要记录一下关于我做这个需求经历的关于物体生成的弯路。

# 弯路

> 因为我做这个需求时，是一刚接触 UE 不到一个月时间的实习生身份去做的，乃至记录时仍不到两个月，所以肯定会有很多问题，仅做避坑用。

我拿到需求的时候，我就将这个项目拆为了以上的 5 个点，第 5 点我有之前实现的经历，而且本身实现就很简单，所以主要还是前三点难倒我了。

在场景中构造这么多 Actor 是个新手也会知道肯定会卡，并且还需要在每个 Actor 上绑定信息，美术也会在 Actor 上添加动画和粒子特效，所以在生成的时候就要节省资源，此时我还不知道有 ISM 这个东西。

所以我硬着头皮去学物体生成，3D widget，写了一个垃圾的位置生成算法，在每个 Actor 上添加一个 struct，存储绑定的信息。

果然，其实效果还不错。

在生成数量小的情况下，这些都不是问题，而且在实现鼠标点击的时候，也只是需要开启鼠标显示，以及开启 Actor 的鼠标点击事件，就可以调用 UHD 进行信息展示，响应也很快。

当 Actor 的数量超过 1000 时，明显感觉到 FPS 发生帧率波动，因为时构建的一个矩阵，当摄像机进入矩阵内部进行鼠标点击时，帧率波动就会更加明显。

当我搁置几天后，重提这个需求时，美术告诉我应该用 Instance 去做，然后发现了新世界。

# ISM(Instance Static Mesh)

## 介绍：

在虚幻引擎（Unreal Engine）中，"Instance Static Mesh"（实例化静态网格）是一种特殊的网格实例化技术，用于在游戏中高效地渲染大量相似的对象。

静态网格是指在编辑器中创建的不可变的网格对象，例如建筑物、地形、道具等。然而，当需要在场景中多次复制相同的静态网格时，传统方法是为每个复制的实例创建一个独立的网格对象，这可能会占用大量的内存和计算资源。这时，使用实例化静态网格可以有效地解决这个问题。

实例化静态网格允许您在场景中创建一个实例化对象，然后通过指定位置、缩放和旋转等参数，复制该对象的多个实例。这些实例都共享同一个网格数据，因此占用的内存和计算资源相对较少。这样，您可以在游戏中创建大量相似的对象，而不会对性能产生太大的影响。

另外，实例化静态网格还可以提供更好的渲染性能，因为引擎可以使用批处理技术来一次性渲染多个实例，从而减少渲染调用的开销。

## 使用

在 Actor 中添加一个 Instance Static Mesh Component，或者通过 c++ or 蓝图添加一个新的ISM组件。

![image-20230828101232352](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/08/28_10_29_45_image-20230828101232352.png)

![image-20230828101214295](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/08/28_10_29_53_image-20230828101214295.png)

通过使用 add instance 方法给 ISM 组件添加新的instance，并传入新的 instance 的 transform。

>图中的 Get Instance Transforms 为获取 instance 的 transform 算法

![image-20230828101405426](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/08/28_10_29_55_image-20230828101405426.png)

## 三维矩阵生成算法

**参数介绍：**

Index ： 生成物体的序号

Sum：生成物体的总数

Interval_Value：物体之间的间隔

```c++
FVector ASpawnActor::GetInstanceTransforms(int32 Index, int32 Sum, float Interval_Value)
{
	//1-10
	if (Sum < 11)
	{
		const int32 ColumnNum = (Sum + 1) / 2;
		const int32 Row = Index / ColumnNum;
		const int32 IndexLoc = Index % ColumnNum;
		return FVector(IndexLoc * Interval_Value, Row * Interval_Value, 0.f);
	}
	else if (Sum >= 11 && Sum < 125)
	{
		const int32 RowNum = CalculateIntegerSquareRoot(Sum);
		//向上取整
		const int32 ColumnNum = (Sum - 1) / RowNum + 1;
		const int32 IndexLoc = Index % ColumnNum;
		const int32 Row = Index / ColumnNum;
		return FVector(IndexLoc * Interval_Value, Row * Interval_Value, 0.f);
	}
	else
	{
		const int32 RowNum = CalculateIntegerCubeRoot(Sum);
		const int32 ColumnNum = RowNum;
		const int32 LevelSum = RowNum * ColumnNum;

		const int32 Level = Index / LevelSum;
		const int32 Reminder = Index % LevelSum;
		const int32 Row = Reminder / ColumnNum;
		const int32 IndexLoc = Reminder % ColumnNum;
		return FVector(IndexLoc * Interval_Value, Row * Interval_Value, Level * Interval_Value);
	}
}

```

## 结果截图

![image-20230828101829633](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/08/28_10_30_8_image-20230828101829633.png)

![image-20230828101907975](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/08/28_10_30_18_image-20230828101907975.png)

>给每一个 Instance 添加了一个 Widget，通过位置和生成序号进行绑定。

# 鼠标点击

通过 FHitResult 判断组件的类型并获取 Item 的 Index。

ISM 内部的所有 instance 拥有一个不同的 index，可以通过这个 index 与需要展示的信息进行简单的绑定。

```c++
void ABasePlayerController::LeftMousePress()
{
	FHitResult CursorHit;
	GetHitResultUnderCursor(ECC_Visibility, false, CursorHit);
	if (!CursorHit.bBlockingHit) return;

	UInstancedStaticMeshComponent* InstancedStaticMeshComponent = Cast<UInstancedStaticMeshComponent>(
		CursorHit.GetComponent());
	if (!InstancedStaticMeshComponent || DataTable == nullptr) return;

	const int Index = CursorHit.Item;
	UE_LOG(LOG_Controller, Warning, TEXT("鼠标点击的 Item：%d"), Index);
	//索引超过数据上限
	if (Index >= DataTable->GetRowNames().Num()) return;

	FName IndexName = DataTable->GetRowNames()[Index];
	FDataStruct* SidebarWidgetData = DataTable->FindRow<FDataStruct>(IndexName,TEXT(""));
	HUD->AppearSidebarWidget(*SidebarWidgetData);
}
```



# 参考文章｜视频

🌟[UE4 ISM(实例化静态网格体)理解](https://zwcloud.net/#blog/112)

[UE5使用ISM（InstanceStaticMesh）或HISM生成大量静态网格体提升渲染性能](https://www.bilibili.com/read/cv19015625)

[UE5使用XGrid生成器一键生成铁丝网围栏](https://www.bilibili.com/video/BV1Fe4y1U7PL/?vd_source=cb5794df42f8077181bc5f31958ae7df)

[[UE4中如何实现放置大量的使用同一模型资源的StaticMeshActor不卡](https://www.cnblogs.com/seamus1995/p/14725599.html)](https://www.cnblogs.com/seamus1995/p/14725599.html)

[UE4 ISM(实例化静态网格体)理解](https://zhuanlan.zhihu.com/p/339732645)

[Instanced Static Mesh](https://docs.unrealengine.com/4.27/en-US/BlueprintAPI/Components/InstancedStaticMesh/)
