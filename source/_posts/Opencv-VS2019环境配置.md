---
title: Opencv-VS2019环境配置
date: 2020-04-02 18:21:55
tags:
- OpenCV
- VS2019
---

本文记录Win10操作系统下，Opencv+VS2019的环境配置，具体Opencv版本为4.1.0

<!--more-->

### 下载安装包

Opencv 官网下载链接：[传送门](https://opencv.org/releases/)

### 配置环境变量

1. 下载好的文件解压到合适的目录下，本文以`D:\OpenCV`目录放置解压文件，如下图

   [![8sQycT.md.png](https://s1.ax1x.com/2020/03/19/8sQycT.md.png)](https://imgchr.com/i/8sQycT)

2. 打开资源管理器窗口，在`此电脑`上右击，点击`属性`，弹出`控制面板中的系统窗口`，点击`高级系统设置`，弹出`系统属性窗口`，点击`环境变量`

3. 在窗口上方的`用户变量`中，选中Path变量，点击下方编辑，在编辑系统变量窗口中，新增以下变量：

   ```
   D:\OpenCV\opencv\build\x64\vc15\bin
   ```

4. 连续点击3个确定，关闭系统窗口

### 配置VS

1. 打开VS2019，选择创建新项目

   [![8slUxK.md.png](https://s1.ax1x.com/2020/03/19/8slUxK.md.png)](https://imgchr.com/i/8slUxK)

2. 在弹出的创建新项目面板中，选择C++语言的空项目，点击右下方的下一步

   [![8sQs3V.md.png](https://s1.ax1x.com/2020/03/19/8sQs3V.md.png)](https://imgchr.com/i/8sQs3V)

3. 结合自身情况，选择储存位置，点击创建

4. 在新打开的VS界面中，找到右侧`解决方案资源管理器`，右键单击【源文件】->【添加】->【新建项】，新建一个命名为“main”的`C++文件`

5. 点开【视图】->【其他窗口】->【属性管理器】，打开属性管理器面板

6. 调试模式改为`Debug x64`模式。

7. 找到Opencv的安装目录`D:\OpenCV\opencv\build\x64\vc15\bin`，将如下图所示的三个文件（dll文件），复制粘贴到系统路径`C:\Windows\System32`文件夹内。

   [![8sQ0Nn.md.png](https://s1.ax1x.com/2020/03/19/8sQ0Nn.md.png)](https://imgchr.com/i/8sQ0Nn)

8. 接下来，开始为软件配置目录和附加项。右键点击【属性管理器】中的【Debug | x64】选项，选择【添加新的项目属性表】

9. 将新建的项目属性表重命名为`Opencv410Debugx64`，之后双击打开，选中【VC++ 目录】->【包含目录】，新增以下路径：

   ```
   D:\OpenCV\opencv\build\include
   D:\OpenCV\opencv\build\include\opencv2
   ```

10. 在【库目录】中添加以下路径：

    ```
    D:\OpenCV\opencv\build\x64\vc15\lib
    ```

11. 打开【链接器】->【输入】->【附加依赖项】，添加以下名称：

    ```
    opencv_world410d.lib
    ```

    PS：opencv_world410d.lib 是指 Debug 编辑模式，opencv_world410.lib 是指Relsase 编辑模式。

### 程序测试

在main.cpp中粘贴以下程序：

```
#include "opencv.hpp" //opencv 的头文件
using namespace std;
using namespace cv; //opencv 的命名空间

int main()
{
	printf("Hello Open CV!");
	Mat srcImage = imread("1.jpg");	//图片需要在工程目录下
	Mat temImage, dstImage1;
	temImage = srcImage;
	//尺寸调整
	resize(temImage, dstImage1, Size(0, 0), 0.25, 0.25, INTER_LINEAR);     //长宽缩小1/4
	imshow("缩小", dstImage1);
	waitKey();
	return 0;
}
```

### 后续工作

调试时如果在下方出现 `xxx符号在加载`，并且很久都没有出现图片，需要做以下工作：

进入【工具】->【选项】->【调试】->【符号】，看右边有个`Microsoft符号服务器`，将前面的勾去掉

### 参考资料

> https://blog.csdn.net/qingzai_/article/details/73838709
>
> https://blog.csdn.net/qq_24912401/article/details/90475290?utm_source=app