---
title: '[paper] YOLO9000: Better, Faster, Stronger'
date: 2020-07-02 10:00:00
categories: deep learning
tags:
    - ai
    - deep learning
    - paper
mathjax: true
---


# 背景

`yolo v1`的出现提供了一个`one stage`的算法，同年没多久就被`SSD`等在准确率和速度上欺负。时隔一年多，作者基于时代的进步，加入了多个先进的工作，使用多个trick`取长补短`优化模型。

# 效果

1.优化yolo模型。

2.预测超9000类别。

『官方实验』

非常理想的在各方面远超其它模型。

![effect](/imgs/deep_learning/paper/paper-YOLO-v2/2.png)


# 网络

『网络结构』

![net](/imgs/deep_learning/paper/paper-YOLO-v2/1.png)

# 思路

yolo v1主要的缺点：

1. 与Fast R-CNN做误差分析，yolo v1有大量的定位误差。
2. 与region proposal方式比，yolo v1的recall比较低。

『10个trick』

![Path from YOLO to YOLOv2](/imgs/deep_learning/paper/paper-YOLO-v2/3.png)


## 1.batch norm?

> 2.4% mAP ↑

用于解决`internal covariate shift`问题，防止梯度消失、加速收敛等。

作用：

1. 提升收敛速度
2. 正则化模型

有正则化效果，就可以不使用dropout了。

## 2.hi-res classifier?

> 3.7% mAP ↑

**不足：**因为yolo v1的分类网络是`224x224`的，而detector网络是`448x448`，这意味着大于`224x224`分辨率的图片分类，分类器并没有在更高的分辨率中获得好处。

**优化：**先让**分类网络**在以`224x224`分辨率训练，再以`448x448`分辨率训练10个epoch进行调整。

## 3.convolutional?

> convolutional + anchor boxes => 0.3% mAP ↓
>
> recall ↑

移除全连接层，采用卷积预测。使用全卷积后，输入的图片不再固定，可以任意变化，只要大于步长。

输出的分辨率从64倍缩小，变为32倍。输出分辨率从`7x7`变为了`13x13`。由此，输入分辨率也调整为`416x416`。

## 4.anchor boxes?

> convolutional + anchor boxes => 0.3% mAP ↓
>
> recall ↑

**不足：**yolo v1是让每个cell预测那片的坐标，不像Faster R-CNN哪样预测的是`anchor box`的偏移。

**优化：**也采用`anchor box`方式。

1. 预测偏移比简单的预测坐标更容易使网络学习。
2. 从先预测目标是否存在后，再分类。 -> 为每个anchor box都预测类别和对象存在与否。
3. yolo v1只预测98个盒子（`7x7x2`），采用anchor box后，预测超千个。（解决了1代无法检测密集目标的问题）


## 5.new network?

> 0.4% mAP ↑

大多detector使用`VGG-16`，它是强力的，精确的分类器，但没必要那么复杂。

yolo v2使用基于`GoogleNet`的自制网络，比`VGG-16`快，参数更少，精度稍微差点。

使用全卷积分类，19层conv + 5层maxpool。

> 最终，top-5准确率高于VGG-16和GoogleNet...

## 6.dimension priors?

> dimension priors + location prediction => 4.8% mAP ↑

手选的anchor box尺寸，虽然网络会学习调整预测的box去变得合适，但好的`anchor box`的挑选,能让网络学习更容易，检测更好。

yolo v2采用`k-means`从数据集中挑选合适的`anchor box`尺寸。

而且，不使用标准的欧几里德距离的k-means，因为越大的box比小盒产生的更大的误差，而想要的IOU的值。故采用IOU评估。

以下k的选取，k=5是模型复杂度和recall的交易，通过k-means挑选出来尺寸，有更多矮、宽、高、瘦的box。

『anchor box的尺寸和数量选择』

![Clustering box dimensions on VOC and COCO](/imgs/deep_learning/paper/paper-YOLO-v2/5.png)


## 7.location prediction?

> dimension priors + location prediction => 4.8% mAP ↑

约束到区域的预测，参数的学习更容易。

『边界盒坐标的计算方式』

![Bounding boxes with dimension priors and location
prediction](/imgs/deep_learning/paper/paper-YOLO-v2/6.png)

> ($c_x$, $c_y$)是bounding box的左上角的坐标。

## 8.passthrough?

> 1% mAP ↑

将最后一个pool之前，`26x26x512`大小的特征图，reshape到`13x13x2048`，passthrough到分类卷积（filter数量为1000）之前的特征图`13x13x1024`合并为`13x13x3072`。

## 9.multi-scale?

> 1.4% mAP ↑

只使用卷积和池化后，网络能运行不同的分辨率。

每训练10代就，随机选取一个新的分辨率。

因为下采样是32倍，图片尺寸也选用32的倍数。{320, 352, ..., 608}

## 10.hi-res detector?

> 1.8% mAP ↑

分辨率越高，mAP越高，FPS越低。

『不同输入分辨率的图片的mAP和FPS』

![different resolutions](/imgs/deep_learning/paper/paper-YOLO-v2/4.png)
