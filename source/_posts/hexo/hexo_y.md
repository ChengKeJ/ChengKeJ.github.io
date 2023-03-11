---
layout: post
title:  "hexo 博客加载优化"
categories: hexo
tags:  hexo
keywords: hexo,RTT,TCP滑动窗口,Zero Window
date: 2022/12/24 19:28:25
---
# hexo 博客加载优化

### 背景

最近趁着周末折腾自己博客的时候,发现刷页面的时候会有点卡顿,感觉页面性能很低,所以开始了下面的优化,分析下来结果还不错比之前快了很多,那让我们开始吧!

![Image.png](https://res.craft.do/user/full/d3f184bd-2fc6-ae80-c9ed-d9b26e0cd7ba/doc/1050E4F2-AB1F-423B-8DE0-393C1A892684/3E8FC5EB-EEB0-4C2D-94EC-C948F99002CA_2/D2DBPCbNGLqxlHxMmGlIfcTaOf8xxt6pIZxuqnXNyMwz/Image.png)

简单的来说主要以下几个部分的优化:

- **压缩静态资源,提高访问速度**
- **图片懒加载**

<!--more-->
### 压缩静态资源

安装插件：

```other
npm install hexo-neat --save
```

然后我们需要在站点配置文件_config.yml 中添加以下代码：

```other
# 博文压缩
neat_enable: true
# 压缩html
neat_html:
  enable: true
  exclude:
# 压缩css
neat_css:
  enable: true
  exclude:
    - '**/*.min.css'
# 压缩js
neat_js:
  enable: true
  mangle: true
  output:
  compress:
  exclude:
    - '**/*.min.js'
    - '**/jquery.fancybox.pack.js'
    - '**/index.js'
    - '**/fireworks.js'
```

### 图片懒加载

安装插件:

```other
npm install hexo-lazyload-image --save
```

_config.yml 中添加以下代码:

```other
# 图片懒加载
lazyload:
  enable: true
  onlypost: false
  loadingImg: /images/loading.gif
```




