---
title:  使用ubuntu的小tips
categories: [杂记]
tags:   [ubuntu,linux]
copyright: false
reward: false
rating: false
related_posts: false
date: 2020-03-04 02:51:55
---


# 常用的 linux 配置

## zsh

## tmux 


# 常用的 linux 命令

## 后台运行命令

 - 第一种 &
  
    关闭终端停止运行   
  
        command &
 
 - 第二中 nohup   
    关闭终端停止运行,如果你正在运行一个进程，而且你觉得在退出帐户时该进程还不会结束，那么可以使用nohup命令。该命令可以在你退出帐户之后继续运行相应的进程。nohup就是不挂起的意思( no hang up)。 该命令的一般形式为：

        nohup conmmand &
    如果使用nohup命令提交作业，那么在缺省情况下该作业的所有输出都被重定向到一个名为nohup.out的文件中，除非另外指定了输出文件：

         nohup command > myout.file 2>&1

    在上面的例子中，输出被重定向到myout.file文件中。


### 安装有道词典

一直出现：
```        
    ModuleNotFoundError: No module named 'PyQt5.QtWebKitWidgets    
```
找了好多方法并没有解决。

    1) 检查有用 APT 安装上 PyQt5
    ----------------------------------------
    $ apt search python3-pyqt5 | grep installed
    python3-pyqt5/bionic,now 5.10.1+dfsg-1ubuntu2 amd64 [installed]
    python3-pyqt5.qtmultimedia/bionic,now 5.10.1+dfsg-1ubuntu2 amd64 [installed]
    python3-pyqt5.qtquick/bionic,now 5.10.1+dfsg-1ubuntu2 amd64 [installed]
    python3-pyqt5.qtwebkit/bionic,now 5.10.1+dfsg-1ubuntu2 amd64 [installed]

    确保 PyQt5 以上安装在 /usr/lib/python3/dist-packages/PyQt5

    1) PIP3 上卸载 PyQt5 (目前最新是 5.12.1)
    ------------------------------------------------------
    $ su
    $ apt install python3-pip
    $ pip3 list | grep PyQt5

    如有列出 PyQt5, 
    再查一查这个 PyQt5 是否安装在 /usr/local/lib/python3.6/dist-packages
    $ pip3 show PyQt5

    如是, 撤除 PyQt5 和 PyQt5-sip.
    $ pip3 uninstall PyQt5 PyQt5-sip

    1) 打开新的 terminal, 运行 (不用 $PYTHONPATH)
    ----------------------------------------------------------------
    $ youdao-dict