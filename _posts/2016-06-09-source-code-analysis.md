---
layout: post
title:  "UnityEvent源码分析（一）"
categories: Unity.Event
author: Bob
date:   2016-06-09
tags:	UnityEvent
---

## 前叙
有很多小伙伴认同UnityEvent实现原理是反射，我觉的有异议，就看了一下源码（反编译），发现事实果真不是这个样子的。UnityEvent有两种注册方式，一是代码注册，方式为UnityEvent.AddListener()；二是序列过后的（即Inspector面板可视化的），可通过鼠标完成方法的注册。所以我准备通过两篇文章来分别讨论这两种实现方式。首先来说一下代码的方式。

## 方法注册
执行步骤如下：
* 1.UnityEvent.AddListener(UnityAction call);
* 2.UnityEventBase.AddCall(BaseInvokableCall call);
* 3.InvokableCallList.AddListener(BaseInvokableCall call);

首先看一下UnityAction是个什么鬼，源码如下：

``` javascript
namespace UnityEngine.Events
{
    public delegate void UnityAction();
}
```
仅仅封装了一个委托而已，然后UnityAction可以通过GetDelegate方法实现UnityAction与InvokableCall（BaseInvokableCall的子类）相互转化，最终执行到InvokableCallList.AddListener（），这个InvokableCallList里面封装的说白了就是委托的集合。

由此可看出其本质就是C#的委托，和反射没有关系，但是其注册方式不是原生的C#委托，是将委托放在List里面了，这也例证了UnityEvent不支持“+=”的简写方式。

## 方法执行
执行步骤的入口为：UnityEvent.Invoke(),剩下的步骤我就不啰嗦了，最终执行的还是委托，代码详见InvokableCall类：
``` javascript
using System;
using System.Reflection;
using System.Runtime.CompilerServices;

internal class InvokableCall : BaseInvokableCall
{
    private event UnityAction Delegate;

    public InvokableCall(UnityAction action)
    {
        Delegate += action;
    }

    public InvokableCall(object target, MethodInfo theFunction) : base(target, theFunction)
    {
        Delegate += System.Delegate.CreateDelegate(typeof(UnityAction), target, theFunction) as UnityAction;
    }

    public override bool Find(object targetObj, MethodInfo method)
    {
        return ((Delegate.Target == targetObj) && (Delegate.Method == method));
    }

    public override void Invoke(object[] args)
    {
        if (AllowInvoke(Delegate))
        {
            Delegate();
        }
    }
}
```

## 结论
通过代码AddListener是C#委托的封装，没有用到反射。至于他与C#原生Event的性能比较[点这里](http://mp.weixin.qq.com/s?__biz=MjM5NjE1MTkwMg==&mid=2651037162&idx=1&sn=2a3ccb3ba813521f04034438e512ad34&scene=1&srcid=0525taR6jPSURJYWxp5KRwDw#wechat_redirect)
