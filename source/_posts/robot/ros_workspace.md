---
title:  【ros】 workspace and namespace
categories: [学术]
tags:   [Robot, Ros, Note]
copyright: false
reward: false
rating: false
related_posts: false
date: 2020-07-19 23:34:55
---

一直想把之前ros的坑给补上， 最近终于有机会了
# 工作空间
## 定义
工作空间(workspace)是存放工程开发相关文件的文件夹。现ROS默认使用Catkin编译系统。
典型的工作空间包括四部分：
- src ： 放源代码文件
- build ： 放编译中的缓存和中间文件
- devel ： 放编译生成的可执行文件
- install ： （非必需）

## 工作空间的相互依赖
- 若想查看所有ROS相关的环境变量： 
    ```bash
    env | grep ros
    ```
- 若想查看ROS_PACKAGE_PATH：
    ```bash
    echo $ROS_PACKAGE_PATH 
    ```
- 激活相应工作空间
    ```bash
    #根据自己启用的shell工具决定， bash用sh，zsh用zsh
    soucrce {workspace}/devel/setup.sh

    soucrce {workspace}/devel/setup.zsh
    ```
- 工作空间编译依赖问题:
    - 默认的工作空间,即使用 sudo apt-get install ros-kinetic-desktop-full, 安装的默认位置为:
        
        /opt/ros/kinetic/setup.zsh

    - 激活默认环境:
        ```bash
        source /opt/ros/kinetic/setup.zsh
        ```
    - 查看环境变量
        ```bash
        echo $ROS_PACKAGE_PATH 
        
        #output
        /opt/ros/kinetic/share     
        ```
    - 激活默认环境后激活要用的工作空间， 这儿我的是 ur_ws
        ```bash
        # compile
        source /opt/ros/kinetic/setup.zsh
        cd ~/ros/ur_ws/
        catkin_make
        
        # use new workspace
        source ~/ros/ur_ws/devel/setup.zsh
        echo $ROS_PACKAGE_PATH
        
        # output                         
        /home/sunshine/ros/ur_ws/src:/opt/ros/kinetic/share

        ```
        新的workspace在前面，被依赖的workspace在后面 如果两个公共空间有重名的功能包，调用新的工作空间的功能包。

    - 这儿有个问题，如果接下来要用的工作空间arm_ws继续依赖前面两个工作空间呢
        ```bash
        # compile
        source source ~/ros/ur_ws/devel/setup.zsh
        cd ~/ros/arm_ws/
        catkin_make

        echo $ROS_PACKAGE_PATH
        
        # output                         
        /home/user/ros/arm_ws/src:/home/user/ros/ur_ws/src:/opt/ros/kinetic/share

        # use arm_ws
        source source ~/ros/arm_ws/devel/setup.zsh
        ```
## Note
- 工作空间的依赖只在编译时有用，
    - **c++:** 因为大部分程序采样 **c++** 编写， c++为编译型语言， 故修改c++程序后需要重新编译工作空间
    - **python:** 为解释型语言，如果修改部分为python语言，则不需要重新编译工作空间
  
- 在使用相应工作空间的时候，需要用source激活，可以用alias来简化
    ```bash
    alias ros='source /opt/ros/kinetic/setup.zsh'
    ```

# namespace
在实际的ROS网络中，各个节点、话题、消息和参数的名称必须是唯一的，不然就会发生冲突。

因此最简单的两种方法，来区分相同名字的资源：

- 给两个名字前加上定语，就是 **添加命名空间**；
- 给两个名字取个不同的别名，就是 **重映射**；

