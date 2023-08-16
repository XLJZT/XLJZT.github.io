---
title: Ue 的输入增强组件及前后左右移动
description: 
date: 2023-8-16
top_image: https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/08/16_21_43_39_image-20230816214335026.png
cover: https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/08/16_21_43_39_image-20230816214335026.png
categories: 
- UE
tag: 
- UE
- C++
- 游戏

---



# Ue 的输入增强组件&前后左右移动

UE 更新新的输入系统之后，使用方法与旧输入系统有许多的差异，对于我这种小菜鸡来说关注不到增强组件的效率提高，所以会记录一下简单的使用方法。

> 根据使用的 UE 版本不同可能需要在设置里设置使用输入增强组件或者在插件里添加插件，可遵循官方文档操作。

# 文件创建

![image-20230816214335026](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/08/16_21_43_39_image-20230816214335026.png)

实现简单的移动操作一共需要创建如图两个文件，按照顺序分别命名为`IA_Move` 和`IA_Context`。

因为移动操作实在一个平面上，看做为一个二维的操作，所以`IA_Move`文件中，我们设置 Value Type 为 Axis2D (vector2D).

![image-20230816214719772](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/08/16_21_47_21_image-20230816214719772.png)

接下来需要进行对`IA_Context`文件的修改，我们首先需要添加一个 Mappings 映射，选择刚刚创建的 ` IA_Move` 文件作为输入动作

![image-20230816215316567](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/08/16_21_53_18_image-20230816215316567.png)

![image-20230816215904555](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/08/16_21_59_6_image-20230816215904555.png)

需要设置的地方，就是图中标记的 9 处位置，因为是一个二维向量，所以会有三处按键使用修饰符。

> 官方文档解释：
>
> 增强输入支持来自一维源的输入，例如键盘的方向键或常用的"WASD"键配置；可通过应用正确的输入修饰器来实现此控制方案。具体而言，使用 **负（Negate）** 可以将某些键注册为负值，而使用 **交换输入轴值（Swizzle Input Axis Values）** 可以将某些键注册为Y轴，而不是默认的X轴值：

![image-20230816215616547](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2023/08/16_21_56_20_image-20230816215616547.png)

我的理解为：二维的操作仅建立在二维坐标系上，也就是 XY轴，X轴为左右方向，Y 轴为上下方向。

向右就是 X 轴的正方向，向左就是 X 轴的负方向，所以向左时需要添加修饰器- Negate；

初始输入轴值顺序为 XYZ，为获得在 Y 轴上的值，我们使用交换输入轴值修饰器-Swizzle Input Axis Values，根据 Y 轴的正负方向，在向下时再添加负值修饰器- Negate。

# 代码添加

代码中重要的部分主要是三个部分：

1. SetupInputComponent 函数的 override 实现
2. Move 函数的调用
3. BeginPlay 中映射文件的设置

**AuraPlaycontroller.h**

```cpp
#pragma once

#include "CoreMinimal.h"
#include "GameFramework/PlayerController.h"
#include "AuraPlayerController.generated.h"

struct FInputActionValue;
class UInputMappingContext;
class UInputAction;

UCLASS()
class AURAGAME_API AAuraPlayerController : public APlayerController
{
	GENERATED_BODY()
public:
	AAuraPlayerController();

protected:
	virtual void BeginPlay() override;
	virtual void PlayerTick(float DeltaTime) override;
	virtual void SetupInputComponent() override;
	
private:
	UPROPERTY(EditAnywhere,Category="Input")
	TObjectPtr<UInputMappingContext> AuraContext;

	UPROPERTY(EditAnywhere,Category="Input")
	TObjectPtr<UInputAction> MoveAction;

	void Move(const FInputActionValue& InputActionValue);
};

```

**AuraPlaycontroller.cpp**

```cpp
#include "Player/AuraPlayerController.h"
#include "EnhancedInputSubsystems.h"
#include "EnhancedInputComponent.h"

AAuraPlayerController::AAuraPlayerController()
{
	//多人设置。。。
	bReplicates = true;
}
void AAuraPlayerController::BeginPlay()
{
	Super::BeginPlay();
	check(AuraContext);
	//设置映射文件
	UEnhancedInputLocalPlayerSubsystem* Subsystem = ULocalPlayer::GetSubsystem<UEnhancedInputLocalPlayerSubsystem>(GetLocalPlayer());
	if(Subsystem)
	{
		Subsystem->AddMappingContext(AuraContext,0);
	}
	
	//设置鼠标显示
	bShowMouseCursor = true;
	DefaultMouseCursor = EMouseCursor::Default;
	//输入模式
	FInputModeGameAndUI InputMode;
	InputMode.SetLockMouseToViewportBehavior(EMouseLockMode::DoNotLock);
	InputMode.SetHideCursorDuringCapture(false);
	SetInputMode(InputMode);
}

void AAuraPlayerController::PlayerTick(float DeltaTime)
{
	Super::PlayerTick(DeltaTime);
}

void AAuraPlayerController::SetupInputComponent()
{
	Super::SetupInputComponent();

	UEnhancedInputComponent* EnhancedInputComponent = CastChecked<UEnhancedInputComponent>(InputComponent);
	EnhancedInputComponent->BindAction(MoveAction,ETriggerEvent::Triggered,this,&AAuraPlayerController::Move);
	
}

void AAuraPlayerController::Move(const FInputActionValue& InputActionValue)
{
	const FVector2D InputAxisVector = InputActionValue.Get<FVector2D>();
	const FRotator Rotation = GetControlRotation();
	const FRotator YawRotation(0.f,Rotation.Yaw,0.f);

	const FVector ForwardVector = FRotationMatrix(YawRotation).GetUnitAxis(EAxis::X);
	const FVector RightVector = FRotationMatrix(YawRotation).GetUnitAxis(EAxis::Y);

	if(APawn* ControllerPawn = GetPawn<APawn>())
	{
		ControllerPawn->AddMovementInput(ForwardVector,InputAxisVector.Y);
		ControllerPawn->AddMovementInput(RightVector,InputAxisVector.X);
	}
}
```

# 人物朝向移动方向

实现人物始终朝向移动方向移动，主要是**前两行**代码实现的效果。

**Auracharacter.cpp**

```cpp
AAuraCharacter::AAuraCharacter()
{
	//设置角色移动时是否面向移动方向
	GetCharacterMovement()->bOrientRotationToMovement = true;
	//设置角色旋转的速率
	GetCharacterMovement()->RotationRate = FRotator(0.f,400.f,0.f);
	//设置角色移动是否限制在一个平面上
	GetCharacterMovement()->bConstrainToPlane = true;
	//设置角色在开始时是否与所在平面对齐
	GetCharacterMovement()->bSnapToPlaneAtStart = true;

	//设置是否使用控制器的三个旋转
	bUseControllerRotationPitch = false;
	bUseControllerRotationRoll = false;
	bUseControllerRotationYaw = false;
}
```

# 参考文章｜视频

[官方文档](https://docs.unrealengine.com/4.27/zh-CN/InteractiveExperiences/Input/EnhancedInput/)

[学习视频链接](https://www.bilibili.com/video/BV12m4y1W76X?p=7&vd_source=cb5794df42f8077181bc5f31958ae7df)
