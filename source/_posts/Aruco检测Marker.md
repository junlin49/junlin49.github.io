---
title: ' Aruco检测Marker'
date: 2020-08-22 11:29:21
tags:
- Aruco
---

# 

> 参考链接：
>
> https://blog.csdn.net/qq_33446100/article/details/89115983
>
> https://docs.opencv.org/4.0.1/d9/d6a/group__aruco.html

<!--more-->

## 设备

- Azure Kinect

  使用RGB相机，Kinect不接电源

- VS2019

## Code

```c++
#include <opencv2\highgui.hpp>
#include <opencv2\aruco.hpp>
#include <opencv2\aruco\dictionary.hpp>
#include <opencv2\core.hpp>
#include <vector>
#include <iostream>

using namespace std;
using namespace cv;

int main()
{
	cv::Mat markerImage;
	cv::VideoCapture mVideoCapture(0);

	//创建字典，这里注意使用Ptr<>，不然无法显示结果
	Ptr<aruco::Dictionary> dictionary = cv::aruco::getPredefinedDictionary(cv::aruco::DICT_4X4_50);
	cv::aruco::drawMarker(dictionary, 23, 240, markerImage);//这里取出一个marker用于检测

	imshow("marker", markerImage);//显示待检测标记

	while (mVideoCapture.grab())//取得下一帧
	{
		cv::Mat frame, frame_show;
		mVideoCapture.retrieve(frame);//放入Mat

		frame.copyTo(frame_show);//复制一份

		std::vector<int> ids;
		std::vector<std::vector<cv::Point2f>> corners;

		cv::aruco::detectMarkers(frame, dictionary, corners, ids);//检测该帧是否有标记

		if (ids.size() > 0)
			cv::aruco::drawDetectedMarkers(frame_show, corners, ids);//如果有，则标记出来，放入另一个Mat

		cv::imshow("video", frame_show);//显示结果

		waitKey(30);
	}

	return 0;
}
```

## 运行结果

![](https://cdn.jsdelivr.net/gh/junlin49/up/20200822112653.png)

![](https://cdn.jsdelivr.net/gh/junlin49/up/20200822112801.png)