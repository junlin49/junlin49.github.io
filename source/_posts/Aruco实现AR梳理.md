---
title: Aruco实现AR梳理
date: 2020-09-14 08:26:52
categories: Aruco
tags:
- Aruco
---

使用Aruco实现AR效果的方式，实在是很基础，对于相机标定，OpenGL等基础知识具有很好的加强作用，有待长期梳理更新

<!--more-->

# Aruco AR过程梳理-1

---

## 概述

**目的**：在*VS2019*运行的*OpenGL*窗口上，*generated virtual computer generated 3D graphics on the realistic environment , i.e.* 在*OpenGL*的相机位置看到*AR*效果

**思路**：

**OpenGL的世界中有虚拟相机，现实世界中有真实相机，只要将两个世界的世界坐标重合，相机位置重合，分别渲染虚拟模型与真实场景，即可达到AR效果**

- 生成*OpenGL*窗口
- 将外部相机捕获的*frame*传递到*OpenGL*中，作为背景
- 定义虚拟的三维物体，将其渲染
- 相机捕获的背景，不需要透视变换，正交变换即可
- 虚拟物体，需要*MVP（model transform, view transform, projection transform）*变换

- 世界坐标系重合，使用OpenCV去做

---

## 细节

两个世界的世界坐标系要完全重合，相机位置完全重合

> OpenGL中的世界坐标与虚拟物体局部坐标系的变换为Model Transform，世界坐标系与相机坐标系之间的变换为View Transform；
>
> OpenCV中的aruco，将世界坐标系注册在marker上；**Model Transform 保持不变（即虚拟物体的位置保持不变）** 

*OpenGL* 的摄像机需要实时改变自身位置与姿态，数据来自于*opencv* 相机标定~~外参~~ 

> 相机标定完成后，得到的是相机内参与畸变参数，对于特定的相机来说，都是不变量，因此，相机标定只需要一次即可
>
> **通过aruco的marker，获得相机的外参（重点），并实时传入OpenGL的View Transform矩阵** 
>
> **相机内参的作用：用来构造Projection Transform** 

*OpenGL* 中的背景图也需要实时改变

> 将OpenCV捕获的图像，实时正交投影至背景

---

## 遗留问题

- 虚拟模型没有出现在规定的世界原点处；

- ~~如何将三维模型直接导入OpenGL，而不是一个一个的定义顶点坐标~~ 

  > 解决方法：导入Assimp库，Assimp能够导入很多种不同的模型文件格式（并也能够导出部分的格式），它会将所有的模型数据加载至Assimp的通用数据结构中。当Assimp加载完模型之后，我们就能够从Assimp的数据结构中提取我们所需的所有数据了

---

## 效果

![](https://cdn.jsdelivr.net/gh/junlin49/up/20200913163957.png)

