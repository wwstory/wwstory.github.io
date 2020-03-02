---
title: '[ros] 远程连接'
date: 2020-01-02 15:00:00
categories: 'ros'
tags:
    - 电子
    - ros
---

# 远程节点

为了远程接入roscore，只需要简单配置环境变量。

**master：**

`ROS_HOSTNAME`设置为执行roscore主节点的ip：
```sh
export ROS_HOSTNAME=192.168.0.100
export ROS_MASTER_URI=http://${ROS_HOSTNAME}:11311
```

**other computer：**

仅接受master的数据：
```sh
export ROS_MASTER_URI=http://192.168.0.100:11311
```
如果需要发送数据到master，需要指定自身的ip：
```sh
export ROS_MASTER_URI=http://192.168.0.100:11311
export ROS_HOSTNAME=192.168.0.101
```

# 远程摄像头
**master：**

按照上述远程节点的方式配置后，在master上执行：
```sh
roscore
rosrun uvc_camera uvc_camera_node
```

**other computer：**

安装uvc摄像头相关库：
```sh
sudo apt install ros-melodic-uvc-camera
```
插入uvc摄像头，赋予读取设备权限：
```sh
sudo chmod 777 /dev/ttyUSB0
```
在其它计算机上执行：
```sh
rosrun image_view image_view image:=/image_raw
```
或使用rqt可视化。

