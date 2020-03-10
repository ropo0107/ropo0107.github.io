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

## 后台运行

 - 第一种 &
  
    关闭终端停止运行   
  
        command &
 
 - 第二中 nohup   
    关闭终端停止运行,如果你正在运行一个进程，而且你觉得在退出帐户时该进程还不会结束，那么可以使用nohup命令。该命令可以在你退出帐户之后继续运行相应的进程。nohup就是不挂起的意思( no hang up)。 该命令的一般形式为：

        nohup conmmand &
    如果使用nohup命令提交作业，那么在缺省情况下该作业的所有输出都被重定向到一个名为nohup.out的文件中，除非另外指定了输出文件：

         nohup command > myout.file 2>&1

    在上面的例子中，输出被重定向到myout.file文件中。

