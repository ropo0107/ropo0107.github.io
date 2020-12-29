---
title:  【robot】 Kinect_dk 在Ubuntu16.04 下安装并配置ROS
categories: [工程]
tags:   [Robot, ROS]
copyright: false
reward: false
rating: false
related_posts: false
date: 2020-08-30 23:34:55
---
# 引子
最近手头拿到了 Kinect dk 相机， 苦于自己的电脑是16.04， 于是根据网上资料自己配置了一下， 发现有许多坑， 并没有说到， 自己边安装，边记录下内容吧。默认不支持16.04

https://github.com/ropo0107/kinect_dk

放上自己的工程目录， 可以自取

## SDK

### git

- git clone -b v1.2.0 https://github.com/microsoft/Azure-Kinect-Sensor-SDK.git
- mkdir kinect_ws/src && cd kinect_ws/src
- git clone https://github.com/RoseFlunder/Azure_Kinect_ROS_Driver.git

### dependence
- sudo cp ./libdepthengine.so.2.0 /usr/lib/x86_64-linux-gnu/
- sudo ./Azure-Kinect-Sensor-SDK/scripts/bootstrap-ubuntu.sh
- sudo apt-get install gcc-4.9
- sudo apt-get upgrade libstdc++6

### cmske -v 大于 3.9

### build
- cd build
- cmake .. -GNinja
- ninja
- sudo ninja install

- ERROR：
    ```
    **CMake Error: CMake was unable to find a build program corresponding to “Ninja”. **
    ```

    - Solution：install ninja
        - sudo apt-get install re2c
        - git clone https://github.com/ninja-build/ninja.git
        - cd ninja
        - ./configure.py --bootstrap
        - sudo cp ./ninja  /usr/bin
        - ninjia --version -- 1.10.1.git


    ```
    Cloning into 'extern/azure_c_shared/src'...                                                           
    fatal: BUG: initial ref transaction called with existing refs                                         
    fatal: clone of 'https://github.com/Azure/azure-c-shared-utility.git' into submodule path 'extern/azur
    e_c_shared/src' failed                                                                                
    CMake Error at extern/CMakeLists.txt:22 (message):                                                    
    git submodule update failed:
    ```
    - Solution:缺少依赖的库
      - cd extern/azure_c_shared
      - git clone https://github.com/Azure/azure-c-shared-utility.git

      - cd extern/libuv
      - git clone https://github.com/lemenkov/libyuv
      - mv libuv src

### without root
- sudo cp scripts/99-k4a.rules /etc/udev/rules.d/


### k4aviewer
```
k4aviewer: error while loading shared libraries: libk4a.so.1.2: cannot open shared object file: No such file or directory
```
- sudo ln -s /usr/local/lib/libk4a.so.1.2 /usr/lib/libk4a.so.1.2

## ROS

- cd kinect_ws/src/Azure_Kinect_ROS_Driver
- mkdir ext/sdk/
- dependence:
    在https://packages.microsoft.com/ubuntu/18.04/prod/pool/main/libk/里面找到

    libk4a1.2
    k4a1.2-dev/
    文件下载下来并解压，

    在sdk中放入三个文件夹：bin、include、lib，位置关系如下：
    ```
    .\ext\sdk\
            bin\
            include\
            lib\
    ```
    其中，

    bin文件夹: 把编译SDK时build文件夹里面的那个bin文件夹拷贝过来

    include文件夹：SDK源码里的include文件夹和从-dev.deb文件里提取出的include文件夹合并，拷贝过来

    lib文件夹：从deb文件里提取出的两个lib文件夹合并，拷贝过来


- ERROR:
    ```
    k4a_ros_device.h:166:8: error: ‘atomic_int64_t’ in namespace ‘std’ does not name a type
    std::atomic_int64_t last_capture_time_usec_;
    ```
    - Solution: 找到对应文件
        ```
        // line:165
        // Last capture timestamp for synchronizing playback capture and imu thread
        std::atomic<int64_t- last_capture_time_usec_;

        // Last imu timestamp for synchronizing playback capture and imu thread
        std::atomic<uint64_t- last_imu_time_usec_;
        std::atomic<bool- imu_stream_end_of_file_;
        ```
