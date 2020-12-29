---
title: docker中的tips
categories: [编程]
tags:   [docker, Note]
copyright: false
reward: false
rating: false
related_posts: false
date: 2020-12-120 02:51:55
---

# docker 中添加串口设备
## 方法一：
    --device /dev/ttyUSB0:/dev/ttyUSB0
    --cap-add 
    一般这种方式没有权限， 加cap-add添加权限

## 方法二：
    --privileged
    -v /dev/bus/usb:/dev/bus/usb
    会给docker读取本地虽有设备的权限，一般不会这样用

# docker 添加相机等设备
