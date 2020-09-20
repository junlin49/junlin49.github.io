---
title: 关于AR键盘
date: 2020-09-19 21:40:46
tags:
---

# AR Keyboard

> 参考链接：
>
> https://www.cnblogs.com/mantgh/p/5680580.html#other

## 概述

任何一套成熟的开发系统，都需要一整套开发套件，其中UI套件必不可少，其实用性，美观性关系着开发者的体验以及用户的体验。

目前，截至现在，Hololens2的开发工作，在Unity上的UI套件主要包括以下几种：



我有一个虚拟键盘的需求，类似于Hololens2的系统键盘那样，但是根据官方手册的指导，进展的并不顺利。



## 需求

在混合现实主页的模式（即全息三维模式）下，使用虚拟键盘输入文本，类似于Unity 2D模式下的InputFiled。

## 环境

- Unity
- MRTK

## 过程

1. 首先我想到的是查看官方手册，该如何调用系统键盘，能不能实现在应用内部调用系统键盘，从而实现上述需求

   [官方主页链接](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/README_SystemKeyboard.html)

   ![](https://gitee.com/JunLuJun/up/raw/master/20200919213126.png)

   官方链接里面内容说的很清楚，这里不再赘述，将代码列在下方。

   - how to invoke the system keyboard

     ```c#
     public TouchScreenKeyboard keyboard;
     
     ...
     
     public void OpenSystemKeyboard()
     {
         keyboard = TouchScreenKeyboard.Open("", TouchScreenKeyboardType.Default, false, false, false, false);
     }
     ```

   - how to read the input

     ```c#
     public TouchScreenKeyboard keyboard;
     
     ...
     
     private void Update()
     {
         if (keyboard != null)
         {
             keyboardText = keyboard.text;
             // Do stuff with keyboardText
         }
     }
     ```

   - Build的时候，尽量选择到处类型为XAML

2. 按照官方给的方法，实现的效果不好

   当让其显示虚拟键盘的时候，Hololens会直接退出混合现实主页视图，直接变成了进入应用之前的场景，然后出现虚拟键盘，与我的需求并不契合；输入完成后，按Enter键，Unity又会重新进入混合现实主页视图，此时，输入框中会有刚刚输入的文字

## 其他选择

- 自定义一个自己的呗
- 听写识别输入

