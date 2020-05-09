---
title: '[ros] realsense'
date: 2019-01-06 18:50:00
categories: 
    - 'ros'
tags:
    - '电子'
    - 'ros'
---

# 安装

安装ros：
http://wiki.ros.org/melodic/Installation/Ubuntu

安装realsense sdk：
https://github.com/jetsonhacks/installRealSenseSDK
处理`ddynamic_reconfigure`问题：
```sh
sudo apt install ros-melodic-ddynamic-reconfigure
```
处理`CMake Error cv_bridgeConfig.cmake:113`问题：
https://github.com/ros-perception/vision_opencv/issues/209

安装realsense-ros：
https://github.com/IntelRealSense/realsense-ros


---

- 安装realsense sdk：https://www.intelrealsense.com/developers/
- 在ros使用：https://github.com/IntelRealSense/realsense-ros#installation-instructions
- 错误处理[CMake Error. Could not find."ddynamic_reconfigure"]：
- - https://github.com/IntelRealSense/realsense-ros#installation-instructions . `Specifically, make sure that the ros package ddynamic_reconfigure is installed.`
- - https://www.cnblogs.com/LoveBuzz/p/11568628.html
- xavier安装realsense： https://github.com/jetsonhacks/installRealSenseSDK
