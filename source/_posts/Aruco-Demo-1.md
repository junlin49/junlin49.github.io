---
title: ' Aruco Demo_1'
date: 2020-08-22 19:39:27
tags:
- Aruco
---

> 参考链接：
>
> https://blog.csdn.net/qq_33446100/article/details/89209901

<!--more-->

## 简单介绍

Aruco Demo_1 是在**board**板上显示图片，在相机标定的基础上完成



## 代码

```c++
#include <opencv2\highgui.hpp>
#include <opencv2\videoio.hpp>
#include <opencv2\aruco.hpp>
#include <opencv2\aruco\dictionary.hpp>
#include <opencv2\aruco\charuco.hpp>
#include <opencv2\core.hpp>
#include <opencv2\imgproc\imgproc.hpp>
#include <opencv2\opencv.hpp>

using namespace std;
using namespace cv;
using namespace cv::aruco;

#define WINDOW_NAME "ARDemo"

Mat camMatrix, distCoeffs, srcImage;
float markerLength = 100;

static bool readCameraParameters(const string& filename, Mat& camMatrix, Mat& distCoeffs) {
	FileStorage fs(filename, FileStorage::READ);

	if (!fs.isOpened())
		return false;

	fs["camera_matrix"] >> camMatrix;
	fs["distortion_coefficients"] >> distCoeffs;

	return true;
}

void draw_image(Mat image, Mat& frame, Vec3d& rvec, Vec3d& tvec) {
	vector<Point3f> points;
	vector<Point2f> imagePoints;

	float l = markerLength * 0.5f;

	points.emplace_back(-l, l, 0);//确定每个marker对应的坐标系中四个顶点的位置
	points.emplace_back(l, l, 0);//这个坐标是上一篇中绘制出来的坐标轴中对应的坐标
	points.emplace_back(l, -l, 0);
	points.emplace_back(-l, -l, 0);

	//通过相机内参、外参将3d坐标映射到图像坐标系
	cv::projectPoints(points, rvec, tvec, camMatrix, distCoeffs, imagePoints);

	unsigned int x = (unsigned int)imagePoints[0].x;
	unsigned int y = (unsigned int)imagePoints[0].y;

	vector<Point2f> corners;
	corners.emplace_back(0, 0);
	corners.emplace_back(image.cols, 0);
	corners.emplace_back(image.cols, image.rows);
	corners.emplace_back(0, image.rows);

	//从四对对应点计算透视变换
	cv::Mat T = getPerspectiveTransform(corners, imagePoints);

	//将透视变换应用于image
	Mat warpedImg;
	cv::warpPerspective(image, warpedImg, T, frame.size());

	vector<Point2i> pts;

	for (auto i : imagePoints) {
		pts.emplace_back((int)i.x, (int)i.y);
	}

	//用全0填充凸多边形
	cv::fillConvexPoly(frame, pts, cv::Scalar::all(0), cv::LINE_AA);
	//或运算
	cv::bitwise_or(warpedImg, frame, frame);

	/*cout << points << endl;
	cout << imagePoints << endl;
	cout << corners << endl;
	cout << pts << endl;*/

	/*
	[-50, 50, 0;
	 50, 50, 0;
	 50, -50, 0;
	 -50, -50, 0]
	[98.558197, 147.89699;
	 131.39151, 168.23427;
	 108.73455, 203.80612;
	 75.317993, 184.06393]
	[0, 0;
	 100, 0;
	 100, 100;
	 0, 100]
	[98, 147;
	 131, 168;
	 108, 203;
	 75, 184]
	*/

	//waitKey();
}

int main(int argc, char* argv[]) {

	VideoCapture inputVideo(0);
	//VideoWriter writer("VideoTest.avi", VideoWriter::fourcc('M', 'J', 'P', 'G'), 25.0, Size(640, 480));

	Ptr<aruco::Dictionary> dictionary =
		aruco::getPredefinedDictionary(cv::aruco::DICT_4X4_50);

	bool readOk = readCameraParameters("camera.yml", camMatrix, distCoeffs);
	if (!readOk) {
		cerr << "Invalid camera file" << endl;
		return -1;
	}

	cout << camMatrix << endl;
	cout << distCoeffs << endl;

	srcImage = imread("image.png");
	resize(srcImage, srcImage, Size(100, 100));

	namedWindow(WINDOW_NAME);

	Mat frame;

	while (inputVideo.grab()) {
		inputVideo >> frame;

		vector<int> ids;
		vector<vector<Point2f>> corners;

		aruco::detectMarkers(frame, dictionary, corners, ids);

		if (ids.size() > 0) {
			vector< Vec3d > rvecs, tvecs;//得到旋转矢量以及平移矢量
			cv::aruco::estimatePoseSingleMarkers(corners, 100, camMatrix, distCoeffs, rvecs, tvecs);

			for (unsigned int i = 0; i < ids.size(); i++) {
				draw_image(srcImage, frame, rvecs[i], tvecs[i]);
			}

			//writer << frame;
		}
		cv::imshow(WINDOW_NAME, frame);

		char key = (char)waitKey(10);
		if (key == 'b')
			break;
	}
	inputVideo.release();
	return 0;
}
```

## 运行效果

![](https://cdn.jsdelivr.net/gh/junlin49/up/20200822193805.png)