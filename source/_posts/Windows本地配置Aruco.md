---
title: ' Windows本地配置Aruco'
date: 2020-08-20 16:15:16
tags:
- Aruco
---

> 环境：win10 + VS2019 + OpenCV4.30

<!-- more -->

## 下载Contrib库

传送门：[click it](https://github.com/opencv/opencv_contrib/releases)

(**注意**：选择与OpenCV对应的库)

![](https://cdn.jsdelivr.net/gh/junlin49/up/20200820155046.png)

## CMake编译OpecCV

![](https://cdn.jsdelivr.net/gh/junlin49/up/20200820155137.png)

点击*configure*，如果有红色，继续点*configure*，直至红色都消失

## CMake编译OpenCV-contrib

- 将*opencv_contrib-4.3.0*放置在*F:\DownLoad\opencv*目录下，在CMake中找到*OPENCV_EXTRA_MODULES_PATH*，填入*F:\DownLoad\opencv\opencv_contrib-4.3.0\modules*路径，并勾选上方*OPENCV_ENABLE_NONFREE*；

![](https://cdn.jsdelivr.net/gh/junlin49/up/20200820155702.png)

点击*configure*，如果有红色，继续点*configure*，直至红色都消失

最后点击*generate*

## VS2019编译debug与release库

打开OpenCV.sln项目文件；

### 编译debug库

解决方案 -> 右键重新生成解决方案

完成后，找到CMakeTargets中的INSTALL，然后右键选择“仅限于项目”-->“仅生成INSTALL”；

### 编译release库

和上面差不多

## 配置环境变量

系统变量 -> PATH ，添加 F:\DownLoad\MyBuild\install\x64\vc16\bin

![](https://cdn.jsdelivr.net/gh/junlin49/up/20200820160215.png)

用户变量：

变量名：OPENCV

变量值：F:\DownLoad\MyBuild\install\include

![](https://cdn.jsdelivr.net/gh/junlin49/up/20200820160401.png)

## 配置目录

与配置OpenCV相同，新建一个项目，新建一个属性表，为日后重复使用奠定基础

![](https://cdn.jsdelivr.net/gh/junlin49/up/20200820160634.png)

属性 -> VC++目录 -> 包含目录，添加：

```
F:\DownLoad\MyBuild\install\include
F:\DownLoad\MyBuild\install\include\opencv2
```

VC++目录 —> 库目录，添加：

```
F:\DownLoad\MyBuild\install\x64\vc16\lib
```

链接器 -> 输入 -> 附加依赖项 ，添加以下

```
opencv_aruco430d.lib;
opencv_bgsegm430d.lib;
opencv_bioinspired430d.lib;
opencv_calib3d430d.lib;
opencv_ccalib430d.lib;
opencv_core430d.lib;
opencv_datasets430d.lib;
opencv_dnn_objdetect430d.lib;
opencv_dnn430d.lib;
opencv_dpm430d.lib;
opencv_face430d.lib;
opencv_features2d430d.lib;
opencv_flann430d.lib;
opencv_fuzzy430d.lib;
opencv_hdf430d.lib;
opencv_hfs430d.lib;
opencv_highgui430d.lib;
opencv_img_hash430d.lib;
opencv_imgcodecs430d.lib;
opencv_imgproc430d.lib;
opencv_line_descriptor430d.lib;
opencv_ml430d.lib;
opencv_objdetect430d.lib;
opencv_optflow430d.lib;
opencv_phase_unwrapping430d.lib;
opencv_photo430d.lib;
opencv_plot430d.lib;
opencv_reg430d.lib;
opencv_rgbd430d.lib;
opencv_saliency430d.lib;
opencv_shape430d.lib;
opencv_stereo430d.lib;
opencv_stitching430d.lib;
opencv_structured_light430d.lib;
opencv_superres430d.lib;
opencv_surface_matching430d.lib;
opencv_text430d.lib;
opencv_tracking430d.lib;
opencv_video430d.lib;
opencv_videoio430d.lib;
opencv_videostab430d.lib;
opencv_xfeatures2d430d.lib;
opencv_ximgproc430d.lib;
opencv_xobjdetect430d.lib;
opencv_xphoto430d.lib;
```

## 测试 - 生成Aruco_board

```
#include <opencv2\highgui.hpp>
#include <opencv2\aruco.hpp>
#include <opencv2\aruco\dictionary.hpp>
#include <opencv2\aruco\charuco.hpp>
#include <opencv2\core.hpp>
#include <opencv2\imgproc\imgproc.hpp>
#include <opencv2\opencv.hpp>
#include <vector>
#include <iostream>

using namespace std;
using namespace cv;

int main()
{
	int markersX = 5;//X轴上标记的数量
	int markersY = 5;//Y轴上标记的数量   本例生成5x5的棋盘
	int markerLength = 100;//标记的长度，单位是像素
	int markerSeparation = 20;//每个标记之间的间隔，单位像素
	int dictionaryId = cv::aruco::DICT_4X4_50;//生成标记的字典ID
	int margins = markerSeparation;//标记与边界之间的间隔

	int borderBits = 1;//标记的边界所占的bit位数
	bool showImage = true;

	Size imageSize;

	imageSize.width = markersX * (markerLength + markerSeparation) - markerSeparation + 2 * margins;

	imageSize.height = markersY * (markerLength + markerSeparation) - markerSeparation + 2 * margins;

	Ptr<aruco::Dictionary> dictionary = aruco::getPredefinedDictionary(aruco::PREDEFINED_DICTIONARY_NAME(dictionaryId));

	Ptr<aruco::GridBoard> board = aruco::GridBoard::create(markersX, markersY, float(markerLength), float(markerSeparation), dictionary);

	// show created board
	Mat boardImage;
	board->draw(imageSize, boardImage, margins, borderBits);

	if (showImage) {
		imshow("board", boardImage);
		waitKey(0);
	}
	return 0;

}
```



![](https://cdn.jsdelivr.net/gh/junlin49/up/20200820154403.png)