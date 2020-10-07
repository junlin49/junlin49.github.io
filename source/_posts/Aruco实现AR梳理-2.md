---
title: Aruco实现AR梳理_2
date: 2020-09-21 07:45:19
categories: Aruco
tags:
- Aruco
---

# Aruco 实现AR过程梳理_2_

## 概述

[上一篇](https://junlu.club/2020/09/14/Aruco%E5%AE%9E%E7%8E%B0AR%E6%A2%B3%E7%90%86/)说的比较不系统，而且有一些问题没有说清楚，这一篇将系统的整理一下OpenCV，OpenGL，Aruco的具体分工，以及一些疑问。

## 目标效果

![](/iframe/aruco04.gif)

## 思路

和之前的一篇一样

> **OpenGL的世界中有虚拟相机，现实世界中有真实相机，只要将两个世界的世界坐标重合，相机位置重合，分别渲染虚拟模型与真实场景，即可达到AR效果**

## 细节

### OpenCV与Aruco

- 将真实相机捕获的画面，传递给OpenGL，用作背景
- 相机标定，将真实相机的内参标定出来

### OpenGL

- 将真实画面，正交投影至矩形中，充当背景
- 定义虚拟的Cube顶点
- 生成OpenGL窗口
- Cube经历的变换为 *MVP（model transform, view transform, projection transform）*变换

### 其他问题

- 如何将真实的世界坐标系与OpenGL的世界坐标系重合呢？如何将相机位置重合呢？

  OpenGL中，相机的位置是固定不变的，我们看到的永远是相机看到的，所以，只要清楚相机与世界坐标系的转换矩阵，即View Matrix，就可以将世界坐标系重合了。

- Aruco的作用是什么呢？

  获取真实相机相对于Marker的位姿（相机外参），传给OpenGL