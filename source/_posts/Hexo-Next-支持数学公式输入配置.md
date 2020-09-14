---
title: Hexo-Next-支持数学公式输入配置
date: 2020-04-02 18:20:59
tags:
- Hexo
- Next
---

忘记是哪个版本适用来着

<!--more-->

###  1. 使用Kramed 代替 Marked

渲染引擎kramed支持mathjax

```
npm uninstall hexo-renderer-marked --save
npm install hexo-renderer-kramed --save
```

打开:博客根目录`/node_modules/hexo-renderer-kramed/lib/renderer.js`
将下面这几句进行更改

```
// Change inline math rule
function formatText(text) {
  // Fit kramed's rule: $$ + \1 + $$
  return text.replace(/`\$(.*?)\$`/g, '$$$$$1$$$$');
}
```

更改为

```
// Change inline math rule
function formatText(text) {
  // Fit kramed's rule: $$ + \1 + $$
    return text;
}
```

### 2. 使用hexo-renderer-mathjax 代替 hexo-math

```
npm uninstall hexo-math --save
npm install hexo-renderer-mathjax --save
```

### 3. 更新 Mathjax 的 CDN 链接

打开:`博客根目录/node_modules/hexo-renderer-mathjax/mathjax.html`
将最下面<script src=后的url改为

```
https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.1/MathJax.js?config=TeX-MML-AM_CHTML
```

### 4. 更改默认转义规则

打开:`博客根目录/node_modules/kramed/lib/rules/inline.js`

```
//escape: /^\\([\\`*{}\[\]()#$+\-.!_>])/,      第11行，将其修改为
escape: /^\\([`*\[\]()#$+\-.!_>])/,
//em: /^\b_((?:__|[\s\S])+?)_\b|^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,    第20行，将其修改为
em: /^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,
```

### 5. 开启mathjax

在主题的配置文件中,我用的是next主题,那么在其_config.yml中找到mathjax并设置为true

```
# MathJax Support
mathjax:
  enable: true
  per_page: true
  cdn: //cdn.bootcss.com/mathjax/2.7.1/latest.js?config=TeX-AMS-MML_HTMLorMML
```

之后再写文章时,加上mathjax: true

```
---
date: 2017/8/3 18:20:00
tags: hexo
mathjax: true
title: hexo博客
---
```

### 参考资料

> https://blog.csdn.net/littlehaes/article/details/84370393
>
> https://andyvj.coding.me/2019/02/26/190226-02/