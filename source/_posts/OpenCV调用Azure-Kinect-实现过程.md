---
title: ' OpenCV调用Azure Kinect 实现过程'
date: 2020-08-01 19:44:28
tags:
- Azure Kinect
---

> 源代码来源：
>
> 源代码作者：无雨森
>
> 浙江科技大学在读博士

<!-- more -->

## 软件硬件a

VS2019

Azure Kinect

## 名称

OpenCV_OneKinect

## 本地配置过程

### 配置OpenCV

略过

### 配置模式与头文件路径

- 本地建立VS空项目，release X64模式

![](https://cdn.jsdelivr.net/gh/junlin49/up/20200731102606.png)

- 新建cpp文档，作为源文件，将OpenCV_OneKinect的源代码复制进新建的cpp文档
- 配置头文件路径，本文以此为例

![](https://cdn.jsdelivr.net/gh/junlin49/up/20200731102928.png)

项目 -> 属性 -> C/C++ -> 附加包含目录

填入上面图片所示路径

![](https://cdn.jsdelivr.net/gh/junlin49/up/20200731103339.png)

### 安装Kinect包

打开包管理器，下载如下图所示包

![](https://cdn.jsdelivr.net/gh/junlin49/up/20200731103706.png)

安装结束后，即可发现代码已经没有报错

### 修改源代码

此时F5运行，会发现报错：

![](https://cdn.jsdelivr.net/gh/junlin49/up/20200731104137.png)
错误	LNK2001	无法解析的外部符号 "void __cdecl sen::ColorizeDepthImage(class k4a::image const &,struct sen::Pixel (__cdecl*)(unsigned short const &,unsigned short const &,unsigned short const &),struct std::pair<unsigned short,unsigned short>,class std::vector<struct sen::Pixel,class std::allocator<struct sen::Pixel> > *)" (?ColorizeDepthImage@sen@@YAXAEBVimage@k4a@@P6A?AUPixel@1@AEBG11@ZU?$pair@GG@std@@PEAV?$vector@UPixel@sen@@V?$allocator@UPixel@sen@@@std@@@6@@Z)	Test	F:\VS_Area\Test\源.obj	1	



原因：

StaticImageProperties.h头文件中，缺少ColorizeDepthImage的函数体

解决方法：

在StaticImageProperties.h头文件中添加即可

```c++
void ColorizeDepthImage(const k4a::image& depthImage,
		DepthPixelVisualizationFunction visualizationFn,
		std::pair<uint16_t, uint16_t> expectedValueRange,
		std::vector<Pixel>* buffer)
	{
		const k4a_image_format_t imageFormat = depthImage.get_format();
		if (imageFormat != K4A_IMAGE_FORMAT_DEPTH16 && imageFormat != K4A_IMAGE_FORMAT_IR16)
		{
			throw std::logic_error("Attempted to colorize a non-depth image!");
		}

		const int width = depthImage.get_width_pixels();
		const int height = depthImage.get_height_pixels();

		buffer->resize(static_cast<size_t>(width * height));

		const uint16_t* depthData = reinterpret_cast<const uint16_t*>(depthImage.get_buffer());
		for (int h = 0; h < height; ++h)
		{
			for (int w = 0; w < width; ++w)
			{
				const size_t currentPixel = static_cast<size_t>(h * width + w);
				(*buffer)[currentPixel] = visualizationFn(depthData[currentPixel], expectedValueRange.first, expectedValueRange.second);
			}
		}
	}
```

运行成功：

![](https://cdn.jsdelivr.net/gh/junlin49/up/20200801211404.png)



