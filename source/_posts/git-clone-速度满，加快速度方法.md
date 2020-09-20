---
title: git clone 速度满，加快速度方法
date: 2020-09-19 21:40:37
tags:
---

# git clone 速度满，加快速度方法

> 转载于
>
> https://www.cnblogs.com/PieDaoChuan/p/9773039.html

## 设置方法

### 方法1

```
git config --global https.proxy http://127.0.0.1:1080
git config --global https.proxy https://127.0.0.1:1080
```

### 方法2

```
git config --global http.proxy 'socks5://127.0.0.1:1080'
git config --global https.proxy 'socks5://127.0.0.1:1080'
```

**注意：端口号1080为VPN设置的代理端口，可自行修改为自己的VPN代理端口**

有人使用第一种无加速效果，第二种代理方式速度得到明显提升，峰值可达1M/s。

### 取消git代理设置

```
git config --global --unset http.proxy
git config --global --unset https.proxy
```

