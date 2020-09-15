---
title: GitHub上传项目文件
date: 2020-09-15 20:36:32
tags:
- git
---

# 在GitHub上，上传项目文件

小白第一次在GitHub上上传文件

<!--more-->

---

## 步骤

1. 新建仓库
2. 将仓库下载到本地
3. 把要上传的项目文件夹，放进下载的仓库文件夹中
4. 使用以下代码push到GitHub：

```
git add .        
git commit  -m  "提交信息"  
git push -u origin master   
```

---

## 问题

对于体积较大的项目文件，可能会遇到如下错误：

```
fatal: the remote end hung up unexpectedly Everything up-to-date
```

**错误原因**：

文件太大

**解决办法** :

> 转载自https://blog.csdn.net/qq_21770005/article/details/78733008

- windows:

在 .git/config 文件中加入

```
[http]
postBuffer = 524288000
```

- linux:

在 .git/config 文件中加入

```
linux:
git config http.postBuffer 524288000
```



