---

title: 第三人称视角摄像机和Ai跟随
description: 
date: 2022-7-24
top_image: https://gitlab.com/XLJZT/img/-/raw/main/blog/006_Third_006.png
cover: https://gitlab.com/XLJZT/img/-/raw/main/blog/006_Third_006.png
categories: 
- Unity
tag: 
- Games
- 游戏制作


---



# Third Person Character

# 创建

使用Cinemachine的 Freelook 相机进行人物第三人称，使用到的插件和素材分别有，Cinemachine，Asset Store里的Basic motion

![Untitled](https://gitlab.com/XLJZT/img/-/raw/main/blog/006_Third_001.png)

将素材导入到Project中，人物拖进Hierarchy里，并生成Perfab。为人物添加Animation Controller 创建一个BlendTree控制人物站立行走跑步的动画。最终效果如下图。

![Untitled](https://gitlab.com/XLJZT/img/-/raw/main/blog/006_Third_002.png)

创建一个行走的地面，hierarchy 窗口里直接创建 3D plane 设置scale的x和z为20，场景为白色。生成一个material ，名字为Ground，选择适合的颜色并设置Albedo为棋盘格，拖拽Ground到刚创建的地面里，为地面添加材质。添加之后发现平铺效果不尽人意，返回Ground设置第一个Tilling的X值和Y值为5或10。这样就生成了一个标准的棋盘样式地面。

![Untitled](https://gitlab.com/XLJZT/img/-/raw/main/blog/006_Third_003.png)

# 添加摄像机

unity上方选择Cinemachine里的FreeLook摄像机，绑定摄像机的Follow at和Look at为Player。根据个人习惯调整X Axis 以及Y Axis的 Input Axis value的Invert（反转）。设置Orbits的Toprig，MiddleRig以及BottomRig的Height和Radius，这三个分别为上中下三个旋转圈的高度以及半径。

![Untitled](https://gitlab.com/XLJZT/img/-/raw/main/blog/006_Third_004.png)

![Untitled](https://gitlab.com/XLJZT/img/-/raw/main/blog/006_Third_005.png)

接下俩设置Aim一的Tracked Object Offset，这个数值为偏移量。当摄像机处在上中下三个旋转圈的范围时，摄像机注视的中心点。设置Top，Middle，Bottom的Y值分别为 1.5，1，1.3。最终效果为下图，黄点与十字交叉中心位置的偏移

![Untitled](https://gitlab.com/XLJZT/img/-/raw/main/blog/006_Third_006.png)

这样一个简陋的第三人称摄像头创建结束。

# 人物沿摄像机方向移动

在这样的一个3D游戏中，当玩家移动摄像机时，摄像机所指向的方向便是玩家前进的方向，所以当玩家转动鼠标移动摄像机时，玩家控制的角色也应该跟随摄像机的指向方向进行移动或者旋转。正常设定中，当角色处于静止状态 Idle 时，转动摄像机，角色不应该发生旋转。

**PlayerInput**

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using System;
public class PlayerInput : MonoBehaviour, IInput
{
    public Action<Vector2> OnMovementInput { get; set; }
    public Action<Vector3> OnMovementDirectionInput { get; set; }

    private void Start()
    {
        //锁定光标到游戏窗口的中心。同时隐藏了硬件光标
        Cursor.lockState = CursorLockMode.Locked;
    }
    private void Update()
    {
        GetMovementInput();
        GetMovementDirectionInput();
    }

    private void GetMovementInput()
    {
        Vector2 input = new Vector2(Input.GetAxis("Horizontal"), Input.GetAxis("Vertical"));
        OnMovementInput?.Invoke(input);
    }

    private void GetMovementDirectionInput()
    {
        var cameraForwardDirection = Camera.main.transform.forward;
        Debug.DrawRay(Camera.main.transform.position, cameraForwardDirection * 10, Color.red);
        //对应值相乘，转化为xz平面的向量
        Vector3 directionToMoveIn = Vector3.Scale(cameraForwardDirection, (Vector3.right + Vector3.forward));
        Debug.DrawRay(Camera.main.transform.position, directionToMoveIn * 10, Color.blue);
        OnMovementDirectionInput?.Invoke(directionToMoveIn);
    }
}

```

**AgentController**

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class AgentController : MonoBehaviour
{
    IInput input;
    AgentMovement agentMovement;
    private void OnEnable()
    {
        input = GetComponent<IInput>();
        agentMovement = GetComponent<AgentMovement>();
        input.OnMovementInput += agentMovement.HandleMovement;
        input.OnMovementDirectionInput += agentMovement.HandleMovementDirection;
    }
    private void OnDisable()
    {
        input.OnMovementInput -= agentMovement.HandleMovement;
        input.OnMovementDirectionInput -= agentMovement.HandleMovementDirection;
    }
}
```

**interface IInput**

```csharp
using System;
using UnityEngine;

public interface IInput
{
    Action<Vector3> OnMovementDirectionInput { get; set; }
    Action<Vector2> OnMovementInput { get; set; }
}
```

**AgentMovement**

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class AgentMovement : MonoBehaviour
{
    CharacterController controller;
    public float rotationSpeed, movementSpeed, gravity = 20f;
    Vector3 movementVector = Vector3.zero;
    private float desiredRotationAngle = 0;

    Animator animator;
    void Start()
    {
        controller = GetComponent<CharacterController>();
        animator = GetComponent<Animator>();
    }

    // Update is called once per frame
    void Update()
    {
        if (controller.isGrounded)
        {
            //获取向量到原点的长度
            if (movementVector.magnitude > 0)
            {
                var animationSpeedMultiplier = SetCorrectAnimation();
                RotateAgent();
                //用currentAnimatorSpeed限制移动速度，使旋转时速度慢
                movementVector *= animationSpeedMultiplier;
            }
        }
        //重力作用
        movementVector.y -= gravity;
        Debug.Log(movementVector);
        controller.Move(movementVector*Time.deltaTime);
    }

    public void HandleMovement(Vector2 input)
    {
        if (controller.isGrounded)
        {
            if (input.y > 0)
            {
                movementVector = transform.forward * movementSpeed;
            }
            else
            {
                movementVector = Vector3.zero;
                animator.SetFloat("move", 0);
            }
        }
    }

    public void HandleMovementDirection(Vector3 direction)
    {
        desiredRotationAngle = Vector3.Angle(transform.forward, direction);
        //判断旋转左右
        var crossProduct = Vector3.Cross(transform.forward, direction).y;
        if (crossProduct < 0)
        {
            desiredRotationAngle *= -1;
        }
    }

    void RotateAgent()
    {
        //出现一个情况 当角度小于10的时候并不会发生旋转
        if (desiredRotationAngle > 10 || desiredRotationAngle < -10)
        {
            transform.Rotate(Vector3.up*desiredRotationAngle*rotationSpeed*Time.deltaTime);
        }
    }
    public float SetCorrectAnimation()
    {
        float currentAnimationSpeed = animator.GetFloat("move");
        
        if (desiredRotationAngle > 10f || desiredRotationAngle < -10f)
        {
            //判断是不是起步状态
            if (currentAnimationSpeed < 0.2f)
            {
                currentAnimationSpeed += Time.deltaTime * 2;
                currentAnimationSpeed = Mathf.Clamp(currentAnimationSpeed, 0, 0.2f);
            }
            animator.SetFloat("move", currentAnimationSpeed);
        }
        else
        {
            if (currentAnimationSpeed < 1f)
            {
                currentAnimationSpeed += Time.deltaTime * 2;

            }
            else
            {
                currentAnimationSpeed = 1;
            }
            animator.SetFloat("move", currentAnimationSpeed);
        }
        return currentAnimationSpeed;
    }

}

```

# Ai简易跟随

AI和人物角色使用的一个perfab，所以只修改了一个脚本`PlayerInput`修改为`SimpleAi`

**SimpleAi**

```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class SimpleAi : MonoBehaviour,IInput
{
    public Action<Vector3> OnMovementDirectionInput { get ; set ; }
    public Action<Vector2> OnMovementInput { get; set; }

    bool playerDetectionResult;
    public Transform eyeTransform;
    Transform playerTransfrom;
    public LayerMask playerLayer;
    public float visionDistance, stopDistance = 2f;

    void Start()
    {
        
    }

    // Update is called once per frame
    void Update()
    {
        playerDetectionResult = DetectPlayer();
        if (playerDetectionResult)
        {
            var directionToPlayer=playerTransfrom.position - eyeTransform.position;
            directionToPlayer = Vector3.Scale(directionToPlayer, Vector3.forward + Vector3.right);
            if (directionToPlayer.magnitude > stopDistance)
            {
                directionToPlayer.Normalize();
                OnMovementInput?.Invoke(Vector2.up);
                OnMovementDirectionInput?.Invoke(directionToPlayer);
                return;
            }
        }
            OnMovementInput?.Invoke(Vector2.zero);
            OnMovementDirectionInput?.Invoke(transform.forward);
        
    }

    private bool DetectPlayer()
    {
        Collider[] colliders = Physics.OverlapSphere(eyeTransform.position, visionDistance,playerLayer);
        foreach (var item in colliders)
        {
            playerTransfrom = item.transform;
            return true;
        }
        playerTransfrom = null;
        return false;
    }

    private void OnDrawGizmos()
    {
        Gizmos.color = Color.green;
        if(playerDetectionResult)
            Gizmos.color=Color.red;
        Gizmos.DrawWireSphere(eyeTransform.position, visionDistance);
    }
}

```

