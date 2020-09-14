---
title: Hexo静态博客配置
date: 2020-04-02 18:20:28
tags:
- Hexo
---

博客搭建第一步！

<!--more-->

### 前提准备

1. github 账号。

2. ~~购买一个域名的钱~~

   [免费域名传送门](https://www.freenom.com/zh/index.html?lang=zh)

---

### 搭建过程

#### github 创建仓库

- 登录 github 账号，[传送门](https://github.com/)

- Start Project

  [![G0YxHI.png](https://s1.ax1x.com/2020/04/04/G0YxHI.png)](https://imgchr.com/i/G0YxHI)

- 在Repository name中输入新建仓库的名称，yourname.github.io，点击Create Repository

  [![G0YvDA.png](https://s1.ax1x.com/2020/04/04/G0YvDA.png)](https://imgchr.com/i/G0YvDA)

---

#### 安装git

- 下载安装地址：
  - 官网：[传送门](https://gitforwindows.org/)
  - 蓝奏云：[传送门](https://www.lanzous.com/iazxt2j)
- 一路默认安装即可

---

#### 配置git

> 为了把本地的仓库中的内容传输到GitHub上，需要配置ssh key，无论是上传自己的博客还是上传其他的仓库都需要连接Github，ssh key是一个token，作用是身份验证。

- **打开Git Bash，输入命令：**（email是注册Github时使用的邮箱）

  ```
  ssh-keygen -t rsa -C "email"
  ```

- 输入上面的命令后**按Enter键**，Git Bash会提示: 

	```
	Enter passphrase <empty for no passphrase>:_
	```

- **直接按 Enter**，Git Bash会在默认路径C:\users\用户名.ssh下生成几个文件。然后Git Bash又给出提示：

	```
	Enter passphrase <empty for no passphrase>:_
	```

> 这是在提示我们输入密码，直接回车表示不设置密码

- **直接回车**，然后Git Bash要求我们重复密码，也**直接回车**，然后就会提示ssh key已经生成。
- **进入到默认的地址**中查看ssh key文件

  [![G0YOjH.png](https://s1.ax1x.com/2020/04/04/G0YOjH.png)](https://imgchr.com/i/G0YOjH)

- 使用记事本或者其他软件**打开 id_rsa.pub 文件，复制全部内容**
- 浏览器**进入 github ，进入博客仓库**

  [![G0YLge.png](https://s1.ax1x.com/2020/04/04/G0YLge.png)](https://imgchr.com/i/G0YLge)

​		[![G0tpUP.png](https://s1.ax1x.com/2020/04/04/G0tpUP.png)](https://imgchr.com/i/G0tpUP)

- **点击设置**

  [![G0t94f.png](https://s1.ax1x.com/2020/04/04/G0t94f.png)](https://imgchr.com/i/G0t94f)

- 在页面左侧**找到Deploy keys并点击**

  [![G0tPC8.png](https://s1.ax1x.com/2020/04/04/G0tPC8.png)](https://imgchr.com/i/G0tPC8)

- 点击右侧的**Add deploy key**按钮，title填什么都可以，Key中**粘贴**我们刚刚在ssh key文件中复制的key；点击Add Key，**保存ssh key**

  [![G0tFgg.png](https://s1.ax1x.com/2020/04/04/G0tFgg.png)](https://imgchr.com/i/G0tFgg)

  ​		[![G0tSEt.png](https://s1.ax1x.com/2020/04/04/G0tSEt.png)](https://imgchr.com/i/G0tSEt)

- 在本地验证ssh key设置是否成功，**打开Git Bash，输入命令**：

  ```
  ssh -T git@github.com
  ```

  **回车**后，如果看到

  ```
  `You’ve successfully authenticated, but GitHub does not provide shell access`
  ```

  表示当前已经成功地连接上了自己的GitHub账户。

- 接下来，设置一下自己的用户名和邮箱，**输入以下命令：**（name 和 email 每个人不同）

  ```
  git config --global user.name "my name"
  git config --global user.email "my email"
  ```

- 到此为止，我们已经成功地将个人电脑和Github账号连接。

---

#### 安装node.js

- 下载

  - 官网： [传送门](https://nodejs.org/en/)
  - 城通（win）：[传送门](https://474b.com/file/14976410-435186222)

- 按照需要选择不同的版本，下载到本地之后，**直接双击安装，一路默认**

- 检验是否安装成功：

  打开windows的控制台，**输入命令：**

  ```
  node -v
  ```

- 电脑若显示当前安装的node.js的版本，则安装成功

---

#### 安装Hexo

- 在电脑的磁盘里**新建一个空文件夹**，如E:\HexoB，用于存放本地仓库

- 在**windows控制台中输入命令**，跳转至D:\GIT\hexotext；代码如下：

	```
	cd E:\HexoB
	```
	
- 安装Hexo，**输入命令：**

  ```
  npm install hexo -g
  ```

- 检查Hexo是否安装成功，**输入命令：**

  ```
  hexo -v
  ```

---

#### 本地测试

- 初始化 E:\HexoB 这个文件夹，打开Git Bash进入此目录，**输入命令：**

  ```
  hexo init
  ```

- 安装Hexo所需要的组件，**输入命令：**

  ```
  npm install
  ```

- 产生webapp文件，**输入命令：**

  ```
  hexo g
  ```

- 开启服务器，**输入命令：**

  ```
  hexo s
  ```

- 此时控制台提示：

	```
	INFO Start processing
	INFO Hexo is running at http://localhost:4000/. Press Ctrl+C to stop.
	```

- **浏览器输入**地址：localhost:4000 预览

- **新建markdown文件**，保存为.md文件到本地，并移至E:\HexoB\source\\_posts中

- 和上次一样，打开的Git Bash，**执行以下命令**：

  ```
  hexo clean 
  hexo g 
  hexo s
  ```

- 打开浏览器，访问：localhost:4000，此时可以看到刚刚发布的内容

---

#### 连接Hexo和Github Pages及部署博客

- 打开 E:\HexoB 中的**_config.yml文件**，找到deploy字段，改为如下内容

```
deploy:
    type: git
    repository: git@github.com:someurl/someurl.github.io.git
    branch: master
```

>  git@github.com:someurl/someurl.github.io.git 是博客仓库的地址，获取方式是登录GitHub，找到自己的博客的仓库
>
> [![GBEbB4.png](https://s1.ax1x.com/2020/04/05/GBEbB4.png)](https://imgchr.com/i/GBEbB4)

- 现在将Hexo部署到GitHub的仓库里

- 在仓库中点击**Settings，往下拉，找到GitHub Pages**

  [![GBETjU.png](https://s1.ax1x.com/2020/04/05/GBETjU.png)](https://imgchr.com/i/GBETjU)

- Source选择 **master branch**

- 在 E:\HexoB 中打开Git Bash，**输入命令：**

  ```
  npm install hexo-deployer-git --save
  ```

- **输入命令：**

  ```
  hexo d -g
  ```

- 部署完成之后，在浏览器中输入地址 www.yourname.github.io，这个地址就是在repository的Setting页面中看到的那个，就可以访问自己的博客啦，以后写了新的博客，保存成md文件放到source\_post文件夹中，执行

```
hexo clean
hexo d -g
```

- 就可以发布到GitHub上啦

---

### 购买域名并解析

- 已腾讯云为例，[传送门](https://cloud.tencent.com/)

[![834tYj.png](https://s1.ax1x.com/2020/03/15/834tYj.png)](https://imgchr.com/i/834tYj)

- 点击 产品->域名注册

[![835CNQ.png](https://s1.ax1x.com/2020/03/15/835CNQ.png)](https://imgchr.com/i/835CNQ)

- 选择一个域名进行购买
- 购买后进入 控制台
- 进入域名管理界面
- 首先，实名认证
- 成功后，解析

[![83ozB6.png](https://s1.ax1x.com/2020/03/15/83ozB6.png)](https://imgchr.com/i/83ozB6)

- 记录值的获取方式：
  - 打开windows控制台，ping www.yourname.github.io
  - 将IP地址填写进入记录值
- 在本地的D:\GIT\hexotext\source中新建一个名为CNAME的文件，内容则是刚刚申请的域名，然后重新发布
- 到此成功

### 可能出现的问题

- 对于nmp工具
  - 更换淘宝源`npm install --registry=https://registry.npm.taobao.org`
  - 安装cnmp `$ npm install -g cnpm --registry=https://registry.npm.taobao.org`

- https://blog.csdn.net/zyupupup/article/details/85098366

### 参考文献

- https://blog.csdn.net/qq_34229391/article/details/82251852
- https://blog.csdn.net/zyupupup/article/details/85098366