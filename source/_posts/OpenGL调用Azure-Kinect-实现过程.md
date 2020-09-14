---
title: ' OpenGL调用Azure Kinect 实现过程'
date: 2020-08-03 21:54:54
tags:
- Azure Kinect
---

> 源代码来源：
>
> 源代码作者：无雨森；浙江科技大学在读博士

<!--more-->

## 软件硬件

VS2019

Azure Kinect

## 名称

OpenGL_GLUT_ShowImage

## 本地配置过程

### 配置OpenCV

略过

### 配置模式与头文件路径

- 本地建立VS空项目，release X64模式

![](https://cdn.jsdelivr.net/gh/junlin49/up/20200731102606.png)

- 新建cpp文档，作为源文件，将OpenGL_GLUT_ShowImage的源代码复制进新建的cpp文档
- 配置头文件路径，本文以此为例

![](https://cdn.jsdelivr.net/gh/junlin49/up/20200731102928.png)

项目 -> 属性 -> C/C++ -> 附加包含目录

填入上面图片所示路径

![](https://cdn.jsdelivr.net/gh/junlin49/up/20200731103339.png)

### 安装Kinect包

打开包管理器，下载如下图所示包

![](https://cdn.jsdelivr.net/gh/junlin49/up/20200731101741.png)

安装结束后，即可发现代码已经没有报错

### 修改源代码

此时F5运行，会发现报错：

![](https://cdn.jsdelivr.net/gh/junlin49/up/20200731105327.png)

解决方法：

在windows.h语句上方，加入以下语句：

```c++
#define NOMINMAX
```

整体看起来是这样的

![](https://cdn.jsdelivr.net/gh/junlin49/up/20200731105536.png)

此时F5运行，会出现更多错误

一种是 C++ error:LNK2005 已经在*.obj中定义

另一种是和OpenCV的错误一样，无法解析外部命令；这里就略过了

解决方法：

在 属性->配置属性->链接器->命令行中添加 /FORCE:MULTIPLE 即可



此时F5运行，出现错误：

fatal error LNK1179: 无效或损坏的文件: 重复的XXX

解决方法：

属性-C/C++-优化-全程序优化 选择“否”

此时F5运行，成功！

![](https://cdn.jsdelivr.net/gh/junlin49/up/20200803215839.png)

