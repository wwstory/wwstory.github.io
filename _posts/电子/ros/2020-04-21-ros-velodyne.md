---
title: '[ros] velodyne'
date: 2020-04-21 17:30:00
categories: 
    - 'ros'
tags:
    - '电子'
    - 'ros'
---


# 安装

## 连接传感器

- 1 LIDAR连接到电脑的以太网端口
- 2 禁用电脑的wifi连接
- 3 设置电脑的ip（除了201） `sudo ifconfig enp4s0 192.168.1.100`
- 4 添加静态ip：`sudo ifconfig eth0 192.168.3.100`
- 5 添加静态路由到LIDAR的ip地址：`sudo route add 192.168.1.201 eth0` (这里设置的是默认ip地址，多个传感器可设其它值)
- 6 检查是否成功，打开浏览器输入传感器的网络地址`192.168.1.201`，显示页面内容。

## 安装ros包

- 1 安装ros依赖：`sudo apt-get install ros-${ROS_DISTRO}-velodyne`
- 2 安装驱动：`sudo apt-get install ros-${ROS_DISTRO}-velodyne-driver`

## 使用

- 1 查看数据：

```sh
roslaunch velodyne_pointcloud VLP16_points.launch
rostopic echo /velodyne_points
```

- 2 rviz查看：

```sh
rosrun rviz rviz -f velodyne
```

在`displays`面板添加`Point Cloud2`，在其属性`Topic`选择`/velodyne_points`。

---

# 错误处理

**no fixed frame**：
```sh
rosrun tf static_transform_publisher 0 0 0 0 0 0 1 map velodyne 10
```

**DriverNodelet::devicePoll - Failed to poll device.**：由于launch文件的端口号与设备的不同，通过页面，修改`Data Port`一致（这里修改的是`2368`可用）（并未永久修改）。

[issues](https://github.com/ros-drivers/velodyne/issues/232#issuecomment-546900070)

---

- 官方资料：http://wiki.ros.org/velodyne/Tutorials
- 可视化工具veloview：https://www.paraview.org/VeloView （开源：https://github.com/Kitware/VeloView）
- ros vlp16：http://wiki.ros.org/velodyne/Tutorials/Getting%20Started%20with%20the%20Velodyne%20VLP16