---
layout: post
title:  "UnityEvent源码分析（二）"
categories: code
author: Bob
date:   2016-06-09
tags:	UnityEvent
---

通过上篇博客可知代码AddListener的方式实质为封装的C#的委托，详见[UnityEvent源码分析（一）]()，这边分析一下可视化的注册方式，此种实现方式比较复杂，效果如下：
