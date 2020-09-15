---
title: 关于Windows10设置Windows terminal 当前目录打开
date: 2020-09-15 20:36:42
tags:
- Windows terminal
---

# 关于Win10 设置Windows terminal 的特定目录打开设置

> 参考博客:
>
> https://blog.csdn.net/andyL_05/article/details/105943820
>
> 以下内容全部来自参考博客，如有侵权，请联系我删除，感谢

## Windows Terminal 简介

Windows Terminal 是微软推出的Windows端新的命令行工具，相比于之前一直被吐槽丑的command 和 powershell，Windows Terminal更加“现代”，界面好看，使用方便。

作为一款开源工具，repo位于[Windows Terminal](https://github.com/Microsoft/Terminal)这里。当前（2020年5月初）版本为v0.11.1251.0，也是v1.0rc1，基本上就是预览版（preview）的最后一个版本了，在源码仓库里时间表上也可以看到
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200506092739206.png)
预计就是本月推出正式版的windows terminal了。
以往的预览版通常无法直接安装，需要从源码自行编译。目前已经可以直接从Microsoft Store里直接安装，不过要求系统版本
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200506092952865.png)
在1903或更新。

---

## Windows Terminal设置简要介绍

安装后直接打开的Windows Terminal是这个样子：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200506093128723.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FuZHlMXzA1,size_16,color_FFFFFF,t_70)注意到“Windows Terminal”这个名字实在是太长了，所以其实运行的可执行文件叫做 “wt”，按win键启动开始菜单输入 wt 就可以直接打开。
首先我们注意到字体就比cmd或者powershell好看了很多
点击右侧下拉菜单，可以选择新建其他的命令行，这里只是显示了默认的几个，实际上通过设置可以进行ssh连接到远程主机（不限于win系统）
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200506093355250.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FuZHlMXzA1,size_16,color_FFFFFF,t_70)
设置是通过setting.json实现的。
打开设置文件，可以看到一系列的设置选项：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200506093906551.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FuZHlMXzA1,size_16,color_FFFFFF,t_70)
微软也有完善的文档支持这些设置，[使用者文档（user documentation）](https://github.com/microsoft/terminal/blob/master/doc/user-docs/index.md)

以及 [setting.json文档](https://github.com/microsoft/terminal/blob/master/doc/cascadia/SettingsSchema.md)

可以参考完成设置，我这里就不多说了。
简单说一下default里面的设置，就是默认打开的终端设置，这里不光可以设置背景颜色，字体等，还可以选择自定义的图片作为背景，比如我就设置了：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020050609424670.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2FuZHlMXzA1,size_16,color_FFFFFF,t_70)
这样的背景，感觉使用起来还是蛮有感觉的。

list内部就是自定义的profiles，可以配置打开不同的终端时的设置。guid就是全局唯一标识符，可以从工具自动生成。其他设置参考文档中的设置即可。

背景的独立设置，不仅美观，而且能够帮助我们直观的区分所处的环境，避免输入错误的命令导致严重后果。

---

## Windows Terminal 当前路径打开

熟悉cmd或powershell的朋友们应该知道，如果在文件夹地址栏直接输出 cmd 或 powershell 就可以在文件夹所处路径直接打开命令行终端，而不需要启动后再切换路径，对于Windows系统来说，由于磁盘分区、中文路径、反斜杠等因素，切换路径比起linux更让人烦一点。
除此之外，通过修改注册表也能够使用shift+右键选择此处启动powershell，这些都是快速在当前路径启动命令行的方式，那么Windows Terminal能不能这么做呢？

测试发现直接在文件夹地址栏输入wt（因为这个可执行文件就是wt.exe而不是完整名字）确实启动了Windows Terminal，但是初始路径位置并不是当前的文件夹。这里参考我们上面的设置，default里增加一行 “startingDirectory”: “.”，也就是打开目录为当前路径，就可以做到地址栏输入WT在特定目录启动Windows terminal了。

不过这里仍然有一些**问题**：
1· 首先如果不加这一行或者不对startingDirectory设置，那么默认启动路径是C:\Users\username这里，无论是双击wt.exe还是地址栏还是开始菜单启动都是这样，设置了之后默认位置就变成了C:\WINDOWS\system32这里，除了地址栏输入wt能够在特定目录启动外，其他运行Windows Terminal的方式一律在这里打开了。
2· 另外如果已经有Windows Terminal的窗口打开了，再次启动或者通过地址栏运行wt都会新建一个窗口而不是原来窗口的新标签页，并且标签页不能像浏览器一样拖进拖出；同时在某个路径下打开WT的窗口，点击右边“+”新建的标签页一定在同一个路径。一开始发现这个有点不爽，但是仔细想想可能这样做更实用一些，如果所有终端页面都是可拖动的标签页也难免混淆。
以上就是特定目录启动wt的一种方法，setting.json增加相应设置，然后地址栏输入wt按下回车即可。另外在文件夹中，快捷键 ctrl+L将焦点锁定地址栏，这样完全不需要鼠标，听起来有点geek了。

除此之外，通过自己建立注册表，也可以实现类似powershell的右键打开方式，具体方法参考[这个issue](https://github.com/microsoft/terminal/issues/1060)

由于我本人更喜欢地址栏打开方式，右键打开的配置就不赘述了，通过上述链接查看即可。