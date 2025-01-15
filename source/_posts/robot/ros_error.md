---
title:  【ros】 编译时的问题
categories: [学术]
tags:   [Robot, Ros, Note]
copyright: false
reward: false
rating: false
related_posts: false
date: 2020-07-19 23:34:55
---

ros_ethercat/ros_ethercat_loop/CMakeFiles/ros_ethercat_loop.dir/build.make:62: recipe for target 'ros_ethercat/ros_ethercat_loop/CMakeFiles/ros_ethercat_loop.dir/src/main.cpp.o' failed
make[2]: *** [ros_ethercat/ros_ethercat_loop/CMakeFiles/ros_ethercat_loop.dir/src/main.cpp.o] Error 1

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")


The kinematics plugin (rh_first_finger) failed to load. Error: Failed to load library /home/sunshine/ros/shadow_demo/base/devel/lib//libhand_kinematics.so. Make sure that yo
u are calling the PLUGINLIB_EXPORT_CLASS macro in the library code, and that names are consistent between this macro and your XML. Error string: Could not load library (Poco exception = libmoveit_kinematic
s_base.so.0.9.15: cannot open shared object file: No such file or directory)


问题不在于kinematics 插件， 问题在于moveit版本