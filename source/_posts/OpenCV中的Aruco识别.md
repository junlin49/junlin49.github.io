---
title: OpenCV中的Aruco识别
date: 2020-09-17 10:21:35
tags:
- Aruco
---

# OpenCV中的Aruco识别

> 转载博客：
>
> https://blog.csdn.net/u010260681/article/details/77089657
>
> 如有侵权，立即删除

姿态估计（Pose estimation）在[计算机视觉](http://lib.csdn.net/base/computervison)领域扮演着十分重要的角色：[机器人](http://lib.csdn.net/base/robot)导航、增强现实以及其它。这一过程的基础是找到现实世界和图像投影之间的对应点。这通常是很困难的一步，因此我们常常用自己制作的或基本的Marker来让这一切变得更容易。

最为流行的一个途径是基于二进制平方的标记。这种Marker的主要便利之处在于，一个Marker提供了足够多的对应（四个角）来获取相机的信息。同样的，内部的二进制编码使得[算法](http://lib.csdn.net/base/datastructure)非常健壮，允许应用错误检测和校正技术的可能性。

aruco模块基于ArUco库，这是一个检测二进制marker的非常流行的库，是由Rafael Muñoz和Sergio Garrido完成的。

aruco的函数包含在c++：[链接地址](http://docs.opencv.org/3.1.0/d9/d53/aruco_8hpp.html)

```
#include <opencv2/aruco.hpp>
```

## Marker和字典

一个ArUco marker是一个二进制平方标记，它由一个宽的黑边和一个内部的二进制矩阵组成，内部的矩阵决定了它们的id。黑色的边界有利于快速检测到图像，二进制编码可以验证id，并且允许错误检测和矫正技术的应用。marker的大小决定了内部矩阵的大小。例如，一个4x4的marker由16bits组成。

一些ArUco markers的例子：



![markers.jpg](http://docs.opencv.org/3.1.0/markers.jpg)

应当注意到，我们需要检测到一个Marker在空间中发生了旋转，但是，检测的过程需要确定它的初始角度，所以每个角落需要是明确的，不能有歧义，保证上述这点也是靠二进制编码完成的。

markers的字典是在一个特殊应用中使用到的marker的集合。这仅仅是每个marker的二进制编码的链表。

字典的主要性质是字典的大小和marker的大小：

- 字典的大小是组成字典的marker的数量
- marker的大小是这些marker的尺寸（位的个数）

 aruco模块包含了一些预定义的字典，这些字典涵盖了一系列的字典大小和Marker尺寸。

有些人可能会认为Marker的id是从十进制转成二进制的。但是，考虑到较大的marker会有较多的位数，管理如此多的数据不那么现实，这并不可能。反之，一个marker的id仅需是marker在它所在的字典的下标。例如，一个字典里的五个marker的id是：0,1,2,3和4。

更多有关字典的信息在“选择字典”部分提及。



## 创建Marker

​    在检测之前，我们需要打印marker，以把它们放到环境中。marker的图像可以使用[drawMarker()](http://docs.opencv.org/3.1.0/d6/d6e/group__imgproc__draw.html#ga482fa7b0f578fcdd8a174904592a6250)函数生成。

​    例如，让我们分析一下如下的调用：

```c++
  cv::Mat markerImage; cv::aruco::Dictionary dictionary = cv::aruco::getPredefinedDictionary(cv::aruco::DICT_6X6_250);

  cv::aruco::drawMarker(dictionary, 23, 200, markerImage, 1);
```

首先，我们通过选择aruco模块中一个预定义的字典来创建一个字典对象，具体而言，这个字典是由250个marker组成的，每个marker的大小为6x6bits(DICT_6X6_250)

​     `drawMarker`的参数如下：

- 第一个参数是之前创建的字典对象。
- 第二个参数是marker的id，在这个例子中选择的是字典DICT_6X6_250第23个marker。注意到每个字典是由不同数目的Marker组成的，在这个例子中，有效的Id数字范围是0到249。不在有效区间的特定id将会产生异常。
- 第三个参数，200，是输出Marker图像的大小。在这个例子中，输出的图像将是200x200像素大小。注意到这一参数需要满足能够存储特定字典 的所有位。所以，举例而言，你不能为6x6大小的marker生成一个5x5图像（这还没有考虑到Marker的边界）。除此之外，为了避免变形，这一参数最好和位数+边界的大小成正比，至少要比marker的大小大得多（如这个例子中的200)，这样变形就不显著了。
- 第四个参数是输出的图像。
- 最终，最后一个参数是一个可选的参数，它指定了Marer黑色边界的大小。这一大小与位数数目成正比。例如，值为2意味着边界的宽度将会是2的倍数。默认的值为1。
- 生成的图像如下：

![marker23.jpg](http://docs.opencv.org/3.1.0/marker23.jpg)

​    详细的例子在模块演示文件夹中的 `create_marker.cpp` 

## 检测Marker

给定一个可以看见ArUco marker的图像，检测程序应当返回检测到的marker的列表。每个检测到的marker包括：

- 图像四个角的位置（按照原始的顺序）
- marker的Id

marker检测过程由以下两个主要步骤构成：

1. 检测有哪些marker。在这一阶段我们分析图像，以找到哪些形状可以被识别为Markers。首先要做的是利用自适应性阈值来分割marker，然后从阈值化的图像中提取外形轮廓，并且舍弃那些非凸多边形的，以及那些不是方形的。我们还使用了一些额外的滤波（来剔除那些过小或者过大的轮廓，过于相近的凸多边形，等）
2. 检测完marker之后，我们有必要分析它的内部编码来确定它们是否确实是marker。此步骤首先提取每个标记的标记位。为了达到这个目的，首先，我们需要对图像进行透视变换，来得到它规范的形态（正视图）。然后，对规范的图像用Ossu阈值化以分离白色和黑色位。这一图像根据marker大小和边界大小被分为不同格子，我们统计落在每个格子中的黑白像素数目来决定这是黑色还是白色的位。最终，我们分析这些位数来决定这个marker是属于哪个特定字典的，如果有必要的话，需要对错误进行检测。

考虑如下图像：

![singlemarkersoriginal.png](http://docs.opencv.org/3.1.0/singlemarkersoriginal.png)

这些是检测出来的marker（用绿色标记）

![singlemarkersdetection.png](http://docs.opencv.org/3.1.0/singlemarkersdetection.png)

Image with detected markers

以下是识别阶段被剔除的Marker候选（用粉红色标记）：

![singlemarkersrejected.png](http://docs.opencv.org/3.1.0/singlemarkersrejected.png)

在aruco模块，检测是由`detectMarkers()` 函数完成的，这一函数是这个模块中最重要的函数，因为剩下的所有函数操作都基于detectMarkers()返回的检测出的markers。

一个marker检测的例子：

```c++
[cv::Mat](http://docs.opencv.org/3.1.0/d3/d63/classcv_1_1Mat.html) inputImage;

vector< int > markerIds; 

vector< vector<Point2f> > markerCorners, rejectedCandidates; 

[cv::aruco::DetectorParameters](http://docs.opencv.org/3.1.0/d1/dcd/structcv_1_1aruco_1_1DetectorParameters.html) parameters; 

[cv::aruco::Dictionary](http://docs.opencv.org/3.1.0/d5/d0b/classcv_1_1aruco_1_1Dictionary.html) dictionary = cv::aruco::getPredefinedDictionary(cv::aruco::DICT_6X6_250); 

cv::aruco::detectMarkers(inputImage, dictionary, markerCorners, markerIds, parameters, rejectedCandidates); 
```

`detectMarkers` 的参数为：

- 第一个参数是待检测marker的图像。
- 第二个参数是字典对象，在这一例子中是之前定义的字典 (`DICT_6X6_250`).
- 检测出的markers存储在`markerCorners` 和 `markerIds`结构中：
  - `markerCorners` 是检测出的图像的角的列表。对于每个marker，将返回按照原始顺序排列的四个角（从左上角顺时针开始）。因此，第一个点是左上角的角，紧接着右上角、右下角和左下角。
  - `markerIds` 是在markerCorners检测出的所有maker的id列表.注意返回的markerCorners和`markerIds` 向量具有相同的大小。 
- 第四个参数是类型的对象 `DetectionParameters`. 这一对象包含了检测阶段的所有参数。这一参数将在 下一章节详细介绍。
- 最后的参数, `rejectedCandidates`, 返回了所有的marker候选, 例如， 那些被检测出来的不是有效编码的方形。每个候选同样由四个角定义， 它的 形式和markerCorners的参数一样。这一参数可以省略，它仅仅用于debug阶段，或是用于“再次寻找”策略（见[refineDetectedMarkers()](http://docs.opencv.org/3.1.0/d9/d6a/group__aruco.html#ga90374a799f1da566e5de16f277b12463)）

 `detectMarkers()`之后，接下来你想要做的事情可能是检查你的marker是否被正确地检测出来了。幸运的是，aruco模块提供了一个函数，它能在输入图像中来绘制检测出来的markers，这个函数就是[drawDetectedMarkers()](http://docs.opencv.org/3.1.0/d9/d6a/group__aruco.html#ga2ad34b0f277edebb6a132d3069ed2909) ,例子如下：

 ```c++
 cv::Mat outputImage cv::aruco::drawDetectedMarkers(image, markerCorners, markerIds);
 ```

- `image` 是输入/输出图像，程序将在这张图上绘制marker。（它通常就是检测出marker的那张图像）
- `markerCorners` 和 `markerIds` 是检测出marker的结构，它们的格式和 `detectMarkers()`函数提供的一样。

![singlemarkersdetection.png](http://docs.opencv.org/3.1.0/singlemarkersdetection.png)

 注意到这个函数仅仅用于可视化，而没有别的什么用途。

使用这两个函数我们完成了基本的marker识别步骤，我们可以从相机中检测出Marker了。

```c++
cv::VideoCapture inputVideo; 

inputVideo.open(0); 

cv::aruco::Dictionary dictionary = cv::aruco::getPredefinedDictionary(cv::aruco::DICT_6X6_250); 

while (inputVideo.grab()) 

{ 
    cv::Mat image, imageCopy;
    inputVideo.retrieve(image); 
    image.copyTo(imageCopy);
    std::vector<int> ids; std::vector<std::vector<cv::Point2f> > corners; 
    cv::aruco::detectMarkers(image, dictionary, corners, ids);
    // if at least one marker detected 
    if (ids.size() > 0) 
        cv::aruco::drawDetectedMarkers(imageCopy, corners, ids);
    cv::imshow("out", imageCopy); 
    char key = (char) cv::waitKey(waitTime); 
    if (key == 27) break; 

} 
```

​    注意到这里忽略了有些可选的参数，比如检测参数对象、以及输出的被剔除的候选对象向量。

​    完整的工程代码包含在模块样例文件夹中的`detect_markers.cpp` 



## Pose检测

接下来你想要做的应当是通过Marker检测来获取相机pose。

为了展现相机的Pose检测，你需要知道你的相机的校准（Calibration）参数。这是一个相机矩阵和畸变系数。如果你不知道如何校准你的相机，你可以看一看`calibrateCamera()` 函数，以及[OpenCV](http://lib.csdn.net/base/opencv)的校准教程。你同样可以使用aruco模块来校准你的相机，这在使用aruco进行校准的教程中将会介绍。注意这个过程只需要做一次，除非你的相机的光学性质发生了改变（例如调焦）

最后，在校准之后我们得到的是相机矩阵：这是一个3x3的矩阵，包含了焦距和相机中心坐标（相机的内参），以及畸变系数：一个包含五个以上元素的向量，它描述的是相机产生的畸变。

当你用ArUco marker来检测相机Pose时，你可以单独地检测每个Marker的pose。如果你想要从一堆Marker里检测出一个pose，你需要的是aruco板。（参见ArUco板教程）

涉及到marker的相机pose是一个从marker坐标系统到相机坐标系统的三维变换。这是由一个旋转和一个平移向量确定的（参见 `solvePnP()` 函数）

aruco模块提供了一个函数，用来检测所有探测到的Marker的pose。

```c++
Mat cameraMatrix, distCoeffs; 

... 

vector< Vec3d > rvecs, tvecs; 

cv::aruco::estimatePoseSingleMarkers(corners, 0.05, cameraMatrix, distCoeffs, rvecs, tvecs); 
```

- `corners` 参数是marker的角向量，是由`detectMarkers()` 函数返回的。
- 第二个参数是marker的大小（单位是米或者其它）。注意Pose检测的平移矩阵单位都是相同的。
- `cameraMatrix` 和 `distCoeffs` 是需要求解的相机校准参数。
- `rvecs` 和 `tvecs` 分别是每个markers角的旋转和平移向量。

 这一函数获取的marker坐标系统处在marker重心，Z坐标指向纸面外部，如下图所示。坐标的颜色为,X:红色，Y：绿色，Z：蓝色。

![singlemarkersaxis.png](http://docs.opencv.org/3.1.0/singlemarkersaxis.png)

aruco模块提供了一个函数绘制上图中的坐标，所以我们可以检查pose检测的正确性。

```c++
cv::aruco::drawAxis (image, cameraMatrix, distCoeffs, rvec, tvec, 0.1); 
```

- `image` 是输入/输出图像，坐标将会在这张图像上绘制（通常就是检测marker的那张图像）。
- `cameraMatrix` 和 `distCoeffs` 是相机校准参数。
- `rvec` 和 `tvec` 是Pose参数，指明了坐标绘制的位置。
- 最后一个参数是坐标轴的长度，和tvec单位一样（通常是米）。

针对一个marker的pose检测的基本的完整示例：

```c++
cv::VideoCapture inputVideo;
inputVideo.open(0);
cv::Mat cameraMatrix, distCoeffs; // camera parameters are read from somewhere 

readCameraParameters(cameraMatrix, distCoeffs);

cv::aruco::Dictionary dictionary = cv::aruco::getPredefinedDictionary(cv::aruco::DICT_6X6_250);

while (inputVideo.grab()) { 
	cv::Mat image, imageCopy;
	inputVideo.retrieve(image); 
	image.copyTo(imageCopy);
	std::vector<int> ids; 
	std::vector<std::vector<cv::Point2f> > corners; 
	cv::aruco::detectMarkers(image, dictionary, corners, ids);// if at least one marker detected 
	if (ids.size() > 0) { 
		cv::aruco::drawDetectedMarkers(imageCopy, corners, ids);
        vector< Mat > rvecs, tvecs; 
        cv::aruco::estimatePoseSingleMarkers(corners, 0.05, cameraMatrix, distCoeffs, rvecs, tvecs); // draw axis for each marker 
        for(int i=0; i<ids.size(); i++) 
        {
            cv::aruco::drawAxis(imageCopy, cameraMatrix, distCoeffs, rvecs[i], tvecs[i], 0.1); 
        }
        cv::imshow("out", imageCopy); 
        char key = (char) cv::waitKey(waitTime); 
        if (key == 27) break; 

} 
```

​    样例视频:

​    [![ArUco markers detection video](http://img.youtube.com/vi/IsXWrcB_Hvs/0.jpg)](https://youtu.be/IsXWrcB_Hvs)

​    完整的工程代码包含在模块样例文件夹中的 `detect_markers.cpp`

## 选择字典

aruco模块提供了Dictionary类来描述marker的字典。

除了marker大小和字典中的marker数目，字典还有一个很重要的参数，就是内部marker的距离。内部marker的距离是marker之间的最小距离，它决定了字典错误检测和纠正能力。

一般而言，较小的字典大小和较大的marker大小将会产生更大的内部marker距离，反之亦然。但是，过大的Marker在检测中更加困难，因为我们需要从图像中提取出更多位的信息。

 例如，如果你的应用中仅仅需要10个marker，最好使用只包含10个marker的字典，而不是包含1000个marker的字典。原因在于，由10个marker组成的字典将会有更大的内部Marker距离，因此，它的容错性更强。

结果，aruco模块包含了很多选择marker字典的途径，所以你可以让你的系统变得更加健壮。

- 预定义的字典：

这是选择字典最简单的办法。aruco模块包含了一系列预定义的字典，涵盖了不同的marker大小和marker数量。例如：

```c++
cv::aruco::Dictionary dictionary = cv::aruco::getPredefinedDictionary(cv::aruco::DICT_6X6_250); 
```

DICT_6X6_250 是一个预定义的字典，它包含6x6位的marker，总共有250个marker。

在所有提供的字典中，我们推荐使用你选择尽可能小的marker。例如，如果你需要6x6位的200个marker，选择DICT_6X6_250要优于选择DICT_6X6_1000。字典越小，内部距离就越大。

- 自动生成的字典:

我们可以针对想要的marker数量和位来生成字典，以得到最优的内部Marker距离。

```c++
cv::aruco::Dictionary dictionary = cv::aruco::generateCustomDictionary(36, 5); 
```

这将会产生一个由36个5X5位字典组成的标准字典。这个过程需要几秒钟，具体时间取决于你的参数（更大的字典和更多的位数会耗费更多的时间）

- 手动生成的字典:

最终，我们可以手动设置字典，方便做任何修改。为了做到这一点，我们需要手动给 `Dictionary` 对象参数赋值。必须注意的是，除非你有一些特别的理由需要自己来生成字典，一般情况下我们推荐前面的任一种方案。

字典参数为：

```c++
class Dictionary 
{ 
	public:
    	Mat bytesList; 
    	int markerSize; 
    	int maxCorrectionBits; // maximum number of bits that can be corrected
    	...

}
```

`    bytesList` 是一个数组，它包含了所有marker代码的信息。markerSize是每个marker的维度（例如，参数为5代表5x5位）。最终， `maxCorrectionBits`是marker检测中 可校正的最大比特数，如果这个值过大，会得到大量的错误位置。

 `bytesList` 中的每一行代表字典中的一个marker。但是，这些marker的数据并不以二进制形式存储，而是以一种特殊的方式存储，这样可以简化检测的过程。

幸运的是，我们可以简单地调用静态方法`Dictionary::getByteListFromBits()`来转换到这种形式。

示例：

```c++
Dictionary dictionary; 
// markers of 6x6 bits 
dictionary.markerSize = 6; 
// maximum number of bit corrections 
dictionary.maxCorrectionBits = 3;
// lets create a dictionary of 100 markers 
for(int i=0; i<100; i++)
{// assume generateMarkerBits() generate a new marker in binary format, so that 
// markerBits is a 6x6 matrix of CV_8UC1 type, only containing 0s and 1s 
	cv::Mat markerBits = generateMarkerBits(); 
	cv::Mat markerCompressed = getByteListFromBits(markerBits); // add the marker as a new row
	dictionary.bytesList.push_back(markerCompressed); 
 }
```

## 检测参数

 [detectMarkers()](http://docs.opencv.org/3.1.0/d9/d6a/group__aruco.html#ga306791ee1aab1513bc2c2b40d774f370)的一个参数是DetectorParameters对象。这一对象包含了marker检测过程中所有特定的选项。

在这一章节中，我们将介绍所有的参数。我们可以根据它们涉及的阶段来给这些参数分类。

#### 阈值化

检测的第一步是输入图像的阈值化。

例如，上述样例中的图像阈值化的结果如下：

![singlemarkersthresh.png](http://docs.opencv.org/3.1.0/singlemarkersthresh.png)

这一阈值化过程由以下几个参数决定：

- `int adaptiveThreshWinSizeMin`, `intadaptiveThreshWinSizeMax`, `int adaptiveThreshWinSizeStep`

​    `adaptiveThreshWinSizeMin` 和 `adaptiveThreshWinSizeMax` 参数代表选择的自适应阈值窗口大小（以像素为单位）间隔（具体参见[opencv](http://lib.csdn.net/base/opencv)的 `threshold()`函数）。

参数adaptiveThreshWinSizeStep表明了窗口从adaptiveThreshWinSizeMin到adaptiveThreshWinSizeMax大小的增量。

例如，对于adaptiveThreshWinSizeMin=5，adaptiveThreshWinSizeMax=21以及adaptiveThreshWinSizeStep=4，那么将会产生5个阈值化步骤，窗口大小分别为5, 9, 13, 17 和 21。在每个阈值化图像中，都会选出一些marker候选。

如果marker大小太大的话，较小的窗口大小可能会切割marker的边界，所以它将不会被检测到，就像下图一样：

![singlemarkersbrokenthresh.png](http://docs.opencv.org/3.1.0/singlemarkersbrokenthresh.png)

Broken marker image

另一方面，如果marker太小的话，较大的窗口大小也会有类似的效果。此外这一过程将会趋向于全局阈值，而失去了自适应的特性。

最简单的例子是对adaptiveThreshWinSizeMin和 `adaptiveThreshWinSizeMax`使用相同的值，这样就只会执行一次阈值化步骤。但是，最好还是使用一个范围的值作为窗口大小，虽然较多的阈值化步骤会在一定程度上降低性能。

缺省参数：`adaptiveThreshWinSizeMin`: 3, `adaptiveThreshWinSizeMax`: 23, `adaptiveThreshWinSizeStep`: 10

- `double adaptiveThreshConstant`

 这一参数表达了阈值状态下的常量（参见Opencv函数）。它的默认值是大多数例子下较好的情况。

默认值: 7

#### 轮廓滤波

阈值化之后，我们需要检测轮廓。但是，我们并不会把所有的轮廓都当作是候选。在不同步骤中，我们通过滤波剔除一些不太可能是marker的轮廓。这一章节中的参数可以自定义这一过程。

 需要注意到，大多数例子中我们需要平衡检测的性能和效率。所有考虑到的轮廓都会在接下来的过程中做进一步处理，这通常产生了更高的计算消耗。所以，我们希望能够在这一阶段就丢弃错误的候选，而不是下一阶段继续处理。

另一方面，如果滤波的条件过于苛刻，事实上的marker轮廓可能会被错误地剔除，因此，没有检测到marker。

- `double minMarkerPerimeterRate`, `doublemaxMarkerPerimeterRate`

 这些参数决定了marker的最小值和最大值，具体来说，是最大最小marker的周长。它们并不是以绝对像素值作为单位，而是相对于输入图片的最大尺寸指定的。

例如，大小为640x480，最小相对marker周长为0.05的图像，将会产生一个最小周长640x0.05 = 32(像素)的marker，因为640是图像的最大尺寸。参数 `maxMarkerPerimeterRate` 也是类似的。

 如果 `minMarkerPerimeterRate`太小，检测阶段性能会降低，因为会有更多的轮廓进入到接下来的阶段。这一弊端对于 `maxMarkerPerimeterRate`参数而言不是那么显著，因为小的轮廓数目通常要多于大的轮廓。选取 `minMarkerPerimeterRate`值为0以及值为4，就相当于考虑了图像中的所有轮廓，但是出于性能考虑这是不推荐的。

 缺省值：

Default values:`minMarkerPerimeterRate` : 0.03, `maxMarkerPerimeterRate` : 4.0



- `double polygonalApproxAccuracyRate`

我们对所有的候选进行多边形近似，只有近似结果为方形的形状才能通过[测试](http://lib.csdn.net/base/softwaretest)。这一值决定了多边形近似产生的最大误差（参见`approxPolyDP()` 函数）。

 这一参数是相对于候选长度的（像素上）。所以如果候选的周长为100像素，polygonalApproxAccuracyRate的值为0.04，那么最大的误差应当为100x0.04=5.4像素。

在大多例子中，缺省参数的表现已经很好了，但对高失真的图像，我们需要更大的误差值。

​    缺省值：0.05

- `double minCornerDistanceRate`

同一张marker中每一对角的最小距离。这是相对于marker周长的值。像素的最小距离为Perimeter * minCornerDistanceRate.

​    缺省值: 0.05

- `double minMarkerDistanceRate`

 两张不同的marker之间的任一对角的最小距离。它表示相对于两个marker的最小标记周长。如果两个候选太接近，较小的一个被忽略。

​    缺省值：0.05

- `int minDistanceToBorder`

marker角到图像边缘最小距离。部分图像边缘被遮挡的marker也能被正确地检测出来，如果遮挡部分比较小的话。但是，如果其中一个角被挡住了，返回的角通常在图像边界的一个错误的位置。

如果marker角的位置很重要的话，例如你想要做pose检测，最好舍弃掉那些离图像边缘太接近的角。否则就没有必要。

​    缺省值：3

#### 比特位提取

检测到候选之后，我们需要分析每个候选的比特位，来确定它们是不是marker。

在分析二进制编码之前，我们需要提取出比特位。为了达到这个目的，将对透视变换后的图像使用Otsu进行阈值化，来分离黑色和白色像素。

以下是一个透视变换后的图像：

![removeperspective.png](http://docs.opencv.org/3.1.0/removeperspective.png)

接下来，图像被划分为网格，和marker位数相同。在每个单元格里，我们统计黑色和白色的个数，决定这个单元格的比特位。

![bitsextraction1.png](http://docs.opencv.org/3.1.0/bitsextraction1.png)

以下参数可以自定义这一过程：

- `int markerBorderBits`

这一参数指定了marker边界的宽度。这和每个比特位的大小相关。因此，值为2意味着边界的长度是两个内部比特位的长度。

这一参数需要和你使用的Marker边界大小一致，边界的大小可以在绘制函数如 `drawMarker()`中设置。

​    缺省值：1

- `double minOtsuStdDev`

这个值决定了进行Otsu的最小标准差的像素值。如果偏差很低，这可能意味着所有方形都是黑色的(或白色的），Ostu将不起作用。如果是这样的话，所有的比特位都根据平均值大于还是小于128被设为0或者1.

​    缺省值：5.0



- `int perpectiveRemovePixelPerCell`

这一参数决定了透视变换后图像的像素数目（每个单元格，包含边界）。这是上图中红色正方形的大小。

例如，让我们假设我们在处理5x5比特位、边界为1比特位的marker（参见markerBorderBit）。然后，每一维的单元格/比特位的个数为：5 + 2* 1 = 7（边界需要被统计2次）。单元格总体大小为：7x7。

如果perpectiveRemovePixelPerCell的值为10，那么获取到的图像大小为10*7 = 70 -> 70x70 

这一参数选择更大的值可以提升比特位的提取过程（在某一程度上），但是它同样也降低了性能。

​    缺省值：4



- `double perspectiveRemoveIgnoredMarginPerCell`

当提取每个单元格的比特位时，需要统计黑色和白色的像素个数。一般而言，我们不推荐考虑单元格的所有像素。反之，最好忽略单元格的一些像素。

原因在于，透视变换之后，单元格的颜色不会完全分离，白色的单元格可能会混入一些黑色的单元格（反之亦然）。因此，最好忽略这些像素，以避免错误的像素计数。

例如，以下图像：

![bitsextraction2.png](http://docs.opencv.org/3.1.0/bitsextraction2.png)

我们只考虑处在绿色正方形中的像素。我们可以在右边的图像中看到，最终的像素包含了邻域单元格更少的噪声。参数`perspectiveRemoveIgnoredMarginPerCell` 指明了红色和绿色正方形之间的距离。

这一参数是相对于单元格整体的大小的。例如，如果单元格的大小为40像素，这一参数的值为0.1，那么大小为40*0.1=4像素的边界将被剔除。这意味着每个单元格实际上要分析的像素大小为32x32，而不是40x40。

​     缺省值：0.13



#### Marker ID

比特位提取之后，接下来的步骤是检查提取的编码是否属于这个marker字典，有必要的话，还需要做错误检测步骤。

- `double maxErroneousBitsInBorderRate`

 marker边界的比特位应当是黑色的。这一参数指明了允许的边界出错比特位的个数。如，边界可以出现的白色比特位的最大值。它的大小相对于marker中的比特位总数。

​    缺省值：0.35



- `double errorCorrectionRate`

每个marker字典有一位可以纠正的理论最大值（Dictionary.maxCorrectionBits）。但是，这个值可以由`errorCorrectionRate` 参数来修改。

 例如，如果允许纠正的比特位（对于使用的字典）数目为6， `errorCorrectionRate`的值为0.5，那么实际上最大的可以纠正的比特位个数为6*0.5=3

这一值对减少错误容忍率以避免错误的位置识别很有帮助。

​    缺省值：0.6



#### 角落细化（Corner Refinement）

当我们检测完marker，并且验证了它们的id之后，最后要做的一步是在角落处的亚像素级的细化（参见OpenCV `cornerSubPix()`)

注意，这一步是可选的，仅在我们对marker角位置的准确性要求很高时才有意义。例如，pose的检测。这一步骤很耗费时间，所以默认下是不做的。

- `bool doCornerRefinement`

这一参数决定了是否要进行角落亚像素级细化过程，如果对角点的准确性要求不高，可以不进行这一过程。

​    缺省值：false



- `int cornerRefinementWinSize`

这一参数决定了亚像素级细化过程的窗口大小。

较大的值可以产生窗口区域内比较靠近的图像角，marker角会移动到一个不同的错误的地方。除此之外这还会影响到性能。

​    缺省值：5



- `int cornerRefinementMaxIterations`, `doublecornerRefinementMinAccuracy`

这两个值决定了亚像素级细化过程的结束条件。cornerRefinementMaxIterations指明了迭代的最大次数，`cornerRefinementMinAccuracy` 是结束这一过程前的最小错误值。

如果迭代次数过高，这会影响到性能。此外，如果太小的话，亚像素级细化就基本没有发挥作用。

​    缺省值：

`cornerRefinementMaxIterations`: 30, `cornerRefinementMinAccuracy`: 0.1