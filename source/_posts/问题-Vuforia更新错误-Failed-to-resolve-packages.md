---
title: '问题-Vuforia更新错误:Failed to resolve packages'
date: 2020-04-02 18:27:12
tags:
- 问题
---

新冠期间总是不开学，最近Vuforia更新了9.0版本，所以尝试下

<!--more-->

## 问题

错误提示：

Failed to resolve packages: Cannot read property 'scopedRegistries' of undefined. No packages loaded.

如果使用 Unity 2019及以上版本，可以发现 AR camera 的输入密钥页面，已经提示有新版本可用，如下：

[![G3Lmzn.png](https://s1.ax1x.com/2020/04/01/G3Lmzn.png)](https://imgchr.com/i/G3Lmzn)

[![G3LZGj.png](https://s1.ax1x.com/2020/04/01/G3LZGj.png)](https://imgchr.com/i/G3LZGj)

点击后，直接跳转到此界面：

 https://library.vuforia.com/content/vuforia-library/en/articles/Solution/vuforia-engine-package-hosting-for-unity.html

第一种方式最简单，直接下载 SDK 升级即可，但是博主下载导入到 Unity 后，发现不仅没有升级，还出现了很多错误，而且在package manager 界面上，完全没有内容，似乎有文件找不到

[![G3LViQ.png](https://s1.ax1x.com/2020/04/01/G3LViQ.png)](https://imgchr.com/i/G3LViQ)

错误提示：

Failed to resolve packages: Cannot read property 'scopedRegistries' of undefined. No packages loaded.

Cannot perform upm operation: Cannot read property 'scopedRegistries' of undefined [NotFound]
UnityEditor.EditorApplication:Internal_CallUpdateFunctions()

Error fetching package list.
UnityEditor.EditorApplication:Internal_CallUpdateFunctions()

## 解决方法

按照官方链接的最后一个方法，下载 Vuforia Engine软件包，解压后将原名为*package*的文件夹，改名为*com.ptc.vuforia.engine*，放进项目对应的 package 目录，即可发现 Unity 界面已经开始导入。

[![G3L5TS.png](https://s1.ax1x.com/2020/04/01/G3L5TS.png)](https://imgchr.com/i/G3L5TS)

## 后续问题

~~发现不能直接拖拽 .unitypackage 文件进行导入，只能通过右键菜单里的导入进行操作~~

