---
title: '[paper] You Only Look Once:Unified, Real-Time Object Detection'
date: 2020-06-01 00:00:00
categories: deep learning
tags:
    - ai
    - deep learning
    - paper
mathjax: true
---


# 背景

- HOG/DPM使用滑动窗口方法均匀的对整张图片分类。
- R-CNN使用proposal方式，先提出可能的区域，再分类。（后续还有：精修边界框，过滤重复，重新打分。）复杂的pipeline流水线是缓慢和难优化的，因为每个独立的模块都需要分别训练。
- Fast R-CNN，容易将背景块当作对象，因为它看不到更大的上下文。


# 效果

![effect](/imgs/deep_learning/paper/paper-YOLO-v1/2.png)

# 贡献

- 统一模型，速度快，无复杂的pipeline。
- end-to-end的模型（不同于滑动窗口），可以让模型在训练中，联系整个图像上下文，学到其中蕴含的关系。（应该指的是：分类和定位之间的关联，也许会得到某些对象应该出现在场景的哪些地方。（车应该在地面上，云应该在天上））（天上的车会被定位时放弃掉？）
- 统一模型 + yolo做了更多的定位误差，减少了对背景的误判。（是Fast R-CNN的一半）

# 缺点

- 比较靠近的物体，小目标，识别能力较差。（因为1个网格只预测2个bbox，1个网格也只预测一个类）
- 长宽比的泛化能力不行。
- 准确性不如最先进算法。（one stage基本都比two stage准确率低）（作者归因于大部分在于定位问题）

# 网络

『参考GoogLeNet，24层卷积，2层全连接』

![effect](/imgs/deep_learning/paper/paper-YOLO-v1/1.png)

# 思路

> 以VOC数据集为例，B=2，C=20。

## 训练

- 运行网络。结果输出为7x7x30的矩阵。
- 通过confidence打分，判断这些网格是否包含对象，以及重合度多高。

$$
Pr(Class_i | Object) * Pr(Object) * IOU_{pred}^{truth} = Pr(Class_i) * IOU_{pred}^{truth}
$$

- 对这些网格，计算损失。

$$\begin{array}{l}
\lambda_{\text {coord }} \sum_{i=0}^{S^{2}} \sum_{j=0}^{B} \mathbb{1}_{i j}^{\text {obj }}\left[\left(x_{i}-\hat{x}_{i}\right)^{2}+\left(y_{i}-\hat{y}_{i}\right)^{2}\right] \\
\quad+\lambda_{\text {coord }} \sum_{i=0}^{S^{2}} \sum_{j=0}^{B} \mathbb{1}_{i j}^{\text {obj }}\left[(\sqrt{w_{i}}-\sqrt{\hat{w}_{i}})^{2}+(\sqrt{h_{i}}-\sqrt{\hat{h}_{i}})^{2}\right] \\
\quad+\sum_{i=0}^{S^{2}} \sum_{j=0}^{B} \mathbb{1}_{i j}^{\text {obj }}\left(C_{i}-\hat{C}_{i}\right)^{2} \\
\quad+\lambda_{\text {noobj }} \sum_{i=0}^{S^{2}} \sum_{j=0}^{B} \mathbb{1}_{i j}^{\text {noobj }}\left(C_{i}-\hat{C}_{i}\right)^{2} \\
\quad+\sum_{i=0}^{S^{2}} \mathbb{1}_{i}^{\text {obj }} \sum_{c \in \text { classes }}\left(p_{i}(c)-\hat{p}_{i}(c)\right)^{2}
\end{array}$$

## 测试

- 运行网络。得到7x7x30的结果。
- 将每个网格预测的2个bbox的confidence得分 * 分类的概率。（7x7x2 = 98个值）（不存在IOU的值）
- 设置阈值过滤，nms去重。

![test](/imgs/deep_learning/paper/paper-YOLO-v1/6.png)

# 细节

## 训练细节
- 1.网络前20层使用，预训练模型。后4层卷积+2层全连接。
- 2.使用448x448分辨率的图像输入。
- 3.预测（坐标xywh，置信度conf）*B，类别概率c。
- 4.预测的bbox归一化到0~1。x,y为相对单元格的偏移，w,h为想对于整张图片的比例。
- 5.最后一层使用线性激活，其它层使用reaky-relu。
- 6.优化器平方差误差。（容易优化，然而不好到达最大平均精度）
- 7.定位误差和分类误差权重相同，这不好。
- 8.通常一副图中，大部分网格不包含对象，这会将这些单元格的confidence分数`push`到0。加入不含对象的网格的loss来平衡。
- 9.定位损失↑，分类损失↓。
- 10.大盒和小盒偏移相同的像素，对于大盒影响不大，而对小盒有明显偏差。使用平方根做损失，可以提高小盒的损失。

![y=x^(1/2)](/imgs/deep_learning/paper/paper-YOLO-v1/5.png)

- 11.每个单元格预测B个边界盒（VOC数据集中，取B=2），不同边界盒能得到更多尺寸，比例，类别。
- 12.第一个全连接后，使用dropout。

## 附图理解

『很容易被误导成单元格只预测这个一块小区域再组合的，中上表示所有单元格预测的所有bbox，中下表示每个颜色代表不同类别』

![预测的bbox和类别](/imgs/deep_learning/paper/paper-YOLO-v1/3.png)

『实际每个单元格是如何预测的』

![每个单元格的预测方式](/imgs/deep_learning/paper/paper-YOLO-v1/4.png)

# more
## note

误区：

划分为7x7的网格，总觉得采用类似SSD的方式，每个网格预测包含一块区域，然后由这个网格这部分涉及到的神经元去运算。就会联想到，这么一小块网格在定位时由于只有一小部分图像信息，连定位都是问题。 

-> 实际上，由于最后一部分全链接的存在，其实，这里划分的网格是输出的结果部分7x7x30，而不是输入的图像划分的7x7。预测输出的结果已经是包含了整个图像的信息，其中每个网格的划分，只是说这个网格对应的位置区域是预测到的 `对象的中心`落入这个单元格，故由这个单元格去`处理`（这个处理也很具有误导性，其实就是对象中心落入这个单元格，就把预测的结果输出到这个位置的单元格而已）。



## ref

[ref 1][101]


---

[101]: https://blog.csdn.net/c20081052/article/details/80236015
