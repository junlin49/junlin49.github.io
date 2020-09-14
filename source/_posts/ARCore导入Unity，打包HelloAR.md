---
title: ARCore导入Unity，打包HelloAR
date: 2020-04-03 08:21:18
tags:
- ARCore
- HelloAR
---

记录第一次使用ARCore的报错经历

<!--more-->

### 流程

#### 前提要求

硬件：[支持ARCore的手机](https://developers.google.com/ar/discover/supported-devices)

软件：

- Unity2017.4.9f1及以上版本
- ARCore SDK for Unity 1.5.0 或更高版本
- 使用[Android Studio](https://developer.android.com/studio) 中的 SDK 管理器安装的 Android SDK **7.0（API 级别 24）或更高版本**

#### 第一步：下载SDK包

下载 [ARCore SDK for Unity **1.5.0 或更高版本**](https://github.com/google-ar/arcore-unity-sdk/releases)，下载的 SDK 文件名为 `arcore-unity-sdk-v1.5.0.unitypackage`。

#### 第二步：新建项目并导入SDK

1.  打开 Unity 并新建一个 **3D** 项目。
2. 选择 **Assets > Import Package > Custom Package**。
3. 选择您上面下载的 `arcore-unity-sdk-v1.5.0.unitypackage` Unity 软件包。
4. 在 **Importing Package** 对话框中，确保已选择所有软件包选项，并点击 **Import**。
5. 出现提示时接受任何 [API 升级](https://docs.unity3d.com/Manual/APIUpdater.html)。

#### 第三步：配置

1. 转到 **File > Build Settings** 以打开 **[Build Settings](https://docs.unity3d.com/Manual/BuildSettings.html)** 窗口。

2. 选择 **Android** 并点击 **Switch Platform**。

3. 在 **Build Settings** 窗口中，点击 **Player Settings**。

4. 在Inspector窗口中，配置下列播放器设置：

   | 设置                                                         | 值                                                           |
   | :----------------------------------------------------------- | :----------------------------------------------------------- |
   | **Player Settings** > **Other Settings** > **Package Name**  | 创建一个 Java 软件包名称格式的唯一应用 ID。 例如，使用 `com.example.helloAR` |
   | **Player Settings** > **Other Settings** > **Minimum API Level** | Android 7.0 或更高版本                                       |
   | **Player Settings** > **Other Settings** > **Target API Level** | ~~Android 7.0 或更高版本~~ 直接自动最高版本                  |
   | **Player Settings** > **XR Settings** > **ARCore Supported** | 启用                                                         |

#### 第四步：解决报错

导入SDK后，控制台报错如下：

> The type or namespace name 'SpatialTracking' does not exist in the namespace 'UnityEngine'

大概有17条错误

**解决方法：**

1. 打开windows -> package manger
2. 分别搜索 **Multiplayer HLAPI **和 **XR Legacy Input Helper **,安装即可

### 参考资料

> https://developers.google.com/ar/develop/unity/quickstart-android
>
> https://forum.unity.com/threads/he-type-or-namespace-name-spatialtracking-does-not-exist-in-the-namespace-unityengine-are-you-m.650662/