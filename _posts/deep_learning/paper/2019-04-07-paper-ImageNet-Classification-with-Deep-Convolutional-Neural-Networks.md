---
title: '[paper] ImageNet Classification with Deep Convolutional Neural Networks'
date: 2019-04-07 00:00:00
categories: deep learning
tags:
    - ai
    - deep learning
    - paper
mathjax: true
---


# 背景
- 全连接的神经网络需要消耗大量计算资源。
- 全连接难以应用到较高分辨率的图片。


# 效果
![effect](/imgs/deep_learning/paper/paper-ImageNet-Classification-with-Deep-Convolutional-Neural-Networks/2.png)


# 贡献
- 在ImageNet子集上，训练了一个最大的卷积神经网络用于ILSVRC-2010和ILSVRC-2012比赛，获得最好成绩。
- 写了高优化的GPU实现2D卷积操作。
- **网络使用许多新的方法改善，并减少训练时间。**
- **使用有效的技术防止过拟合。**


# 网络
![net](/imgs/deep_learning/paper/paper-ImageNet-Classification-with-Deep-Convolutional-Neural-Networks/1.png)

- 5层卷积
- 3层全连接
- 卷积后每层使用1层最大化池化层
- 每层使用ReLU(Rectified Linear Units)激活
- LRU处理第1层（后来的论文证明无用）

> ps:这里的网络，是将训练拆分成2部分，在2个GPU上跑，在第3层又映射到一起，后又拆分。


# 思路
- 1.卷积，ReLU激活，池化。(5层)
- 2.全连接，ReLU激活，softmax分类。


# 细节
- 1.线性激活和非线性(ReLU Nonlinearity)
- - 非线性的$f(x)=\tanh (x)$或$f(x)=\left(1+e^{-x}\right)^{-1}$饱和后梯度下降变慢。
- - ReLU训练速度更快。
- 2.多GPU训练(Training on Multiple GPUs)
- 3.局部响应归一化(Local Response Normalization) - (2015 VGG指出LRN没有提升)
- - 在通道的维度上做LRN。
- 4.减少过拟合(Reducing Overfitting)
- - 数据增强(Data Augmentation)：将256x256图片从4个角和中间裁减，和改变图像亮度强度等，增加数据量。
- - Dropout：训练过程中，0.5概率抛弃神经元，测试时，使用全部神经元。


# more
## note
网络结构基本同AlexNet，增加了许多细节优化。

主要贡献在于让人们时隔几十年，又再次发掘卷积神经网络价值。

卷积过滤器训练的是什么？

(下图是卷积层1的参数可视化)
![conv1 filter](/imgs/deep_learning/paper/paper-ImageNet-Classification-with-Deep-Convolutional-Neural-Networks/3.png)

可以看出是，低层的卷积过滤器提取到的是许多轮廓特征。


## ref

- 可视化理解卷积网络论文：Visualizing and Understanding Convolutional Networks
- 卷积过滤器的可视化理解：https://zhuanlan.zhihu.com/p/56112920

