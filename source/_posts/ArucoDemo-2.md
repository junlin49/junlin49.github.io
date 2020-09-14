---
title: ArucoDemo-2
date: 2020-08-23 08:00:35
tags:
- Aruco
---

> 参考链接：
>
> https://blog.csdn.net/qq_33446100/article/details/95796560

<!--more-->

## 代码

```c++
#include <iostream>
#include <opencv2\aruco.hpp>
#include <opencv2\core.hpp>
#include <opencv2\opencv.hpp>
#include <opencv2\imgproc.hpp>
#include <vector>

using namespace std;
using namespace cv;

void drawCubeWireframe(
	cv::InputOutputArray image, cv::InputArray cameraMatrix,
	cv::InputArray distCoeffs, cv::InputArray rvec, cv::InputArray tvec,
	float l
);


int main(int argc, char** argv)
{
	int wait_time = 10;
	float actual_marker_l = 0.101; // this should be in meters

	VideoWriter writer("VideoTest.avi", VideoWriter::fourcc('M', 'J', 'P', 'G'), 20.0, Size(640, 480));

	cv::Mat image, image_copy;
	cv::Mat camera_matrix, dist_coeffs;
	std::ostringstream vector_to_marker;

	cv::VideoCapture in_video;
	in_video.open(0);
	cv::Ptr<cv::aruco::Dictionary> dictionary =
		cv::aruco::getPredefinedDictionary(aruco::PREDEFINED_DICTIONARY_NAME(0));

	cv::FileStorage fs("camera.yml", cv::FileStorage::READ);

	fs["camera_matrix"] >> camera_matrix;
	fs["distortion_coefficients"] >> dist_coeffs;

	std::cout << "camera_matrix\n"
		<< camera_matrix << std::endl;
	std::cout << "\ndist coeffs\n"
		<< dist_coeffs << std::endl;

	int frame_width = 480;
	int frame_height = 640;

	while (in_video.grab())
	{

		in_video.retrieve(image);
		image.copyTo(image_copy);
		std::vector<int> ids;
		std::vector<std::vector<cv::Point2f>> corners;
		cv::aruco::detectMarkers(image, dictionary, corners, ids);

		// if at least one marker detected
		if (ids.size() > 0)
		{
			cv::aruco::drawDetectedMarkers(image_copy, corners, ids);
			std::vector<cv::Vec3d> rvecs, tvecs;
			cv::aruco::estimatePoseSingleMarkers(
				corners, actual_marker_l, camera_matrix, dist_coeffs,
				rvecs, tvecs
			);

			// draw axis for each marker
			for (int i = 0; i < ids.size(); i++)
			{
				drawCubeWireframe(
					image_copy, camera_matrix, dist_coeffs, rvecs[i], tvecs[i],
					actual_marker_l
				);

				vector_to_marker.str(std::string());
				vector_to_marker << std::setprecision(4)
					<< "x: " << std::setw(8) << tvecs[0](0);
				cv::putText(image_copy, vector_to_marker.str(),
					Point(10, 30), cv::FONT_HERSHEY_SIMPLEX, 0.6,
					Scalar(0, 252, 124), 1);

				vector_to_marker.str(std::string());
				vector_to_marker << std::setprecision(4)
					<< "y: " << std::setw(8) << tvecs[0](1);
				cv::putText(image_copy, vector_to_marker.str(),
					Point(10, 50), cv::FONT_HERSHEY_SIMPLEX, 0.6,
					Scalar(0, 252, 124), 1);

				vector_to_marker.str(std::string());
				vector_to_marker << std::setprecision(4)
					<< "z: " << std::setw(8) << tvecs[0](2);
				cv::putText(image_copy, vector_to_marker.str(),
					Point(10, 70), cv::FONT_HERSHEY_SIMPLEX, 0.6,
					Scalar(0, 252, 124), 1);
			}
		}

		cv::imshow("Pose estimation", image_copy);

		writer << image_copy;

		char key = (char)cv::waitKey(wait_time);
		if (key == 'b')
			break;
	}

}

void drawCubeWireframe(
	cv::InputOutputArray image, cv::InputArray cameraMatrix,
	cv::InputArray distCoeffs, cv::InputArray rvec, cv::InputArray tvec,
	float l
)
{

	CV_Assert(
		image.getMat().total() != 0 &&
		(image.getMat().channels() == 1 || image.getMat().channels() == 3)
	);
	CV_Assert(l > 0);
	float half_l = l / 2.0;

	// project cube points
	std::vector<cv::Point3f> axisPoints;
	axisPoints.push_back(cv::Point3f(half_l, half_l, l));
	axisPoints.push_back(cv::Point3f(half_l, -half_l, l));
	axisPoints.push_back(cv::Point3f(-half_l, -half_l, l));
	axisPoints.push_back(cv::Point3f(-half_l, half_l, l));
	axisPoints.push_back(cv::Point3f(half_l, half_l, 0));
	axisPoints.push_back(cv::Point3f(half_l, -half_l, 0));
	axisPoints.push_back(cv::Point3f(-half_l, -half_l, 0));
	axisPoints.push_back(cv::Point3f(-half_l, half_l, 0));

	std::vector<cv::Point2f> imagePoints;
	projectPoints(
		axisPoints, rvec, tvec, cameraMatrix, distCoeffs, imagePoints
	);

	// draw cube edges lines
	cv::line(image, imagePoints[0], imagePoints[1], cv::Scalar(255, 0, 0), 3);
	cv::line(image, imagePoints[0], imagePoints[3], cv::Scalar(255, 0, 0), 3);
	cv::line(image, imagePoints[0], imagePoints[4], cv::Scalar(255, 0, 0), 3);
	cv::line(image, imagePoints[1], imagePoints[2], cv::Scalar(255, 0, 0), 3);
	cv::line(image, imagePoints[1], imagePoints[5], cv::Scalar(255, 0, 0), 3);
	cv::line(image, imagePoints[2], imagePoints[3], cv::Scalar(255, 0, 0), 3);
	cv::line(image, imagePoints[2], imagePoints[6], cv::Scalar(255, 0, 0), 3);
	cv::line(image, imagePoints[3], imagePoints[7], cv::Scalar(255, 0, 0), 3);
	cv::line(image, imagePoints[4], imagePoints[5], cv::Scalar(255, 0, 0), 3);
	cv::line(image, imagePoints[4], imagePoints[7], cv::Scalar(255, 0, 0), 3);
	cv::line(image, imagePoints[5], imagePoints[6], cv::Scalar(255, 0, 0), 3);
	cv::line(image, imagePoints[6], imagePoints[7], cv::Scalar(255, 0, 0), 3);
}


```

## 运行效果

![](https://cdn.jsdelivr.net/gh/junlin49/up/20200823075820.png)