---
title: 2.5D摄像机移动放大缩小
description: 
date: 2022-11-14
top_image: https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2022/11/14_21_39_26_image-20221114213232738.png
cover: https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2022/11/14_21_39_26_image-20221114213232738.png
categories: 
- Unity
tag: 
- Unity
- 游戏

---

# 2.5D摄像机移动放大缩小

>老规矩，上[链接](https://www.youtube.com/watch?v=pJQndtJ2rk0&t=563s) ，视频在 youtube 上

由于最近做的一个小游戏，是处在一个 2.5D 的一个视角下，为了完善游玩时的摄像机的可操作性，便添加了这个功能。

## 必要组件

- Cinemachine

需要使用到 Cinemachine 的 VirtualCamera，通过对组件里的参数值进行调整，达到效果。

## 实现

创建一个 VirtualCamera 摄像机

创建一个空物体，用于摄像机的 LookAt 和 Follow 绑定，绑定之后，摄像机视角便会一直跟随物体移动旋转，我们只需要通过对物体进行前后左右的移动和左右的旋转，便可以达到摄像机移动旋转的效果。

>我们创建的是一个空物体，所以在游戏场景里我们是不会看到这个空物体的存在，也不会出现其他的bug

![image-20221114213232738](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2022/11/14_21_39_26_image-20221114213232738.png)

然后我们编写的代码放到我们的空物体上，便可以实现效果。

代码如下：

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Cinemachine;

public class CameraController : MonoBehaviour
{
    public CinemachineVirtualCamera cinemachineVir;
    public float moveSpeed = 5f;//移动速度
    public float rotateSpeed = 100f;//旋转速度
    public bool EnableEdgeScroll = false;//是否开启边缘移动
    public float edgeScrollSize = 20f;//屏幕最外侧移动范围
    public float minFieldOfView = 10f;//最小视野
    public float maxFieldOfView = 50f;//最大视野

    private Input_sys inputsys;//自动生成的输入脚本

    bool mouseClickLeft = false;//鼠标左键点击
    Vector2 move_vec2;//移动方向
    Vector2 mouse_pos;//鼠标位置
    float rotateDir;//旋转方向
    Vector2 lastMousePos;//上次鼠标位置，用于鼠标拖拽
    float targetFieldOfView = 50f;//摄像机视野
    Vector2 mouse_scroll;//鼠标滚轮
    Vector3 followOffset;//摄像机的body follow offset

    private void Awake()
    {
        inputsys = new Input_sys();
        followOffset = cinemachineVir.GetCinemachineComponent<CinemachineTransposer>().m_FollowOffset;
    }
    private void Update()
    {
        Vector3 inputDir = new Vector3(move_vec2.x, 0, move_vec2.y);
        //鼠标位置移动
        inputDir = MouseEdgeMovePos(inputDir);
        inputDir = MouseDragMove(inputDir);
        //矫正旋转摄像机后的输入移动
        Vector3 moveDir = transform.forward * inputDir.z + transform.right * inputDir.x;
        transform.position += Time.deltaTime * moveSpeed * moveDir;
        //QE用来旋转
        transform.eulerAngles += new Vector3(0, rotateDir * rotateSpeed * Time.deltaTime, 0);
        //视野缩放
        CameraZoom_FielfOfView();
        // CameraZoom_MoveForward();

    }
    private void OnEnable()
    {
        //必须开启之后 才能接收到输入的信息
        inputsys.Keyboard.Enable();
        inputsys.Keyboard.Move.performed += ctx => move_vec2 = ctx.ReadValue<Vector2>();
        inputsys.Keyboard.Move.canceled += ctx => move_vec2 = ctx.ReadValue<Vector2>();
        inputsys.Keyboard.Rotate.performed += ctx => rotateDir = ctx.ReadValue<float>();
        inputsys.Keyboard.Rotate.canceled += ctx => rotateDir = ctx.ReadValue<float>();

        inputsys.Mouse.Enable();
        inputsys.Mouse.MousePos.performed += ctx => mouse_pos = ctx.ReadValue<Vector2>();
        inputsys.Mouse.MouseClickLeft.performed += ctx =>
        {
            mouseClickLeft = !mouseClickLeft;
            lastMousePos = mouse_pos;
        };
        inputsys.Mouse.MouseScroll.performed += ctx => mouse_scroll = ctx.ReadValue<Vector2>();
    }

    /// <summary>
    /// 屏幕边缘移动
    /// </summary>
    /// <param name="inputDir"></param>
    /// <returns></returns>
    private Vector3 MouseEdgeMovePos(Vector3 inputDir)
    {
        //如果关闭状态 直接返回原值
        if (!EnableEdgeScroll)
            return inputDir;

        if (mouse_pos.x < edgeScrollSize)
        {
            inputDir.x += -1f;
        }
        else if (mouse_pos.x > Screen.width - edgeScrollSize)
        {
            inputDir.x += 1f;
        }

        if (mouse_pos.y < edgeScrollSize)
        {
            inputDir.z += -1f;
        }
        else if (mouse_pos.y > Screen.height - edgeScrollSize)
        {
            inputDir.z += 1f;
        }
        return inputDir;
    }
    /// <summary>
    /// 鼠标点击拖拽移动
    /// </summary>
    /// <param name="inputDir"></param>
    /// <returns></returns>
    private Vector3 MouseDragMove(Vector3 inputDir)
    {
        if (mouseClickLeft)
        {
            Vector2 mouseMoveDelta = (Vector2)mouse_pos - lastMousePos;
            inputDir.x -= mouseMoveDelta.x;
            inputDir.z -= mouseMoveDelta.y;
            lastMousePos = mouse_pos;
        }
        return inputDir;

    }
    /// <summary>
    /// 摄像机的鼠标滚轮缩放,通过调节摄像机的fieldofview
    /// </summary>
    private void CameraZoom_FielfOfView()
    {
        if (mouse_scroll.y > 0f)
        {
            targetFieldOfView -= 5f;
        }
        else if (mouse_scroll.y < 0f)
        {
            targetFieldOfView += 5f;
        }
        targetFieldOfView = Mathf.Clamp(targetFieldOfView,minFieldOfView,maxFieldOfView);
        cinemachineVir.m_Lens.FieldOfView = Mathf.Lerp(cinemachineVir.m_Lens.FieldOfView,targetFieldOfView,Time.deltaTime*5f);
    }

    private void CameraZoom_MoveForward(){
        //归一化处理 保留向量方向
        Vector3 zoomDir = followOffset.normalized;
        if (mouse_scroll.y > 0f)
        {
            followOffset -= zoomDir;
        }
        else if (mouse_scroll.y < 0f)
        {
            followOffset += zoomDir;
        }
        //followOffset 是vec3，所以用magnitude计算长度
        if (followOffset.magnitude < minFieldOfView){
            followOffset = zoomDir * minFieldOfView;
        }else if(followOffset.magnitude > maxFieldOfView){
            followOffset = zoomDir * maxFieldOfView;
        }
        cinemachineVir.GetCinemachineComponent<CinemachineTransposer>().m_FollowOffset = Vector3.Lerp(cinemachineVir.GetCinemachineComponent<CinemachineTransposer>().m_FollowOffset,followOffset,Time.deltaTime*5f);
    }
}

```

## 可能的问题

**移动时的抖动**

- 将CinemachineVirtualCamera -> Body 的 x/y/z Damping 值修改为 0
- 将CinemachineVirtualCamera -> Aim 的 Horizontal/Vertical Damping 的值修改为 0

![image-20221114213835243](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2022/11/14_21_39_26_image-20221114213835243.png)
