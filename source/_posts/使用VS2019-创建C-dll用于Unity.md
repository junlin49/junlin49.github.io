---
title: 使用VS2019 创建C++ dll用于Unity
date: 2020-08-12 15:11:51
tags:
- Unity
- dll
---

> 参考博客：
>
> https://www.cnblogs.com/aWxvdmVseXc0/p/12857974.html
>
> https://blog.csdn.net/zhunju0089/article/details/103458855

<!--more-->

### 创建VS项目

![](https://cdn.jsdelivr.net/gh/junlin49/up/20200812145559.png)

![](https://cdn.jsdelivr.net/gh/junlin49/up/20200812145630.png)

### 创建项目成功后，打开**pch.h**文件，定义导出函数的定义

<img src="https://cdn.jsdelivr.net/gh/junlin49/up/20200812145856.png" style="zoom:200%;" />

```c++
extern "C" _declspec(dllexport) int myadd(int a,int b);
```



### 打开**pch.cpp**文件，实现上述函数的具体功能

<img src="https://cdn.jsdelivr.net/gh/junlin49/up/20200812150216.png" style="zoom:200%;" />

```c++
#include <iostream>
using namespace std;
int myadd(int a,int b)
{
	return a+b;
}
```



### 点击**生成解决方案**，即可生成dll文件



### 在Unity项目中，新建文件夹**Plugins**，将生成的dll文件复制进此文件夹中

![](https://cdn.jsdelivr.net/gh/junlin49/up/20200812150544.png)

### 脚本调用

![](https://cdn.jsdelivr.net/gh/junlin49/up/20200812150843.png)



<img src="https://cdn.jsdelivr.net/gh/junlin49/up/20200812150927.png" style="zoom:200%;" />