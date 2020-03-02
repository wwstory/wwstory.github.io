---
title: '[arduino] rosserial'
date: 2019-12-29 18:16:00
categories: 'arduino'
tags:
  - '电子'
  - 'arduino'
---

# 软件安装
下载arduino：
```sh
sudo apt install arduino
```

安装rosserial：
```sh
sudo apt install ros-melodic-rosserial ros-melodic-rosserial-server ros-melodic-rosserial-arduino ros-melodic-rosserial-python
```

为了在Arduino中使用它,需要创建一个用于Arduino的rosserial库：
```sh
cd ~/Arduino/libraries/
rm -rf ros_lib
rosrun rosserial_arduino make_libraries.py .
```


# arduino
## subscriber
arduino接受发布在/led_out的数据，控制pin8引脚的电平开关。
### 硬件
使用的是arduino pro mini 328p版，发现168p版本存储不够。

![ros_sub](/imgs/电子/arduino/arduino-rosserial/1.png)

> DTR用于在上传程序时自动RESET板子，否则需要在上传时手动按一下板子上的RESET按键（时机需要自己把握，比较麻烦）。

### 代码
```c++
#include <ros.h>
#include <std_msgs/Byte.h>

void process(const std_msgs::Byte& led_msg){
  digitalWrite(8, led_msg.data);
}

ros::NodeHandle nh;
ros::Subscriber<std_msgs::Byte> sub("led_out", process);

void setup() {
  // put your setup code here, to run once:
  pinMode(8, OUTPUT);

  nh.initNode();
  nh.subscribe(sub);
}

void loop() {
  // put your main code here, to run repeatedly:
  nh.spinOnce();
}
```

> arduino上传程序需要/dev/ttyUSB0的权限。
```sh
sudo chmod 777 /dev/ttyUSB0
```

### 测试
- 启动ros服务
- 启动rosserial服务
- 查看msg数据
- 发送数据

```sh
roscore

rosrun rosserial_python serial_node.py _port:=/dev/ttyUSB0

rostopic echo /led_out

rostopic pub -1 /led_out std_msgs/Byte 1
```

发送数字1打开小灯（非1即可），发送数字0关闭。

> `rosserial_python`可通过_port指定设备，通过_baud指定波特率（书中是115200并不能成功连接，改为57600后成功，后改为不设置可以自动设置）。
> rostopic pub <topic-name> <topic-type> [data...]
> `rostopic pub`中-1是执行一次。

[rosserial](http://wiki.ros.org/rosserial/)
[rostopic](http://wiki.ros.org/rostopic)

## publisher
用引线将VCC/GND输入pin9，将pin8输出电平值。

arduino发送pin9引脚的值到/led_out。

### 硬件
![ros_pub](/imgs/电子/arduino/arduino-rosserial/2.png)

### 代码
```c++
#include <ros.h>
#include <std_msgs/Byte.h>

ros::NodeHandle nh;
std_msgs::Byte led_msg;
ros::Publisher pub("led_out", &led_msg);

void setup() {
  // put your setup code here, to run once:
  nh.initNode();
  nh.advertise(pub);

  pinMode(8, OUTPUT);
  pinMode(9, INPUT);
}

void loop() {
  // put your main code here, to run repeatedly:
  uint8_t reading = 0;
  static uint32_t pre_time;

  reading = digitalRead(9);
  digitalWrite(8, reading);
  
  if(millis() - pre_time >= 50){
    led_msg.data = reading;
    pub.publish(&led_msg);
    pre_time = millis();
  }

  nh.spinOnce();
}
```

### 测试
- 启动ros服务
- 启动rosserial服务
- 查看msg数据
- 输入电平到输入引脚

```sh
roscore

rosrun rosserial_python serial_node.py _port:=/dev/ttyUSB0

rostopic echo /led_out
```

将引脚VCC/GND引到pin9。

在pc，`rostopic echo`命令的终端可以看到来自板子的/led_out数据。
