---
title: FSM有限状态机
description: 
date: 2022-11-10
top_image: https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2022/11/14_21_55_38_image-20221114215500062.png
cover: https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2022/11/14_21_55_38_image-20221114215500062.png
categories: 
- Unity
tag: 
- 游戏制作
- Unity
---

# 有限状态机

代码参考此[作者](https://blog.csdn.net/weixin_43839583/article/details/105501884) ，做了一些改变

FSM ，有限状态机，一个可以枚举出有限个状态，并且这些状态在特定条件下是能够来回切换的。

在游戏中经常看到的一些AI，如敌人巡逻，巡逻过程中看到玩家就追击，追上了就攻击，追不上并且有了一定的距离就返回去继续巡逻。

为什么要这样做呢？为了方便扩展，如果后期需要加入新状态，只需要继承基类，添加实现就好，不用修改原来的代码，对外界开放扩展，这就是开闭原则（对修改关闭，对扩展开放）。![image-20221114215500062](https://gitlab.com/XLJZT/img/-/raw/main/blog/pictures/2022/11/14_21_55_38_image-20221114215500062.png)

## 枚举状态类

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class FSMEnum
{

}
/// <summary>
/// 转换条件
/// </summary>
public enum Transition
{
    NullTransition=0,//空的转换条件
    AttackPlayer,//看到玩家
    LostPlayer,//周围没有玩家
    EventAttack,//强制事件切换
    EventDefence,//强制事件切换
    LowHealth,//血量低进入防御状态
    UnderAttack,//受到攻击
}

/// <summary>
/// 当前状态
/// </summary>
public enum StateID
{
    NullState,//空的状态
    Patrol,//巡逻状态
    Attack,//攻击状态
    Defence,//防御状态
}

```

## FSMState

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public abstract class FSMState
{
    protected StateID stateID;
    public StateID StateID { get { return stateID; } }
    protected Dictionary<Transition, StateID> transitionStateDic = new Dictionary<Transition, StateID>();
    protected FSMSystem fSMSystem;
    protected GameObject Go;
    /// <summary>
    /// 有参构造函数
    /// </summary>
    /// <param name="fSMSystem"></param>
    public FSMState(FSMSystem fSMSystem)
    {
        this.fSMSystem = fSMSystem;
    }

    /// <summary>
    /// 添加转换条件
    /// </summary>
    /// <param name="trans">转换条件</param>
    /// <param name="id">转换条件满足时执行的状态</param>
    public void AddTransition(Transition trans,StateID id)
    {
        if(trans==Transition.NullTransition)
        {
            Debug.LogError("不允许NullTransition");
            return;
        }
        if(id==StateID.NullState)
        {
            Debug.LogError("不允许NullStateID");
            return;
        }
        if(transitionStateDic.ContainsKey(trans))
        {
            Debug.LogError("添加转换条件的时候" + trans + "已经存在于transitionStateDic中");
            return;
        }
        transitionStateDic.Add(trans, id);
    }
    /// <summary>
    /// 删除转换条件
    /// </summary>
    public void DeleteTransition(Transition trans)
    {
        if (trans == Transition.NullTransition)
        {
            Debug.LogError("不允许NullTransition");
            return;
        }
        if(!transitionStateDic.ContainsKey(trans))
        {
            Debug.LogError("删除转换条件的时候" + trans + "不存在于transitionStateDic中");
            return;
        }
        transitionStateDic.Remove(trans);
    }

    /// <summary>
    /// 获取当前转换条件下的状态
    /// </summary>
    public StateID GetOutputState(Transition trans)
    {
        if(transitionStateDic.ContainsKey(trans))
        {
            return transitionStateDic[trans];
        }
        return StateID.NullState;
    }
    /// <summary>
    /// 进入新状态之前做的事
    /// </summary>
    public virtual void DoBeforeEnter() { }
    /// <summary>
    /// 离开当前状态时做的事
    /// </summary>
    public virtual void DoAfterLeave() { }
    /// <summary>
    /// 当前状态所做的事
    /// </summary>
    public abstract void Act(GameObject npc);
    /// <summary>
    /// 在某一状态执行过程中，新的转换条件满足时做的事
    /// </summary>
    public abstract void Reason(GameObject npc);//判断转换条件
}


```

## FSMSystem

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class FSMSystem
{
    private Dictionary<StateID, FSMState> stateDic = new Dictionary<StateID, FSMState>();
    private StateID currentStateID;
    private FSMState currentState;

    /// <summary>
    /// 更新npc的动作
    /// </summary>
    public void Update(GameObject npc)
    {
        currentState.Act(npc);
        currentState.Reason(npc);
    }

    /// <summary>
    /// 添加新状态
    /// </summary>
    public void AddState(FSMState state)
    {
        if(state==null)
        {
            Debug.LogError("FSMState不能为空");
            return;
        }
        if(currentState==null)
        {
            currentState = state;
            currentStateID = state.StateID;
        }
        if(stateDic.ContainsKey(state.StateID))
        {
            Debug.LogError("状态" + state.StateID + "已经存在，无法重复添加");
            return;
        }
        stateDic.Add(state.StateID, state);
    }

    /// <summary>
    /// 删除状态
    /// </summary>
    public void DeleteState(StateID stateID)
    {
        if(stateID==StateID.NullState)
        {
            Debug.LogError("无法删除空状态");
            return;
        }
        if(!stateDic.ContainsKey(stateID))
        {
            Debug.LogError("无法删除不存在的状态");
            return;
        }
        stateDic.Remove(stateID);
    }

    /// <summary>
    /// 执行过渡条件满足时对应状态该做的事
    /// </summary>
    public void PerformTransition(Transition transition)
    {
        if(transition==Transition.NullTransition)
        {
            Debug.LogError("无法执行空的转换条件");
            return;
        }
        StateID id = currentState.GetOutputState(transition);
        if(id==StateID.NullState)
        {
            Debug.LogWarning("当前状态" + currentStateID + "无法根据转换条件" + transition + "发生转换");
            return;
        }
        if(!stateDic.ContainsKey(id))
        {
            Debug.LogError("在状态机里面不存在状态" + id + ",无法进行状态转换");
            return;
        }
        FSMState state = stateDic[id];
        currentState.DoAfterLeave();
        currentState = state;
        currentStateID = state.StateID;
        currentState.DoBeforeEnter();
    }
}


```

## 示例攻击状态

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.AI;
public class AttackState : FSMState
{

    public float visionDistance = 1f;//玩家自动攻击可视范围
    public LayerMask playerLayer = 1 << LayerMask.NameToLayer("player");
    AIController Controller;
    private GameObject currentAttackTarget;//当前攻击目标
    public AttackState(FSMSystem fSMSystem, GameObject gameObject, AIController controller) : base(fSMSystem)
    {
        stateID = StateID.Attack;
        Go = gameObject;
        Controller = controller;
    }

    public override void Act(GameObject npc)
    {
        if (currentAttackTarget == null)
            currentAttackTarget = DetectPlayer();
        try
        {
            Controller.MoveToTarget(currentAttackTarget.gameObject.transform.position, true);
            //Debug.Log(currentAttackTarget.gameObject.transform.position);
        }
        catch (System.Exception)
        {
            currentAttackTarget = DetectPlayer();
            Debug.Log("找不到攻击物体,切换目标或更改状态");
        }


    }

    public override void Reason(GameObject npc)
    {
        //周围没有别的玩家
        if (currentAttackTarget == null)
        {
            Controller.MoveToTarget(Vector3.zero, false);
            fSMSystem.PerformTransition(Transition.LostPlayer);

        }
    }

    private GameObject DetectPlayer()
    {
        //返回最近的目标
        //检测player层
        Collider[] colliders = Physics.OverlapSphere(Go.transform.position, visionDistance, playerLayer);
        var minDistance = Mathf.Infinity;
        GameObject mDgo = null;
        foreach (var item in colliders)
        {
            if ((Go.transform.position - item.transform.position).magnitude < minDistance && (Go.transform.position - item.transform.position).magnitude > 0.2f)
            {
                minDistance = (Go.transform.position - item.transform.position).magnitude;
                mDgo = item.gameObject;
            }

        }
        return mDgo;
    }

}

```

## 初始化

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.AI;
public class AIController : MonoBehaviour
{
    private FSMSystem fsm;//状态机
    private void Awake()
    {
        InitFSM();
    }

    void Update()
    {
        //每帧更新当前状态
        fsm.Update(gameObject);
    }
    /// <summary>
    /// 初始化FSM状态机
    /// </summary>
    private void InitFSM()
    {
        fsm = new FSMSystem();

        FSMState attackState = new AttackState(fsm, gameObject, this);
        //防御回血
        attackState.AddTransition(Transition.LowHealth, StateID.Defence);
        //周围没有人
        attackState.AddTransition(Transition.LostPlayer, StateID.Patrol);
        fsm.AddState(attackState);
    }
    /// <summary>
    /// 移动到相应位置
    /// </summary>
    /// <param name="pos">位置</param>
    /// <param name="isStopped">是否移动，需要移动时为true，转换状态时为false</param>
    public void MoveToTarget(Vector3 pos, bool isStopped)
    {
        // if (id != playerStats.id_player)
        // {
        //     return;
        // }
        navAgent.isStopped = !isStopped;
        navAgent.destination = pos;
    }
}
```

# END

通过一些流行且实用的游戏设计模式，我们可以更好的获得游戏拓展以及开发上的轻松。不得不说，这个fsm真牛！！！

