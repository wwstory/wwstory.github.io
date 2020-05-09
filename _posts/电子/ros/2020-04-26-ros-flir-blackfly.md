---
title: '[ros] flir blackfly'
date: 2020-04-22 17:30:00
categories: 
    - 'ros'
tags:
    - '电子'
    - 'ros'
---


# 安装

## 确定设备可连接

- 1 安装sdk：

[spinnaker sdk][1]

- 2 配置ip：

```sh
ifconfig xxx yyy
```

> `xxx`为网卡名，`yyy`为ip。（或在`/etc/network/interfaces`中配置）

- 3 使用spinview查看：

```sh
sudo spinview
```


## 接入到ros

- 1 安装依赖：

```sh
sudo apt install libunwind-dev ros-${ROS_DISTRO}-cv-bridge ros-${ROS_DISTRO}-image-transport
```

- 2 安装spinnaker驱动：

```sh
mkdir -p ~/spinnaker_ws/src
cd spinnaker_ws/src
git clone https://github.com/neufieldrobotics/spinnaker_sdk_camera_driver.git
cd ~/spinnaker_ws/
catkin_make
source ~/spinnaker_ws/devel/setup.bash
```

- 3 配置，修改`./params/test_params.yaml`中的`cam_ids`和`master_cam`匹配对应的相机sn（贴在相机上的）。

- 4 运行：

```sh
roslaunch spinnaker_sdk_camera_driver acquisition.launch
```

- 5 查看：

```sh
rqt_image_view
```

> 显示为黑白，可以通过`acquisition.launch`（优先级更高）或`test_params.yaml`文件中修改`color`属性，更改为彩色。

# 示例（python）

示例程序：`spinnaker_python-2.0.0.109-Ubuntu18.04-cp36-cp36m-linux_x86_64/Examples/Python3/AcquireAndDisplay.py`


---

[1]: https://flir.app.boxcn.net/v/SpinnakerSDK 

- ros flir：http://wiki.ros.org/spinnaker_sdk_camera_driver
- 官网：https://www.flir.com/products/blackfly-s-gige/
- sdk：https://flir.app.boxcn.net/v/SpinnakerSDK （不使用USB3或GigE：https://flir.app.boxcn.net/v/Flycapture2SDK）

- IEEE-1588精确时间协议（PTP）：https://www.flir.com/discover/iis/machine-vision/precision-system-synchronization-with-the-ieee-1588-precision-time-protocol-ptp/
- 技术文档：https://www.flir.com/support-center/iis/machine-vision/knowledge-base/technical-documentation-bfs-gige/
- Spinnaker-ARM：https://www.flir.com/support-center/iis/machine-vision/application-note/using-spinnaker-on-arm-and-embedded-systems/
- Spinnaker-Nodes：https://www.flir.com/support-center/iis/machine-vision/application-note/spinnaker-nodes/

- 机器学习相机：https://www.flir.com/discover/iis/machine-vision/

- IEEE-1588原理：https://www.flir.com/globalassets/discover/machine-learning/precision-system-synchronization-with-the-ieee-1588-precision-time-protocol-ptp/timing-ieee-1588-ptp.gif
- 其它相机：https://www.flir.com/discover/iis/machine-vision/how-to-build-a-deep-learning-classification-system-for-less-than-$1000/