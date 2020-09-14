---
title: ' Aruco创建Board'
date: 2020-08-22 11:35:00
tags:
- Aruco
---

# 

> 参考链接：
>
> https://blog.csdn.net/qq_33446100/article/details/89186826

<!--more-->

## Board作用

- board是一个aruco marker的棋盘，上面布置了许多marker。

- board可用于校准（Calibrate）摄像机 

## Code

```c++
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
	imageSize.height =
		markersY * (markerLength + markerSeparation) - markerSeparation + 2 * margins;
 
	Ptr<aruco::Dictionary> dictionary =
		aruco::getPredefinedDictionary(aruco::PREDEFINED_DICTIONARY_NAME(dictionaryId));
 
	Ptr<aruco::GridBoard> board = aruco::GridBoard::create(markersX, markersY, float(markerLength),
		float(markerSeparation), dictionary);
 
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

## 运行结果

![](https://cdn.jsdelivr.net/gh/junlin49/up/20200822115151.png)

