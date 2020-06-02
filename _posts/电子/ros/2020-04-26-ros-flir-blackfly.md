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
ifconfig xxx 169.254.67.yyy
```

> `xxx`为网卡名，`169.254.67.yyy`为ip。（ip必须为这个域下）（或在`/etc/network/interfaces`中配置）

- 3 使用spinview查看：

```sh
sudo spinview
```


## 接入到ros

[spinnaker_sdk_camera_driver][2]

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

[烫手][5]


# 错误处理

`Camera not detected`: 

[spinnaker sdk][6]

> - 原因：使用spinnaker sdk 2.0版本会出现的问题，因为对s/n获取方式变了，导致spinnaker_sdk_camera_driver对s/n获取失败。
> - 解决：`git checkout dev`切换到目前后并到的dev中。 （[探讨问题][3] -> [解决部分问题][4]）

`frame id was zero`： 

目前只找到注释`camera.cpp`第59行（master分支）的临时方法。（或设置为`==1`，因为输出发现该值为1）


`NEW_BUFFER_DATA`:

增大UDP缓存区（25Mb）。（有改善，并不稳定）[解决方案][9]

```sh
sudo sysctl -w net.core.rmem_max=26214400
sudo sysctl -w net.core.rmem_default=26214400
```

将`acquisition.launch`中的`binning`参数设置为2或4（有时只允许设置为2），降低分辨率可以保持一定稳定。[全彩高分辨率问题(供电问题？)][7]

`图像上下翻转`：

> 在切换到spinnaker sdk 2.0以后，图像不再颠倒。

~~切换到`dev`分支，在`params/test_params.yaml`中`flip_horizontal`和`flip_vertical`，并设置为`true`。[image flip][8]~~


---

[1]: https://flir.app.boxcn.net/v/SpinnakerSDK 
[2]: http://wiki.ros.org/spinnaker_sdk_camera_driver
[3]: https://github.com/neufieldrobotics/spinnaker_sdk_camera_driver/issues/76
[4]: https://github.com/neufieldrobotics/spinnaker_sdk_camera_driver/pull/84
[5]: http://softwareservices.flir.com/BFS-PGE-31S4/latest/Family/Temperature.htm
[6]: https://flir.app.boxcn.net/v/SpinnakerSDK/folder/74729015802
[7]: https://github.com/neufieldrobotics/spinnaker_sdk_camera_driver/issues/63#issuecomment-565573504
[8]: https://github.com/neufieldrobotics/spinnaker_sdk_camera_driver/pull/41
[9]: https://github.com/neufieldrobotics/spinnaker_sdk_camera_driver/issues/10#issuecomment-461545543
