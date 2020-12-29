---
title:  【gpd】复现GPD
categories: [学术]
tags:   [Robot]
copyright: false
reward: false
rating: false
related_posts: false
date: 2020-11-17 23:34:55
---

# environment
源码编译opencv3.4.0， 和pcl1.9.1

## error1
错误描述:

    ./detect_grasps error while loading shared libraries: libopencv_highgui.so.3.4: cannot open shared object file: No such file or directory

源码编译后链接库文件在 **/uer/local/lib**

首先看看这个文件需要哪些库
```bash
$ ldd detect_grasps
```
这时候会看到有一些库没有连接到。

## solution
那些not　found的就是找不到的库拉。很明显这些都是的库，这说明我们在安装的时候，忘记把他配置进去了。接下来，我们定位一下这些库的位置。
```bash
$ locate libopencv_highgui.so.3.4
```

在/etc/ld.so.conf.d目录下新建一个opencv.conf文件并将其内容写入刚才找到的库的路径。

```bash
$ sudo vim opencv.conf
```
里面添加
```
/usr/local/lib
```
保存后执行

```bash
sudo ldconfig
```
