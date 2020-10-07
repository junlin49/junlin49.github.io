---
title: MarkerLess AR Cube Demo 总结
date: 2020-10-07 19:18:57
cover: https://gitee.com/JunLuJun/up/raw/master/20200927093140.jpg
tags:
---

# MarkerLess AR Cube Demo 总结

> 参考文章：
>
> https://blog.csdn.net/qq_33446100/article/details/98848510

## 效果

![](/iframe/aruco06.gif)

## 前言

根据[ARUCO实现AR梳理-2](https://junlu.club/2020/09/21/Aruco%E5%AE%9E%E7%8E%B0AR%E6%A2%B3%E7%90%86-2/)中的叙述，可以知道，以OpenGL为最后显示平台的AR实现方案，最终要考虑的问题是，**如何获得相机的外部参数？**

如果知道了相机的外部参数，即可将其带入**View Matrix**；加上**Model Matrix**可以人为指定，**Projection Matrix**是由相机内参定义的，一切将水到渠成。

## Markerless

AR可以分为基于标志物的AR，与基于无标志物的AR，基于无标志物的AR又可以分为很多种，本文实现的是利用RGB单目摄像头，利用自然特征实现AR。

## 实现流程

1. 使用**SURF**（SpeedUp Robust Features 加速版的具有鲁棒性的特征）特征点检测算法检测事先准备的图像的特征点
2. 使用**SURF**特征点检测算法，检测相机拍到的实时图像
3. 使用**FLANN**（Fast Library for Approximate Nearest Neighbors 快速最近邻逼近搜索函数库）特征点匹配算法，将事先准备好的图像的特征点，与相机实时捕获的图像特征点作匹配
4. 根据**FLANN**，使用**findHomography**函数，得出单应性矩阵，将事先准备好的图像的四个角点，映射到相机捕获的图像上
5. 使用**SolvePnP**函数估计相机位姿，即相机外参
6. 代入**View Matrix**



