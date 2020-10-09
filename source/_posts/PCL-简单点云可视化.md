---
title: PCL-简单点云可视化
date: 2020-10-09 09:50:51
cover: https://gitee.com/JunLuJun/up/raw/master/20201009090400.png
tags:
- pcl
---

# PCL-简单点云可视化

```c++
#include <pcl/visualization/cloud_viewer.h>
#include <iostream>
#include <pcl/io/io.h>
#include <pcl/io/pcd_io.h>

//--------------------------------
//简单点云可视化
//--------------------------------

int user_data;

/// <summary>
/// 回调函数，只能调用一次的回调函数
/// 对可视化对象背景的颜色设置，添加一个圆球几何体
/// </summary>
/// <param name="viewer"></param>
void viewerOneOff(pcl::visualization::PCLVisualizer& viewer)
{
    //背景
    viewer.setBackgroundColor(1.0, 0.5, 1.0);
    //存储球心位置
    pcl::PointXYZ o;
    o.x = 1.0;
    o.y = 0;
    o.z = 0;
    //添加圆球几何对象
    viewer.addSphere(o, 0.25, "sphere", 0);
    std::cout << "i only run once" << std::endl;

}

/// <summary>
/// 渲染输出时每次都调用的回调函数
/// 每帧都执行一次
/// 在可视化对象中添加一个刷新显示字符串
/// </summary>
/// <param name="viewer"></param>
void viewerPsycho(pcl::visualization::PCLVisualizer& viewer)
{
    static unsigned count = 0;
    std::stringstream ss;
    ss << "Once per viewer loop: " << count++;
    viewer.removeShape("text", 0);
    viewer.addText(ss.str(), 200, 300, "text", 0); 
    //FIXME: possible race condition here:
    user_data++;
}

int main()
{
    pcl::PointCloud<pcl::PointXYZRGBA>::Ptr cloud(new pcl::PointCloud<pcl::PointXYZRGBA>);
    pcl::io::loadPCDFile("my_point_cloud.pcd", *cloud);

    pcl::visualization::CloudViewer viewer("Cloud Viewer");
    //showCloud函数是同步的，在此处等待直到渲染显示为止
    viewer.showCloud(cloud);
    //该注册函数在可视化时只调用一次
    viewer.runOnVisualizationThreadOnce(viewerOneOff);
    //该注册函数在渲染输出时每次都调用
    viewer.runOnVisualizationThread(viewerPsycho);

    while (!viewer.wasStopped())
    {
        //在此处可以添加其他处理
        user_data++;
    }
    return 0;
}
```

