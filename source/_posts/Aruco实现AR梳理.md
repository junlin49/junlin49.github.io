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

*OpenGL* 的摄像机需要实时改变自身位置与姿态，数据来自于*opencv* 相机标定外参

*OpenGL* 中的背景图也需要实时改变

---

## 遗留问题

- 虚拟模型没有出现在规定的世界原点处；

- ~~如何将三维模型直接导入OpenGL，而不是一个一个的定义顶点坐标~~ 

  > 解决方法：导入Assimp库，Assimp能够导入很多种不同的模型文件格式（并也能够导出部分的格式），它会将所有的模型数据加载至Assimp的通用数据结构中。当Assimp加载完模型之后，我们就能够从Assimp的数据结构中提取我们所需的所有数据了

---

## 效果

![](https://cdn.jsdelivr.net/gh/junlin49/up/20200913163957.png)

