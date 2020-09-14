---
title: 问题-Vuforia错误：Failed-load-data-set-xxx
date: 2020-04-02 18:26:04
tags:
- 问题
---

vuforia运行问题

<!--more-->

### 引言

按照常规套路，布置好AR Camera , 2个model target 之后，运行会报错。提示 failed-load-data-set-xxx 

<!-- more -->

错误提示：

> Failed to load data set all.
> UnityEngine.Debug:LogError(Object)
> Vuforia.DatabaseLoadARController:LoadDatasets()
> Vuforia.VuforiaARController:StartAfterInitializationCompleted()
> Vuforia.VuforiaARController:Start()
> System.Delegate:DynamicInvoke(Object[])
> Vuforia.DelegateHelper:InvokeDelegate(Delegate, Object[])
> Vuforia.DelegateHelper:InvokeWithExceptionHandling(Action)
> Vuforia.VuforiaBehaviour:Start()

### 解决过程

推荐 Vuforia 官方论坛搜索，此问题已经有人反映过，链接：https://developer.vuforia.com/forum/model-targets/failed-load-data-set-xxx ；

### 解决方法

尝试换Key，但是没啥用；

亲测解决方法：Unity 版本2019及以上，Vuforia 9.0 即可，就没有报错了

Vuforia 更新可以看我的另一篇文章：[https://junlu.club/2020/04/02/%E9%97%AE%E9%A2%98-Vuforia%E6%9B%B4%E6%96%B0%E9%94%99%E8%AF%AF-Failed-to-resolve-packages/](https://junlu.club/2020/04/02/问题-Vuforia更新错误-Failed-to-resolve-packages/)