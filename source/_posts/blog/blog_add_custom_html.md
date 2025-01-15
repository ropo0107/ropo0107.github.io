---
title: 【blog】 HEXO添加自定义HTML静态网页
categories:
  - 杂记
tags: blog
copyright: false
reward: false
rating: false
related_posts: true
date: 2020-03-08 08:23:55
---

## 添加自定义HTML静态网页

今天发现一个好玩的毒鸡汤界面,打开了新世界的大门.


## 新建个page

```
hexo new page soup
```

在source 会生成一个
    
    soup/index.md

修改index.md为

    ---
        layout : false
    ---

在主配置文件_config.yml下修改,该过程不会渲染soup文件夹下内容,直接将soup文件夹下内容copy到publi文件夹下.

    skip_render: soup/**  

在theme/next/_config.yml文件添加page

    menu:
      home: /
      tags: /tags
      categories: /categories
      archives: /archives
      about: /about
      soup: /soup


## 静态html文件

将html文件放在/source/soup/目录下

## Done!
如果有好玩的网页,可以通过搞到网页index.html加到自己博客中.

html文件与之前写的 **urdf** 和 **xacro** 文件.风格和 **css** **html** 及其类似.