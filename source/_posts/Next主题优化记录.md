---
title: Next主题优化记录
date: 2020-04-02 18:21:25
tags:
- Hexo
- Next
---

经典主题！

<!--more-->

### 名词说明

- 站点配置文件：指文件所在路径为：根目录\\_config.yml 
- 主题配置文件：指文件所在路径为：根目录\themes\next\\_config.yml 

### 配置1：语言与基本信息更改

改变博客的语言为中文，博客标题，博客作者

- 打开站点配置文件
- 找到代码并修改如下：

```
# Site
title: JunLuBlog #要修改的内容区域
subtitle: ''
description: ''
keywords:
author: JunLu #要修改的内容区域
language: zh-Hans #要修改的内容区域
timezone: ''
```

### 配置2：主题样式修改

Next主题提供了多种样式以供选择

- 打开主题配置文件
- 修改代码如下

```
# Schemes
#scheme: Muse
#scheme: Mist
scheme: Pisces 若选择此样式，将其前面的#去掉即可
#scheme: Gemini
```

### 配置3：菜单设置

在博客顶端需要有标签，分类，关于等菜单栏

- 在根目录开启 `Git Bash Here`
- 命令：`hexo new page "tags"`
- 命令：`hexo new page "categories"`
- 命令：`hexo new page "about"`
- 进入路径：根目录\\source，可以看到新建了分类，标签，关于的文件夹
- 打开主题配置文件，修改代码如下：

```
menu:
  home: /|| home
  about: /about/|| user
  tags: /tags/|| tags
  categories: /categories/|| th
  archives: /archives/|| archive
  #schedule: /schedule/ || calendar
  #sitemap: /sitemap.xml || sitemap
  #commonweal: /404/ || heartbeat
```

PS：一丁点空格也不许错

### 配置3 ：侧边栏

Next主题提供了侧边栏的左右放置方向

- 打开主题配置文件
- 修改代码如下：

```
sidebar:
  # Sidebar Position, available value: left | right (only for Pisces | Gemini).
  #position: left
  position: right
```

### 配置4： 开启动态背景

- 打开主题配置文件
- 修改代码如下：

```
# Canvas-nest
canvas_nest: true
```



### 参考

> http://theme-next.iissnan.com/