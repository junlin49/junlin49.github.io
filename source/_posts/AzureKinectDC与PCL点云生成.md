---
title: AzureKinectDK与PCL点云生成
date: 2020-10-08 09:42:39
cover: https://gitee.com/JunLuJun/up/raw/master/20201008095341.jpg
tags:
- pcl
---

# AzureKinectDK与PCL点云生成

> 参考博文：
>
> https://blog.csdn.net/weixin_39746114/article/details/107835727

## 效果

![](https://gitee.com/JunLuJun/up/raw/master/20201008095211.jpg)

![](https://gitee.com/JunLuJun/up/raw/master/20201008095233.jpg)

## AzureKinectDK

### 简介

<img src="https://gitee.com/JunLuJun/up/raw/master/20201008094515.png" style="zoom:50%;" />

Azure Kinect DK 是一款开发人员套件和 PC 外部设备，它搭载 100 万像素高级深度摄像头、360˚ 麦克风阵列，1200万像素全高清摄像头和方向传感器，适用于先进的计算机视觉和语音模型开发。它的长度不到13厘米，厚度仅为4厘米。

系统要求：采用第7代 Intel® CoreTM i3 处理器（双核 2.4GHz 包含 HD620 GPU 或更快）的 Windows 10® 计算机、USB 3.0 端口、4GB 内存。不支持 Windows 10 S 模式。人体跟踪等其他功能可能需要更高级的 PC 硬件。

## 组成

![](https://gitee.com/JunLuJun/up/raw/master/20201008094609.jpg)

1. 100 万像素深度传感器，具有宽、窄视场角 (FOV) 选项，可针对应用程序进行优化
2. 7 麦克风阵列，可用于远场语音和声音捕获
3. 1200 万像素 RGB 摄像头，提供和深度数据匹配的彩色图像数据流
4. 加速计和陀螺仪 (IMU)，可用于传感器方向和空间跟踪
5. 外部同步引脚，可轻松同步多个 Kinect 设备的传感器数据流

## 代码

1. main.cpp

    ```c++
    #include <k4a/k4a.hpp>
    
    #include <iostream>
    #include <vector>
    #include <array>
    #include <conio.h>
    
    #include <Eigen/Dense>
    
    #include <pcl/point_cloud.h>
    #include <pcl/point_types.h>
    #include<pcl/visualization/cloud_viewer.h>
    #include <pcl/visualization/pcl_visualizer.h>
    #include <boost/make_shared.hpp>
    #include<boost/thread.hpp>
    #include "k4a_grabber.h"
    
    #include <pcl/filters/passthrough.h>
    
    
    using namespace std;
    using namespace boost;
    using namespace pcl;
    
    typedef pcl::PointXYZRGBA PointType;
    
    void viewerOneOff(visualization::PCLVisualizer& viewer)
    {
    	viewer.setBackgroundColor(0, 0, 0);   //设置背景颜色为黑色
    }
    
    
    int main()
    {
    	const uint32_t deviceCount = k4a::device::get_installed_count();
    	if (deviceCount == 0)
    	{
    		cout << "no azure kinect devices detected!" << endl;
    	}
    
    	// PCL Visualizer
    	boost::shared_ptr<pcl::visualization::PCLVisualizer> viewer(new pcl::visualization::PCLVisualizer("Point Cloud Viewer"));
    
    	//定义初始点云
    	pcl::PointCloud<pcl::PointXYZRGBA>::Ptr cloud(new pcl::PointCloud<pcl::PointXYZRGBA>);
    
    	// 回调函数
    	boost::mutex mutex;
    	boost::function<void(const pcl::PointCloud<PointType>::ConstPtr&)> function =
    		[&cloud, &mutex](const pcl::PointCloud<PointType>::ConstPtr& ptr)
    	{
    		boost::mutex::scoped_lock lock(mutex);
    		//-----------------------------------------
    		/* Point Cloud Processing */
    		//-----------------------------------------
    		cloud = ptr->makeShared();
    	};
    
    	// 定义grabber
    	boost::shared_ptr<pcl::Grabber> grabber =
    		boost::make_shared<pcl::KinectAzureDKGrabber>(0, K4A_DEPTH_MODE_WFOV_2X2BINNED, K4A_IMAGE_FORMAT_COLOR_BGRA32, K4A_COLOR_RESOLUTION_720P);
    	//强制类型转换
    	boost::shared_ptr<pcl::KinectAzureDKGrabber> grabber_ = boost::dynamic_pointer_cast<pcl::KinectAzureDKGrabber>(grabber);
    	//注册回调函数
    	boost::signals2::connection connection = grabber->registerCallback(function);
    	//开始
    	grabber->start();
    
    	k4a::calibration calibration = grabber_->getCalibration();
    	k4a_calibration_intrinsic_parameters_t* intrinsics = &calibration.color_camera_calibration.intrinsics.parameters;
    	Eigen::Matrix3f intrinsics_eigen;
    	intrinsics_eigen <<
    		intrinsics->param.fx, 0.0f, intrinsics->param.cx,
    		0.0f, intrinsics->param.fy, intrinsics->param.cy,
    		0.0f, 0.0f, 1.0f;
    	/*Eigen::Matrix4f extrinsics_eigen = Eigen::Matrix4f::Identity();*/
    	Eigen::Matrix4f extrinsics_eigen;
    	extrinsics_eigen << -1.0f, 0.0f, 0.0f, 0.0f,
    		0.0f, -1.0f, 0.0f, 0.0f,
    		0.0f, 0.0f, 1.0f, 0.0f,
    		0.0f, 0.0f, 0.0f, 1.0f; //看起来方向是正确的 
    	viewer->setCameraParameters(intrinsics_eigen, extrinsics_eigen);
    
    	//显示采集到的点云数据
    	while (!viewer->wasStopped())
    	{
    		// Update Viewer
    		viewer->spinOnce();
    		boost::mutex::scoped_try_lock lock(mutex);
    		if (lock.owns_lock() && cloud)
    		{
    			//pcl::PassThrough<pcl::PointXYZRGBA> pass;
    
    			//pass.setInputCloud(cloud);            //设置输入点云
    			//pass.setFilterFieldName("z");         //设置过滤时所需要点云类型的Z字段
    			//pass.setFilterLimits(0.5, 1.08);        //设置在过滤字段的范围
    			//pass.setFilterLimitsNegative(true);   //设置保留范围内还是过滤掉范围内
    			//pass.filter(*cloud);            //执行滤波，保存过滤结果在cloud_filtered
    
    			// Update Point Cloud
    			if (!viewer->updatePointCloud(cloud, "cloud"))
    			{
    				viewer->addPointCloud(cloud, "cloud");
    			}
    		}
    
    		if (_kbhit())
    		{
    			grabber->stop();
    		}
    	}
    
    
    	// Stop Grabber
    	
    
    	pcl::visualization::CloudViewer pviewer("Cloud Viewer");     //创建viewer对象
    	pviewer.showCloud(cloud);
    	pviewer.runOnVisualizationThreadOnce(viewerOneOff);
    
    
    
    	// Disconnect Callback Function
    	if (connection.connected())
    	{
    		connection.disconnect();
    	}
    	std::system("pause");
    	return 0;
    }
    ```

2. k4a_grabber.h

    ```c++
    #pragma once
    #include <k4a/k4a.hpp>
    
    #include <pcl/io/boost.h>
    #include <pcl/io/grabber.h>
    #include <pcl/point_cloud.h>
    #include <pcl/point_types.h>
    #include <boost/thread/thread.hpp>
    
    namespace pcl
    {
    	struct pcl::PointXYZ;
    	struct pcl::PointXYZI;
    	struct pcl::PointXYZRGB;
    	struct pcl::PointXYZRGBA;
    	template <typename T> class pcl::PointCloud;
    
    	class KinectAzureDKGrabber : public pcl::Grabber
    	{
    	public:
    		KinectAzureDKGrabber(const int& device_id_, const int& depth_mode_, const int& color_format_, const int& color_resolution_);
    		virtual ~KinectAzureDKGrabber() throw ();
    		virtual void start();
    		virtual void stop();
    		virtual bool isRunning() const;
    		virtual std::string getName() const;
    		virtual float getFramesPerSecond() const;
    
    		typedef void (signal_KinectAzureDK_PointXYZ)(const std::shared_ptr<const pcl::PointCloud<pcl::PointXYZ>>&);
    		typedef void (signal_KinectAzureDK_PointXYZI)(const std::shared_ptr<const pcl::PointCloud<pcl::PointXYZI>>&);
    		typedef void (signal_KinectAzureDK_PointXYZRGB)(const std::shared_ptr<const pcl::PointCloud<pcl::PointXYZRGB>>&);
    		typedef void (signal_KinectAzureDK_PointXYZRGBA)(const std::shared_ptr<const pcl::PointCloud<pcl::PointXYZRGBA>>&);
    
    	protected:
    		void setupDevice(const int& device_id_, const int& depth_mode_, const int& color_format_, const int& color_resolution_);
    
    		boost::signals2::signal<signal_KinectAzureDK_PointXYZ>* signal_PointXYZ;
    		boost::signals2::signal<signal_KinectAzureDK_PointXYZI>* signal_PointXYZI;
    		boost::signals2::signal<signal_KinectAzureDK_PointXYZRGB>* signal_PointXYZRGB;
    		boost::signals2::signal<signal_KinectAzureDK_PointXYZRGBA>* signal_PointXYZRGBA;
    
    		pcl::PointCloud<pcl::PointXYZ>::Ptr convertDepthToPointXYZ();
    		pcl::PointCloud<pcl::PointXYZI>::Ptr convertInfraredDepthToPointXYZI();
    		pcl::PointCloud<pcl::PointXYZRGB>::Ptr convertRGBDepthToPointXYZRGB();
    		pcl::PointCloud<pcl::PointXYZRGBA>::Ptr convertRGBADepthToPointXYZRGBA();
    
    		std::thread thread;
    		mutable std::mutex mutex;
    
    		void threadFunction();
    
    		bool quit;
    		bool running;
    
    		k4a_device_configuration_t config;
    		k4a::device dev;
    		int device_id;
    
    		k4a::calibration calibration;
    		k4a::transformation transformation;
    
    		int colorWidth;
    		int colorHeight;
    		k4a::image colorImage;
    
    		int depthWidth;
    		int depthHeight;
    		k4a::image depthImage;
    
    		int infraredWidth;
    		int infraredHeight;
    		k4a::image infraredImage;
    	public:
    
    		k4a::calibration getCalibration();
    	};
    }
    
    ```

3. k4a_grabber.cpp

    ```c++
    #include <Eigen/Dense>
    
    #include "k4a_grabber.h"
    
    pcl::KinectAzureDKGrabber::KinectAzureDKGrabber(const int& device_id_, const int& depth_mode_, const int& color_format_, const int& color_resolution_) :
    	config(K4A_DEVICE_CONFIG_INIT_DISABLE_ALL),
    	dev(nullptr),
    	colorImage(nullptr),
    	depthImage(nullptr),
    	infraredImage(nullptr),
    	running(false),
    	quit(false),
    	signal_PointXYZ(nullptr),
    	signal_PointXYZI(nullptr),
    	signal_PointXYZRGB(nullptr),
    	signal_PointXYZRGBA(nullptr)
    {
    	setupDevice(device_id_, depth_mode_, color_format_, color_resolution_);
    
    	signal_PointXYZ = createSignal<signal_KinectAzureDK_PointXYZ>();
    	signal_PointXYZI = createSignal<signal_KinectAzureDK_PointXYZI>();
    	signal_PointXYZRGB = createSignal<signal_KinectAzureDK_PointXYZRGB>();
    	signal_PointXYZRGBA = createSignal<signal_KinectAzureDK_PointXYZRGBA>();
    }
    
    pcl::KinectAzureDKGrabber::~KinectAzureDKGrabber() throw()
    {
    	stop();
    
    	disconnect_all_slots<signal_KinectAzureDK_PointXYZ>();
    	disconnect_all_slots<signal_KinectAzureDK_PointXYZI>();
    	disconnect_all_slots<signal_KinectAzureDK_PointXYZRGB>();
    	disconnect_all_slots<signal_KinectAzureDK_PointXYZRGBA>();
    
    	thread.join();
    
    	if (dev)
    	{
    		transformation.destroy();
    		dev.close();
    	}
    }
    
    void pcl::KinectAzureDKGrabber::start()
    {
    	dev = k4a::device::open(device_id);
    	dev.start_cameras(&config);
    	calibration = dev.get_calibration(config.depth_mode, config.color_resolution);
    	transformation = k4a::transformation(calibration);
    
    	running = true;
    
    	thread = std::thread(&KinectAzureDKGrabber::threadFunction, this);
    }
    k4a::calibration pcl::KinectAzureDKGrabber::getCalibration()
    {
    	return calibration;
    }
    void pcl::KinectAzureDKGrabber::stop()
    {
    	std::unique_lock<std::mutex> lock(mutex);
    
    	quit = true;
    	running = false;
    
    	lock.unlock();
    }
    
    bool pcl::KinectAzureDKGrabber::isRunning() const
    {
    	std::unique_lock<std::mutex> lock(mutex);
    
    	return running;
    
    	lock.unlock();
    }
    
    std::string pcl::KinectAzureDKGrabber::getName() const
    {
    	return std::string("KinectAzureDKGrabber: " + std::to_string(device_id));
    }
    
    float pcl::KinectAzureDKGrabber::getFramesPerSecond() const
    {
    	return config.camera_fps;
    }
    
    void pcl::KinectAzureDKGrabber::setupDevice(const int& device_id_, const int& depth_mode_, const int& color_format_, const int& color_resolution_)
    {
    	device_id = device_id_;
    
    	config.camera_fps = K4A_FRAMES_PER_SECOND_30;
    	config.depth_mode = k4a_depth_mode_t(depth_mode_);
    	config.color_format = k4a_image_format_t(color_format_);
    	config.color_resolution = k4a_color_resolution_t(color_resolution_);
    	config.synchronized_images_only = true;
    }
    void pcl::KinectAzureDKGrabber::threadFunction()
    {
    	while (!quit)
    	{
    		std::unique_lock<std::mutex> lock(mutex);
    		k4a::capture capture;
    		if (!dev.get_capture(&capture, std::chrono::milliseconds(0)))
    		{
    			continue;
    		}
    
    		depthImage = capture.get_depth_image();
    		if (depthImage == nullptr)
    		{
    			throw std::exception("Failed to get depth image from capture\n");
    		}
    
    		colorImage = capture.get_color_image();
    		if (colorImage == nullptr)
    		{
    			throw std::exception("Failed to get color image from capture\n");
    		}
    
    		infraredImage = capture.get_ir_image();
    		if (infraredImage == nullptr)
    		{
    			throw std::exception("Failed to get IR image from capture\n");
    		}
    
    		lock.unlock();
    
    		if (signal_PointXYZ->num_slots() > 0)
    		{
    			signal_PointXYZ->operator()(convertDepthToPointXYZ());
    		}
    
    		if (signal_PointXYZI->num_slots() > 0)
    		{
    			signal_PointXYZI->operator()(convertInfraredDepthToPointXYZI());
    		}
    
    		if (signal_PointXYZRGB->num_slots() > 0)
    		{
    			signal_PointXYZRGB->operator()(convertRGBDepthToPointXYZRGB());
    		}
    
    		if (signal_PointXYZRGBA->num_slots() > 0)
    		{
    			signal_PointXYZRGBA->operator()(convertRGBADepthToPointXYZRGBA());
    		}
    	}
    }
    
    pcl::PointCloud<pcl::PointXYZ>::Ptr pcl::KinectAzureDKGrabber::convertDepthToPointXYZ()
    {
    	PointCloud<PointXYZ>::Ptr cloud(new PointCloud<PointXYZ>());
    	int color_image_width_pixels = colorImage.get_width_pixels();
    	int color_image_height_pixels = colorImage.get_height_pixels();
    
    	k4a::image transformed_depth_image = NULL;
    	transformed_depth_image = k4a::image::create(K4A_IMAGE_FORMAT_DEPTH16,
    		color_image_width_pixels,
    		color_image_height_pixels,
    		color_image_width_pixels * (int)sizeof(uint16_t));
    
    	k4a::image point_cloud_image = NULL;
    	point_cloud_image = k4a::image::create(K4A_IMAGE_FORMAT_CUSTOM,
    		color_image_width_pixels,
    		color_image_height_pixels,
    		color_image_width_pixels * 3 * (int)sizeof(int16_t));
    
    	transformation.depth_image_to_color_camera(depthImage, &transformed_depth_image);
    	transformation.depth_image_to_point_cloud(transformed_depth_image, K4A_CALIBRATION_TYPE_COLOR, &point_cloud_image);
    
    	int width = colorImage.get_width_pixels();
    	int height = colorImage.get_height_pixels();
    
    	cloud->width = width;
    	cloud->height = height;
    	cloud->is_dense = false;
    	cloud->points.resize(cloud->height * cloud->width);
    
    	int16_t* point_cloud_image_data = (int16_t*)(void*)point_cloud_image.get_buffer();
    
    #ifdef VTK_VISUALIZATION
    	Eigen::Matrix3f m;
    	m = Eigen::AngleAxisf(-M_PI, Eigen::Vector3f::UnitZ());
    #endif
    
    	for (int i = 0; i < width * height; ++i)
    	{
    		PointXYZ point;
    
    		point.x = point_cloud_image_data[3 * i + 0] / 1000.0f;
    		point.y = point_cloud_image_data[3 * i + 1] / 1000.0f;
    		point.z = point_cloud_image_data[3 * i + 2] / 1000.0f;
    
    		if (point.z == 0)
    		{
    			continue;
    		}
    
    #ifdef VTK_VISUALIZATION
    		point.getVector3fMap() = m * point.getVector3fMap();
    #endif
    
    		cloud->points[i] = point;
    	}
    	return cloud;
    }
    pcl::PointCloud<pcl::PointXYZI>::Ptr pcl::KinectAzureDKGrabber::convertInfraredDepthToPointXYZI()
    {
    	PointCloud<PointXYZI>::Ptr cloud(new PointCloud<PointXYZI>());
    	int color_image_width_pixels = colorImage.get_width_pixels();
    	int color_image_height_pixels = colorImage.get_height_pixels();
    
    	k4a::image transformed_depth_image = NULL;
    	transformed_depth_image = k4a::image::create(K4A_IMAGE_FORMAT_DEPTH16,
    		color_image_width_pixels,
    		color_image_height_pixels,
    		color_image_width_pixels * (int)sizeof(uint16_t));
    
    	k4a::image transformed_infrared_image = NULL;
    	transformed_infrared_image = k4a::image::create(K4A_IMAGE_FORMAT_IR16,
    		color_image_width_pixels,
    		color_image_height_pixels,
    		color_image_width_pixels * (int)sizeof(uint16_t));
    
    	k4a::image point_cloud_image = NULL;
    	point_cloud_image = k4a::image::create(K4A_IMAGE_FORMAT_CUSTOM,
    		color_image_width_pixels,
    		color_image_height_pixels,
    		color_image_width_pixels * 3 * (int)sizeof(int16_t));
    
    	transformation.depth_image_to_color_camera(depthImage, &transformed_depth_image);
    	transformation.depth_image_to_color_camera(infraredImage, &transformed_infrared_image);
    	transformation.depth_image_to_point_cloud(transformed_depth_image, K4A_CALIBRATION_TYPE_COLOR, &point_cloud_image);
    
    	int width = colorImage.get_width_pixels();
    	int height = colorImage.get_height_pixels();
    
    	cloud->width = width;
    	cloud->height = height;
    	cloud->is_dense = false;
    	cloud->points.resize(cloud->height * cloud->width);
    
    	int16_t* point_cloud_image_data = (int16_t*)(void*)point_cloud_image.get_buffer();
    	int16_t* transformed_infrared_image_data = (int16_t*)(void*)transformed_infrared_image.get_buffer();
    
    #ifdef VTK_VISUALIZATION
    	Eigen::Matrix3f m;
    	m = Eigen::AngleAxisf(-M_PI, Eigen::Vector3f::UnitZ());
    #endif
    
    	for (int i = 0; i < width * height; ++i)
    	{
    		PointXYZI point;
    
    		point.x = point_cloud_image_data[3 * i + 0] / 1000.0f;
    		point.y = point_cloud_image_data[3 * i + 1] / 1000.0f;
    		point.z = point_cloud_image_data[3 * i + 2] / 1000.0f;
    
    		if (point.z == 0)
    		{
    			continue;
    		}
    
    		point.intensity = transformed_infrared_image_data[i];
    
    #ifdef VTK_VISUALIZATION
    		point.getVector3fMap() = m * point.getVector3fMap();
    #endif
    
    		cloud->points[i] = point;
    	}
    	return cloud;
    }
    pcl::PointCloud<pcl::PointXYZRGB>::Ptr pcl::KinectAzureDKGrabber::convertRGBDepthToPointXYZRGB()
    {
    	PointCloud<PointXYZRGB>::Ptr cloud(new PointCloud<PointXYZRGB>());
    	int color_image_width_pixels = colorImage.get_width_pixels();
    	int color_image_height_pixels = colorImage.get_height_pixels();
    
    	k4a::image transformed_depth_image = NULL;
    	transformed_depth_image = k4a::image::create(K4A_IMAGE_FORMAT_DEPTH16,
    		color_image_width_pixels,
    		color_image_height_pixels,
    		color_image_width_pixels * (int)sizeof(uint16_t));
    
    	k4a::image point_cloud_image = NULL;
    	point_cloud_image = k4a::image::create(K4A_IMAGE_FORMAT_CUSTOM,
    		color_image_width_pixels,
    		color_image_height_pixels,
    		color_image_width_pixels * 3 * (int)sizeof(int16_t));
    
    	transformation.depth_image_to_color_camera(depthImage, &transformed_depth_image);
    	transformation.depth_image_to_point_cloud(transformed_depth_image, K4A_CALIBRATION_TYPE_COLOR, &point_cloud_image);
    
    	int width = colorImage.get_width_pixels();
    	int height = colorImage.get_height_pixels();
    
    	cloud->width = width;
    	cloud->height = height;
    	cloud->is_dense = false;
    	cloud->points.resize(cloud->height * cloud->width);
    
    	int16_t* point_cloud_image_data = (int16_t*)(void*)point_cloud_image.get_buffer();
    	uint8_t* color_image_data = colorImage.get_buffer();
    
    #ifdef VTK_VISUALIZATION
    	Eigen::Matrix3f m;
    	m = Eigen::AngleAxisf(-M_PI, Eigen::Vector3f::UnitZ());
    #endif
    
    	for (int i = 0; i < width * height; ++i)
    	{
    		PointXYZRGB point;
    
    		point.x = point_cloud_image_data[3 * i + 0] / 1000.0f;
    		point.y = point_cloud_image_data[3 * i + 1] / 1000.0f;
    		point.z = point_cloud_image_data[3 * i + 2] / 1000.0f;
    		if (point.z == 0)
    		{
    			continue;
    		}
    
    #ifdef VTK_VISUALIZATION
    		point.getVector3fMap() = m * point.getVector3fMap();
    #endif
    
    		point.b = color_image_data[4 * i + 0];
    		point.g = color_image_data[4 * i + 1];
    		point.r = color_image_data[4 * i + 2];
    		uint8_t alpha = color_image_data[4 * i + 3];
    		if (point.b == 0 && point.g == 0 && point.r == 0 && alpha == 0)
    		{
    			continue;
    		}
    		cloud->points[i] = point;
    	}
    	return cloud;
    }
    
    pcl::PointCloud<pcl::PointXYZRGBA>::Ptr pcl::KinectAzureDKGrabber::convertRGBADepthToPointXYZRGBA(/*RGBQUAD* colorBuffer, UINT16* depthBuffer*/)
    {
    	PointCloud<PointXYZRGBA>::Ptr cloud(new PointCloud<PointXYZRGBA>());
    	int color_image_width_pixels = colorImage.get_width_pixels();
    	int color_image_height_pixels = colorImage.get_height_pixels();
    
    	k4a::image transformed_depth_image = NULL;
    	transformed_depth_image = k4a::image::create(K4A_IMAGE_FORMAT_DEPTH16,
    		color_image_width_pixels,
    		color_image_height_pixels,
    		color_image_width_pixels * (int)sizeof(uint16_t));
    
    	k4a::image point_cloud_image = NULL;
    	point_cloud_image = k4a::image::create(K4A_IMAGE_FORMAT_CUSTOM,
    		color_image_width_pixels,
    		color_image_height_pixels,
    		color_image_width_pixels * 3 * (int)sizeof(int16_t));
    
    	transformation.depth_image_to_color_camera(depthImage, &transformed_depth_image);
    	transformation.depth_image_to_point_cloud(transformed_depth_image, K4A_CALIBRATION_TYPE_COLOR, &point_cloud_image);
    
    	int width = colorImage.get_width_pixels();
    	int height = colorImage.get_height_pixels();
    
    	cloud->width = width;
    	cloud->height = height;
    	cloud->is_dense = false;
    	cloud->points.resize(cloud->height * cloud->width);
    
    	int16_t* point_cloud_image_data = (int16_t*)(void*)point_cloud_image.get_buffer();
    	uint8_t* color_image_data = colorImage.get_buffer();
    
    #ifdef VTK_VISUALIZATION
    	Eigen::Matrix3f m;
    	m = Eigen::AngleAxisf(-M_PI, Eigen::Vector3f::UnitZ());
    #endif
    
    	for (int i = 0; i < width * height; ++i)
    	{
    		PointXYZRGBA point;
    
    		point.x = point_cloud_image_data[3 * i + 0] / 1000.0f;
    		point.y = point_cloud_image_data[3 * i + 1] / 1000.0f;
    		point.z = point_cloud_image_data[3 * i + 2] / 1000.0f;
    
    		if (point.z == 0)
    		{
    			continue;
    		}
    
    
    #ifdef VTK_VISUALIZATION
    		point.getVector3fMap() = m * point.getVector3fMap();
    #endif
    
    		point.b = color_image_data[4 * i + 0];
    		point.g = color_image_data[4 * i + 1];
    		point.r = color_image_data[4 * i + 2];
    		point.a = color_image_data[4 * i + 3];
    
    		if (point.b == 0 && point.g == 0 && point.r == 0 && point.a == 0)
    		{
    			continue;
    		}
    
    		cloud->points[i] = point;
    	}
    	return cloud;
    }
    ```

    

