---
title: HOLOLENS2开发1
date: 2020-05-20 13:57:58
tags:
- 毕设
---

Hololens初步上手

<!--more-->

## HOLOLENS2 开发 - 环境配置

>现在毕设项目已经做完，现在复盘下走过的坑
>
>可通过邮件2998963449@qq.com联系

### 第1步：单独安装Win10 SDK

SDK下载链接：[I am here](https://developer.microsoft.com/zh-cn/windows/downloads/windows-10-sdk/)

1. 安装时必须使用短路径！（建议放在C盘），如`C:\SDK`
2. 请记住这个路径，因为后续在VS中部署时，解决错误时需要用到此路径

### 第2步：安装Unity，VS2019

安装完Win10 SDK后

### 1. 安装Unity

链接：[传送门](https://unity.cn/releases)

#### 注意

1. 推荐下载下图所示，因为其他的安装渠道，博主并没有看到有单独安装组件的界面；不过需要外网才能提高网速，博主在不开启外网情况下，网速在30-40k/s
   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200302144820699.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNjU2MjUw,size_16,color_FFFFFF,t_70)

  

2. 安装组件界面：（不必勾选这么多）
   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200302145208244.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNjU2MjUw,size_16,color_FFFFFF,t_70)

3. 下载完成后，如需要 Vuforia 组件，请[参考此链接](https://blog.csdn.net/qq_43656250/article/details/104475189)

### 2. 安装VS2019

链接：[传送门](https://visualstudio.microsoft.com/zh-hans/vs/)

#### 注意：

1. 组件选择：
   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200302145823352.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNjU2MjUw,size_16,color_FFFFFF,t_70)

  

2. 使用Unity的游戏开发：不要勾选编辑器！之前装过了

3. 通用Windows平台开发中会包含Win10 SDK，没办法

### 3.后续问题

到此基本就已经结束了，但是在Unity做完项目以后，使用VS2019打开项目时，并不能直接使用，会出现以下报错。

```
MSB3774找不到SDK“ WindowsMo​​bile，版本= 10.0.17763.0”
```

解决方案：

https://developercommunity.visualstudio.com/content/problem/402362/msb3774-could-not-find-sdk-windowsmobile-version10.html

![](https://i.loli.net/2020/05/20/jcPnxkgND3opC8a.png)



