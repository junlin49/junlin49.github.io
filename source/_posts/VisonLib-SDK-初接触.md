---
title: VisonLib SDK 初接触
date: 2020-05-18 19:38:01
tags:
- VisonLib
---

AR质检方案

<!--more-->

## 接触VisonLib SDK

### 引言

由于毕设需求，在网上寻找能够解决质量检测或者测量方面的*AR SDK*，找到了这篇报道：[VisionLib推跨平台AR质检解决方案，可结合CAD对比检测](https://www.toutiao.com/a6674774916132241931/)

所以开始准备尝试下

### 准备工作

1. 进[官网](https://visionlib.com/#sect-6)
2. 注册申请使用*SDK*（会有人员审核，我是隔一天以后收到的登录密码）
3. 等待邮件通知审核通过，复制账号密码
4. 登录官网

6. 直接下载*vlUnitySDK v20.3.1 Gengral Release*
7. 网页页面拉到底，下载许可证文件

![](https://i.loli.net/2020/05/18/7Nf4r9JIo6HvBKi.png)

8. 新建Unity 项目
9. 导入下载好的*package*

![image-20200518191431453](C:\Users\VR5\AppData\Roaming\Typora\typora-user-images\image-20200518191431453.png)

### 配置工作

1. 进入*StreamingAssets->VisionLib*目录，将下载的许可证文件（*license.xml*）复制进此目录下
2. 将电脑接上摄像头

### 使用

官方demo使用的是折纸折起来的各种汽车，官方资源链接在此 [Resources](https://visionlib.com/customer-area/resources/)

使用的话可以查看*SDK*本身自带的*demo*

由于后续写的文章，没有拍摄图片下来，希望各位自己做一下

### 感受

感觉和*Vuforia*功能上差别不大，*model target*（识别实物）的功能，这个*SDK*支持多个识别，*Vuforia*试过不可以。

另外，识别过程中的官方水印也太强了，真的是。。。。

