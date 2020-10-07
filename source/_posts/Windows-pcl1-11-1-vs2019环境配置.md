---
title: Windows+pcl1.11.1+vs2019环境配置
date: 2020-10-05 17:19:36
cover: https://gitee.com/JunLuJun/up/raw/master/20201005183615.jpg
tags:
- PCL
---

# Windows+pcl1.11.1+vs2019环境配置

> 参考博文【若有侵权，立即删除】
>
> https://blog.csdn.net/qq_36686437/article/details/106347482
>
> https://blog.csdn.net/qq_36686437/article/details/108803098
>
> https://blog.csdn.net/zhangxz259/article/details/85683201

## 引言

想学点云处理方面的知识了，就配一个pcl

## 步骤

### 下载PCL1.11.0

Github下载地址：https://github.com/PointCloudLibrary/pcl/releases
下载红框内的两个文件
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200526085007336.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2Njg2NDM3,size_16,color_FFFFFF,t_70)

### 安装PCL1.11.0

**安装“PCL-1.11.0-AllInOne-msvc2019-win64.exe”。**

- 选择第二个，自动添加系统变量
    ![PCL安装界面00](https://img-blog.csdnimg.cn/20200526085600527.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2Njg2NDM3,size_16,color_FFFFFF,t_70)
- 安装路径选择D盘，系统会自动新建PCL 1.11.0文件夹。
    ![PCL安装界面01](https://img-blog.csdnimg.cn/20200526085833607.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2Njg2NDM3,size_16,color_FFFFFF,t_70)
- **安装完成之后打开文件夹 D:\PCL 1.11.0\3rdParty\OpenNI2
    双击OpenNI-Windows-x64-2.2 选择路径（D:\PCL 1.11.0\3rdParty\OpenNI2）安装即可。**
    ![PCL安装界面02](https://img-blog.csdnimg.cn/20200526090128836.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2Njg2NDM3,size_16,color_FFFFFF,t_70)
- **全部安装完成后，将pcl-1.11.0-pdb-msvc2019-win64.zip解压后的.pdb文件拷贝到（D:\PCL 1.11.0\bin）中。**
- **设置环境变量：右击计算机—属性—高级系统设置—高级—环境变量—用户变量—Path—编辑!**
    ![PCL安装界面03](https://img-blog.csdnimg.cn/20200526090605288.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2Njg2NDM3,size_16,color_FFFFFF,t_70)
- 如下图所示。
    ![PCL安装界面04](https://img-blog.csdnimg.cn/20200526090646241.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2Njg2NDM3,size_16,color_FFFFFF,t_70)
    在这里直接给出，防止出现错误（依次添加）：

```c
%PCL_ROOT%\3rdParty\FLANN\bin
%PCL_ROOT%\3rdParty\VTK\bin
%OPENNI2_REDIST64%
%OPENNI2_LIB64%
%OPENNI2_INCLUDE64%
```

到此，环境变量的配置完成。

### VS2019相关设置

- **新建空项目**
    ![VS配置界面00](https://img-blog.csdnimg.cn/20200526090823909.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2Njg2NDM3,size_16,color_FFFFFF,t_70)
- 解决方案配置选择Debug，解决方案平台选择x64。
    ![VS配置界面01](https://img-blog.csdnimg.cn/20200526103129751.png)
- **新建一个C++源文件**
    ![VS配置界面02](https://img-blog.csdnimg.cn/20200526103415256.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2Njg2NDM3,size_16,color_FFFFFF,t_70)
- **右击新建的项目—属性：打开属性表**

![VS配置界面03](https://img-blog.csdnimg.cn/20200526103657409.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2Njg2NDM3,size_16,color_FFFFFF,t_70)

- **配置属性—调试—环境—添加：**

```c
PATH=D:\PCL 1.11.0\\bin;D:\PCL 1.11.0\\3rdParty\FLANN\bin;D:\PCL 1.11.0\\3rdParty\VTK\bin;D:\PCL 1.11.0\\3rdParty\OpenNI2\Tools
```

![VS配置界面04](https://img-blog.csdnimg.cn/20200526104101644.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2Njg2NDM3,size_16,color_FFFFFF,t_70)

- **C/C++—语言—符合模式：否**
    ![VS配置界面05](https://img-blog.csdnimg.cn/20200526104535843.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2Njg2NDM3,size_16,color_FFFFFF,t_70)
- **C/C++—常规—SDL检查：否**
    ![VS配置界面06](https://img-blog.csdnimg.cn/20200526104722251.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2Njg2NDM3,size_16,color_FFFFFF,t_70)

### 配置PCL1.11.0

**为了使用方便，这里使用添加属性表的形式。注意：添加属性表的方式，需要再次将属性表内的SDL检测设置为：否。否则将报如下错误：**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200925194241929.png#pic_center)

- **视图—其他窗口—属性管理器**
    ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200526091549978.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2Njg2NDM3,size_16,color_FFFFFF,t_70)

- **打开属性管理器之后，选择Debug|X64—单击Debug|X64左侧倒三角—右击选择 添加型项目属性表**
    ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200526092104635.png)
- **项目属性表命名**
    ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200526092333946.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2Njg2NDM3,size_16,color_FFFFFF,t_70)
- **双击新添加的属性表—VC++目录—包含目录，添加7个include路径**
    ![在这里插入图片描述](https://img-blog.csdnimg.cn/2020052609260194.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2Njg2NDM3,size_16,color_FFFFFF,t_70)

具体添加的include路径如下：

```c
D:\PCL 1.11.0\include\pcl-1.11
D:\PCL 1.11.0\3rdParty\Boost\include\boost-1_73
D:\PCL 1.11.0\3rdParty\Eigen\eigen3
D:\PCL 1.11.0\3rdParty\FLANN\include
D:\PCL 1.11.0\3rdParty\Qhull\include
D:\PCL 1.11.0\3rdParty\VTK\include\vtk-8.2
D:\PCL 1.11.0\3rdParty\OpenNI2\Include
```

- **VC++目录—库目录，添加6个lib路径**
    ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200526093426340.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2Njg2NDM3,size_16,color_FFFFFF,t_70)
    具体添加的lib路径如下：

```c
D:\PCL 1.11.0\lib
D:\PCL 1.11.0\3rdParty\Boost\lib
D:\PCL 1.11.0\3rdParty\FLANN\lib
D:\PCL 1.11.0\3rdParty\Qhull\lib
D:\PCL 1.11.0\3rdParty\OpenNI2\Lib
D:\PCL 1.11.0\3rdParty\VTK\lib
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200526093806871.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2Njg2NDM3,size_16,color_FFFFFF,t_70)

- **C/C++—预处理器—预处理器定义—添加：**

```c
BOOST_USE_WINDOWS_H
NOMINMAX
_CRT_SECURE_NO_DEPRECATE
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200526094047662.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2Njg2NDM3,size_16,color_FFFFFF,t_70)

- **链接器—输入—附加依赖项——添加PCL和VTK的相关lib文件。我用的Debug版本。**
    ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200526094504741.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2Njg2NDM3,size_16,color_FFFFFF,t_70)
    附加依赖项具体添加内容如下：（文章末尾 **附录**中 给出批量获取附加依赖项的方法）
    输入到属性表里边的时候必须一行对应一个lib才能成功。
    Debug版本
- **保存属性表**
    ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200526102730254.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2Njg2NDM3,size_16,color_FFFFFF,t_70)
    下一次需要创建新项目的时候，只需进行第三步 **VS2019相关设置** 的操作，然后打开属性管理器，添加现有属性表，找到之前保存的属性表添加进去即可。
    ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200526105924435.png)
    ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200526110148988.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2Njg2NDM3,size_16,color_FFFFFF,t_70

### 测试代码

```c
#include <iostream>
#include <vector>
#include <ctime>
#include <pcl/point_cloud.h>
#include <pcl/octree/octree.h>
#include <boost/thread/thread.hpp>
#include <pcl/visualization/pcl_visualizer.h>
using namespace std;
int
main(int argc, char** argv)
{
	srand((unsigned int)time(NULL));
	pcl::PointCloud<pcl::PointXYZ>::Ptr cloud(new pcl::PointCloud<pcl::PointXYZ>);
	// 创建点云数据
	cloud->width = 1000;
	cloud->height = 1;
	cloud->points.resize(cloud->width * cloud->height);
	for (size_t i = 0; i < cloud->points.size(); ++i)
	{
		cloud->points[i].x = 1024.0f * rand() / (RAND_MAX + 1.0f);
		cloud->points[i].y = 1024.0f * rand() / (RAND_MAX + 1.0f);
		cloud->points[i].z = 1024.0f * rand() / (RAND_MAX + 1.0f);
	}

	pcl::octree::OctreePointCloudSearch<pcl::PointXYZ> octree(0.1);
	octree.setInputCloud(cloud);
	octree.addPointsFromInputCloud();
	pcl::PointXYZ searchPoint;
	searchPoint.x = 1024.0f * rand() / (RAND_MAX + 1.0f);
	searchPoint.y = 1024.0f * rand() / (RAND_MAX + 1.0f);
	searchPoint.z = 1024.0f * rand() / (RAND_MAX + 1.0f);

	//半径内近邻搜索
	vector<int>pointIdxRadiusSearch;
	vector<float>pointRadiusSquaredDistance;
	float radius = 256.0f * rand() / (RAND_MAX + 1.0f);
	cout << "Neighbors within radius search at (" << searchPoint.x
		<< " " << searchPoint.y
		<< " " << searchPoint.z
		<< ") with radius=" << radius << endl;
	if (octree.radiusSearch(searchPoint, radius, pointIdxRadiusSearch, pointRadiusSquaredDistance) > 0)
	{
		for (size_t i = 0; i < pointIdxRadiusSearch.size(); ++i)
			cout << "    " << cloud->points[pointIdxRadiusSearch[i]].x
			<< " " << cloud->points[pointIdxRadiusSearch[i]].y
			<< " " << cloud->points[pointIdxRadiusSearch[i]].z
			<< " (squared distance: " << pointRadiusSquaredDistance[i] << ")" << endl;
	}
	// 初始化点云可视化对象
	boost::shared_ptr<pcl::visualization::PCLVisualizer>viewer(new pcl::visualization::PCLVisualizer("显示点云"));
	viewer->setBackgroundColor(0, 0, 0);  //设置背景颜色为黑色
	// 对点云着色可视化 (red).
	pcl::visualization::PointCloudColorHandlerCustom<pcl::PointXYZ>target_color(cloud, 255, 0, 0);
	viewer->addPointCloud<pcl::PointXYZ>(cloud, target_color, "target cloud");
	viewer->setPointCloudRenderingProperties(pcl::visualization::PCL_VISUALIZER_POINT_SIZE, 1, "target cloud");

	// 等待直到可视化窗口关闭
	while (!viewer->wasStopped())
	{
		viewer->spinOnce(100);
		boost::this_thread::sleep(boost::posix_time::microseconds(1000));
	}

	return (0);
}
```

**输出下图（数字可能不同），则表示安装成功！**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200526110936338.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2Njg2NDM3,size_16,color_FFFFFF,t_70)

## 附录

```c
win+r调出“运行”窗口并输出cmd
输入：cd /d D:\PCL 1.11.0\3rdParty\VTK\lib 回车 （填自己的路径）
输入：dir /b *.lib *>0.txt 回车 
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200526112537554.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2Njg2NDM3,size_16,color_FFFFFF,t_70)
这时打开你对应路劲的目录，多了一个0.txt文件，里面存了你这个文件夹里所有链接库名字。
由于每一个Debug版本和Release版本的链接库是挨在一起的。写一个读取文档并对其分别保存就行了。
具体代码如下：（主要功能是读取一个txt文件，将其中奇数行和偶数行单独输出到新的txt文档。）

```c
#include <iostream>
#include <string>
#include <fstream> 
#include <iostream>
using namespace std;

int main()
{
	ifstream txtfile;//打开读取的文件
	ofstream txt01;//保存的文件
	ofstream txt02;//保存的文件
	string temp;
	int index = 0;//用于判断奇偶

	txtfile.open("0.txt", ios::in);
 
	while (!txtfile.eof())            // 若未到文件结束一直循环
	{

		getline(txtfile, temp);//一行一行读取
		if (index%2==0)//判断除以2的余数，即为奇偶的判断
		{
			txt01.open("1.txt", ios::app);
			txt01 << temp;
			txt01 << endl;
			txt01.close();
		}
		else
		{
			txt02.open("2.txt", ios::app);
			txt02 << temp;
			txt02 << endl;
			txt02.close();
		}
		index++;
	}
	txtfile.close();   //关闭文件
	txtfile.close();
	txt01.close();
	txt02.close();
	
	return 0;
}
```