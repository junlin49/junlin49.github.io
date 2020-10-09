---
title: PCL-kdtree实现快速邻域搜索
date: 2020-10-09 09:02:29
cover: https://gitee.com/JunLuJun/up/raw/master/20201009090400.png
tags:
- pcl
---

# PCL-kdtree实现快速邻域搜索

```c++
#include <pcl/point_cloud.h>
#include <pcl/kdtree/kdtree_flann.h>
#include <iostream>
#include <vector>
#include <ctime>

//------------------------------
//PCL中实现快速邻域搜索
//------------------------------

int main(int argc, char** argv)
{
	//用系统时间初始化随机种子
	srand(time(NULL));
	pcl::PointCloud<pcl::PointXYZ>::Ptr cloud(new pcl::PointCloud<pcl::PointXYZ>);
	//随机点云生成
	cloud->width = 1000;
	cloud->height = 1;
	cloud->points.resize(cloud->width * cloud->height);
	for (size_t i = 0; i < cloud->points.size(); ++i)
	{
		cloud->points[i].x = 1024.0f * rand() / (RAND_MAX + 1.0f);
		cloud->points[i].y = 1024.0f * rand() / (RAND_MAX + 1.0f);
		cloud->points[i].z = 1024.0f * rand() / (RAND_MAX + 1.0f);
	}
	//创建KdTreeFLANN类型对象，kdtree
	pcl::KdTreeFLANN<pcl::PointXYZ>kdtree;
	//设置搜索空间
	kdtree.setInputCloud(cloud);
	//定义查询点并赋随机值
	pcl::PointXYZ searchPoint;
	searchPoint.x = 1024.0f * rand() / (RAND_MAX + 1.0f);
	searchPoint.y = 1024.0f * rand() / (RAND_MAX + 1.0f);
	searchPoint.z = 1024.0f * rand() / (RAND_MAX + 1.0f);
	// k近邻搜索
	int K = 10;
	//定义向量pointIdxNKNSearch，存储查询点邻近索引
	std::vector<int>pointIdxNKNSearch(K);
	//定义向量pointNKNSquaredDistance，存储近邻点对应平方距离
	std::vector<float>pointNKNSquaredDistance(K);
	//打印相关信息
	std::cout << "K nearest neighbor search at (" << searchPoint.x
		<< " " << searchPoint.y
		<< " " << searchPoint.z
		<< ") with K=" << K << std::endl;
	if (kdtree.nearestKSearch(searchPoint, K, pointIdxNKNSearch, pointNKNSquaredDistance) > 0)
		//执行K邻近搜索，返回值是近邻的个数
		//searchPoint，查询点
		//K，10
		//pointIdxNKNSearch，存储查询点近邻索引的向量
		//pointNKNSquaredDistance，存储近邻点对应的平方距离
	{
		//打印输出
		for (size_t i = 0; i < pointIdxNKNSearch.size(); ++i)
			std::cout << "    " << cloud->points[pointIdxNKNSearch[i]].x
			<< " " << cloud->points[pointIdxNKNSearch[i]].y
			<< " " << cloud->points[pointIdxNKNSearch[i]].z
			<< " (squared distance: " << pointNKNSquaredDistance[i] << ")" << std::endl;
	}
	// 在半径r内搜索近邻
	//重新定义两个向量，存储近邻索引与对应平方距离
	std::vector<int> pointIdxRadiusSearch;
	std::vector<float> pointRadiusSquaredDistance;
	//定义随机数r
	float radius = 256.0f * rand() / (RAND_MAX + 1.0f);
	//打印相关信息
	std::cout << "Neighbors within radius search at (" << searchPoint.x
		<< " " << searchPoint.y
		<< " " << searchPoint.z
		<< ") with radius=" << radius << std::endl;

	if (kdtree.radiusSearch(searchPoint, radius, pointIdxRadiusSearch, pointRadiusSquaredDistance) > 0)
		//执行r内邻近搜索，返回值是近邻的个数
		//searchPoint，查询点
		//radius，半径
		//pointIdxRadiusSearch，存储查询点近邻索引的向量
		//pointRadiusSquaredDistance，存储近邻点对应的平方距离
	{
		//打印输出
		for (size_t i = 0; i < pointIdxRadiusSearch.size(); ++i)
			std::cout << "    " << cloud->points[pointIdxRadiusSearch[i]].x
			<< " " << cloud->points[pointIdxRadiusSearch[i]].y
			<< " " << cloud->points[pointIdxRadiusSearch[i]].z
			<< " (squared distance: " << pointRadiusSquaredDistance[i] << ")" << std::endl;
	}
	return 0;
}
```

