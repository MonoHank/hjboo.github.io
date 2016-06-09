---
layout: post
title:  "UnityEvent源码分析（二）"
categories: code
author: Bob
date:   2016-06-09
tags:	UnityEvent
---

通过上篇博客可知代码AddListener的方式实质为封装的C#的委托，详见[UnityEvent源码分析（一）](http://www.hjboo.com/code/2016/06/08/source-code-analysis1.html)，这边分析一下可视化的注册方式，此种实现方式比较复杂，Inspector效果如下：

<img src="http://www.hjboo.com/assets/images/UnityEvent1.jpg" width = "384" height = "124">

<a href="/assets/images/UnityEvent1.jpg" data-lightbox="pebbletime" align="left">
	<img src="/assets/images/UnityEvent1.jpg" class="post-image-half"/>
</a>

Debug模式下的效果如下：

<img src="http://www.hjboo.com/assets/images/UnityEvent2.jpg" width = "384" height = "295">

在Debug模式下可以看出里面主要参数为PersistentCalls这个字段名，从源码可知其对应的类是PersistentCallGroup，其内定义了一个集合：List<PersistentCall> m_Calls = new List<PersistentCall>();，从图中也可看出就是Calls,里面有包含的重要信息，比如：目标物体，方法名，参数等，这些信息是通过反射赋值的，那是不是就能肯定此种UnityEvent的原理就是反射呢？答案是不完全对！你想一下此时的反射仅仅是在编辑器中执行的，它的作用有两个，一是方法可视化，二是赋值必要信息。

当我们编辑中选中一个方法后，m_Calls就会被赋值，里面包含方法名等有用信息，当满足执行条件是他会执行Invoke方法，它的执行步骤大致是： 首先MethodInfo method = type.GetMethod(string name, BindingFlags bindingAttr, Binder binder, Type[] types, ParameterModifier[] modifiers);，这样就得到了MethodInfo method；然后通过Delegate.CreateDelegate(Type type, object firstArgument, MethodInfo method)方法我们得到了一个委托。截止到这里总结为：编辑器里反射出方法名等信息，运行时将字符串的方法名转化成委托，然后执行委托。

说以这种方式的UnityEvent是一个优化的过的反射，其执行速度是大大优于单纯反射执行的，为什么这样优化后执行速度就会快呢，首先我们要分析一下反射慢的原因：System.Reflection.RuntimeMethodInfo.Invoke 方法，它首先需要检查参数（检查默认参数、类型转换之类的），然后检查各种 Flags，然后再调用 UnsafeInvokeInternal 完成真正的调用过程，显然这是很浪费时间的，而把它构造成委托之后就避免这个问题，当然这比直接调用还是要慢的，但除非常在特殊情况下，否则你是察觉不到的。

NGUI的回调事件的源码当初大体也浏览过，其实现原理和这种方式也大同小异。但可能会有这样的疑问，那是不是代码AddListener方式要比可视化的方式要快呢，理论上是要快的，但实测差异是在一个数量级的，几乎看不出来谁更快。

## 结论
可视化方式的UnityEvent原理是反射与委托结合使用。其速度与[第一种方式](http://www.hjboo.com/code/2016/06/08/source-code-analysis1.html)差不多。反射是个好东西，好多框架里都离不开反射，它非常灵活，解耦，只要好好驾驭，就可以放心的使用！
