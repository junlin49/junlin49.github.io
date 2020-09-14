---
title: Unity-场景切换
date: 2020-04-02 18:27:47
tags:
- Unity
---

本文记录如何在Unity中实现场景切换

<!--more-->

代码如下：

```
public void Click()
{
      Invoke("ChangeScene", 1);  # 1s后切换场景，延时作用
}
 
void ChangeScene()
{
      SceneManager.LoadScene("场景名字");  // 登陆成功则切换到游戏界面
}
```



### 加载当前场景

比如点击重新开始按钮后，重新开始游戏。

```
SceneManager.LoadScene(SceneManager.GetActiveScene().name);
```

注意该语句执行的时候，仍会执行`update`函数。因此在`update`中置位标志位要多加留意。

### 参考资料

> https://www.jianshu.com/p/fb688b80986b?tdsourcetag=s_pcqq_aiomsg