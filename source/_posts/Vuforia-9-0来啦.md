---
title: Vuforia 9.0来啦
date: 2020-04-28 23:46:47
tags:
---

来了，Vuforia 9.0来了！

<!--more-->

转载自 https://unity.cn/projects/lai-liao-vuforia-9-0lai-liao

[原文链接：https://blogs.unity3d.com/2020/04/21/augment-your-spaces-with-vuforia-engine/](https://blogs.unity3d.com/2020/04/21/augment-your-spaces-with-vuforia-engine/)

在新版本中，可以使用Area Target（区域型目标）轻松地在大型环境中制作沉浸式增强现实（AR）体验。

日常的生活、工作和购物空间中包含大量的价值信息，但要获取特定语境的相关数据会比较困难。目前基于对象的增强现实（AR）技术仅能在视场中展示物体，并不具备处理大型空间的能力。

为了让制作大型增强现实体验成为现实，Vuforia Engine 9.0推出了新的Area Target功能。Area Target可让你在扫描而成的空间3D模型中制作巨大、连贯的AR体验，在选择区域中持续识别、跟踪场景内容，形成一种持续性的增强效果。制作成果可在各个手机、平板和AR头显上播放。

<iframe width="100%" height="100%" src="https://unity.cn/player/5ea7a942edbc2a002026cef2" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen="" __idm_frm__="11" style="box-sizing: border-box; font-family: PingFangSC, Roboto, &quot;Noto Sans SC&quot;, SimSun, sans-serif;"></iframe>

Area Target体验制作

制作Area Target体验的流程可总结为：扫描、创作和查看。流程可大致分为以下五步：

👉第一步：

扫描场景空间：Area Target经由真实空间的3D扫描模型制作而来。Vuforia Engine的最初版本可以使用Matterport Pro2 camera来抓拍扫描数据。

👉第二步：

创建一个Area Target：在3D模型扫描完成后，将数据导入Vuforia的Area Target Generator（区域性目标生成器），就能生成对应的Vuforia数据集。数据集包含一个带数据集文件的Unity包和扫描空间的纹理网格，可直接导入到Unity项目中。

👉第三步：

导入Area Target：在Unity中安装设置好Vuforia Engine，然后在GameObject下拉菜单中导入Area Targets文件。若想修改文件的配置，可参考Area Targets in Unity的详细指南。

![img](https://connect-cn-cdn-public-prd.unitychina.cn/h1/20200428/p/images/2c98786f-7ce6-41dd-8d63-b962e5d68823_image.png)

👉第四步：

创作：如上图，在导入完成后，开发者可以查看详细的扫描3D模型，可以在环境中放置3D对象，使用播放模式来测试体验。播放模式可让开发者“演练”一遍3D模型，在电脑上观看AR体验的最终效果。如果某个空间不方便实地查看，可使用播放模式来测试应用。

👉第五步：

查看：在部署完Area Target应用后，你就能从场景中的任何位置启动增强应用了——启动操作简单明了。当使用者在空间中移动时，3D增强效果会被持续追踪，且因为采用了扫描数据，还能被真实物体遮挡。

通过沉浸式AR将人们连接在一起

Area Target功能不仅强大，还可用于商场、旅馆和工厂等各种场景，用庞大、持续性的AR体验提供独特的商业价值。

企业可将整个商店作为画布来制作AR体验，轻松展示或修改促销、发售或导购体验。专业客服可在对应语境中提供说明，如让顾客学会使用某些电器。体验可用不同语言进一步拓展，来支持不同国家的顾客。

在工业中，该技术可为前线工人提供实时、沉浸式的体验，让工作更加安全、简便。某些任务或设备的导览数据可部署到整个设施中，引导工人完成工作。此类方法可节省新员工培训的时间和成本，引导现场技术人员，协助工人在多个流程和设备间流畅地过渡。

Vuforia Engine 9.0已于3月18日发布，推出了更多高级的AR功能。

如果希望马上尝试Vuforia Area Target功能，可参阅How to Create Area Targets说明页面。

https://library.vuforia.com/content/vuforia-library/en/features/environments/area-targets/how-to-create-area-targets.html