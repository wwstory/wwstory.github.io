---
title: paper list
date: 2019-10-01 00:00:00
categories: 'note'
tags:
    - note
---

# 1.基础网络

## ImageNet

net |   year    |   detail  |   download    |   description
-   |   -       |   -       |   -           |   -
**LetNet** | `IEEE 1998` | *Gradient-Based Learning Applied to Document Recognition* | [pdf](https://ieeexplore.ieee.org/document/726791) | CNN开山之作，手写体识别。
**AlexNet** | `ILSVRC 2012` | *ImageNet Classification with Deep Convolutional Neural Networks* | [pdf](http://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf) | ILSVRC 2012冠军，促进CNN发展。
**VGGNet** | `ICLR 2015` | *VERY DEEP CONVOLUTIONAL NETWORKS FOR LARGE-SCALE IMAGE RECOGNITION* | [pdf](https://arxiv.org/pdf/1409.1556.pdf) | 创建非常深的网络。
**Inception** | `CVPR 2015` | *Going Deeper with Convolutions* | [pdf](https://www.cv-foundation.org/openaccess/content_cvpr_2015/papers/Szegedy_Going_Deeper_With_2015_CVPR_paper.pdf) | google设计，2014年的ImageNet冠军。
**ResNet** | `CVPR 2015` | *Deep Residual Learning for Image Recognition* | [pdf](https://arxiv.org/pdf/1512.03385.pdf) | 连接前后信息，可以训练更深的网络。
**DenseNet** | `CVPR 2017` | *Densely Connected Convolutional Networks* | [pdf](https://arxiv.org/pdf/1608.06993.pdf) | 与ResNet类似，所有层更加稠密的联系。
**MobileNets** | `2017` | *MobileNets: Efficient Convolutional Neural Networks for Mobile Vision Applications* | [pdf](https://arxiv.org/pdf/1704.04861.pdf) | 
**DilatedConv** | `ICLR 2016` | *MULTI-SCALE CONTEXT AGGREGATION BY DILATED CONVOLUTIONS* | [pdf](https://arxiv.org/pdf/1511.07122.pdf) | 



## RNN
-|-|-|-|-
**** | `` | ** | [pdf]() | 

## Unsupervised Learning
-|-|-|-|-
**** | `` | ** | [pdf]() | 

## GAN
-|-|-|-|-
**GAN** | `2014` | *Generative Adversarial Nets* | [pdf](http://papers.nips.cc/paper/5423-generative-adversarial-nets.pdf) | 创新性思维，通过2个网络相互对抗的形式来训练，分别得到鉴别网络和生成网络。
**LSGANs** | `2016` | *Least Squares Generative Adversarial Networks* | [pdf](https://arxiv.org/pdf/1611.04076v2.pdf) | 
**WGAN** | `2017` | *Wasserstein GAN* | [pdf](https://arxiv.org/pdf/1701.07875v1.pdf) | 
**** | `` | ** | [pdf]() | 

## Reinforcement Learning
-|-|-|-|-
**** | `` | ** | [pdf]() | 


# 2.优化

## Model
-|-|-|-|-
**Dropout** | `2014` | *Dropout: A Simple Way to Prevent Neural Networks from Overfitting* | [pdf](http://jmlr.org/papers/volume15/srivastava14a/srivastava14a.pdf) | 
**Batch Normalization** | `2015` | *Batch Normalization: Accelerating Deep Network Training by Reducing Internal Covariate Shift* | [pdf](https://arxiv.org/pdf/1502.03167.pdf) | 
**Attention** | `ICLR 2015` | *Neural Machine Translation by Jointly Learning to Align and Translate* | [pdf](https://arxiv.org/pdf/1409.0473v2.pdf) | 首次提出Attention，用于及其翻译，展示了attention对源语目标的对其效果，解释深度模型到底学到了什么。由于后续提出的概念，这个attention被称为soft/global attention。
**hard attention相关** | `ICML 2015` | *Show, Attend and Tell: Neural Image Caption Generation with Visual Attention* | [pdf](https://arxiv.org/abs/1502.03044) | 提出hard/soft attention的概念，在图像上的应用。
**local attention** | `EMNLP 2015` | *Effective Approaches to Attention-based Neural Machine Translation* | [pdf](https://arxiv.org/pdf/1508.04025v3.pdf) | 提出global/local attention的概念，对Attention的变化，其中multiplicative attention结构被广泛使用。
**self-attention** | `` | *Hierarchical Attention Networks for Document Classification* | [pdf](https://www.cs.cmu.edu/~hovy/papers/16HLT-hierarchical-attention-networks.pdf) | 
**transformer** | `2017` | *Attention Is All You Need* | [pdf](https://arxiv.org/pdf/1706.03762.pdf) | google提出的Transformer结构，完全摒弃递归结构，依赖注意力机制，可并行。（以往nlp中大量使用encoder-decoder结构，由于前后隐藏状态的依赖性，无法并行计算。）
**** | `` | ** | [pdf]() | 


## Optimization
-|-|-|-|-
**Adam** | `ICLR 2014` | *ADAM: A METHOD FOR STOCHASTIC OPTIMIZATION* | [pdf](https://arxiv.org/pdf/1412.6980.pdf) | 
**** | `` | ** | [pdf]() | 


## Loss Function
-|-|-|-|-
**** | `` | ** | [pdf]() | 


## ?
-|-|-|-|-
**** | `` | ** | [pdf]() | 


# 3.应用

## NLP
-|-|-|-|-
**** | `` | ** | [pdf]() | 


## Object Detection
-|-|-|-|-
**-** | `2013` | *Deep Neural Networks for Object Detection* | [pdf](http://papers.nips.cc/paper/5207-deep-neural-networks-for-object-detection.pdf) | 
**RCNN** | `CVPR 2014` | *Rich feature hierarchies for accurate object detection and semantic segmentation* | [pdf](https://www.cv-foundation.org/openaccess/content_cvpr_2014/papers/Girshick_Rich_Feature_Hierarchies_2014_CVPR_paper.pdf) | 
**SPPNet** | `2014` | *Spatial Pyramid Pooling in Deep Convolutional Networks for Visual Recognition* | [pdf](https://arxiv.org/pdf/1406.4729.pdf) | 
**Fast R-CNN** | `IEEE 2015` | *Fast R-CNN* | [pdf](https://arxiv.org/pdf/1504.08083.pdf) | 
**Faster R-CNN** | `2015` | *Faster R-CNN: Towards Real-Time Object Detection with Region Proposal Networks* | [pdf](https://arxiv.org/pdf/1506.01497.pdf) | 
**YOLO** | `2015` | *You Only Look Once: Unified, Real-Time Object Detection* | [pdf]() | 
**SSD** | `2015` | *SSD: Single Shot MultiBox Detector* | [pdf](https://arxiv.org/pdf/1512.02325.pdf) | 
**R-FCN** | `2016` | *R-FCN: Object Detection via Region-based Fully Convolutional Networks* | [pdf](https://arxiv.org/pdf/1605.06409.pdf) | 
**Mask R-CNN** | `2017` | *Mask R-CNN* | [pdf](https://arxiv.org/pdf/1703.06870.pdf) | 
**** | `` | ** | [pdf]() | 


## face
-|-|-|-|-
**** | `` | ** | [pdf]() | 


## VQA
-|-|-|-|-
**** | `` | ** | [pdf]() | 

