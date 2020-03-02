---
title: >-
  [paper] Face Aging with Identity-Preserved Conditional Generative Adversarial
  Networks
date: 2019-05-16 00:00:00
categories: 'deep learning'
tags:
    - ai
    - deep learning
    - paper
mathjax: true
---

# 背景
- 1.前人缺乏不同年龄段给定的人脸的训练样本。
- 2.传统做法，粗略的从原型基础上分类，基于原型方法年龄分组计算平均人脸。使用时对待不同年龄组的模式合成。结果导致每个人中特殊人群的信息丢失，一些合成的结果看起来不真实。
- 3.基于物理模型的方法。
- 4.RNN方法，相邻年龄组的面部合成更平滑，但身份信息没有明确的保持。
- 5.CGAN方法，没有效率，不同年龄的衰老几乎没有什么不同，由一定的效果，但看起来模糊。


# 效果
![effect](/imgs/deep_learning/paper/paper-Face-Aging-with-Identity-Preserved-Conditional-Generative-Adversarial-Networks/2.png)


# 贡献
- 将CGAN应用到人脸衰老预测中。
- 重点是保证合成的人脸的人的身份是同一人，并迫使使其分类到目标年龄。


# 网络
![net](/imgs/deep_learning/paper/paper-Face-Aging-with-Identity-Preserved-Conditional-Generative-Adversarial-Networks/1.png)

IPCGAN组成
- 1.条件生成对抗网络。
- 2.身份保持模块。
- 3.年龄分类模块

将年龄分为5组，对应不同年龄段，设成328x328x5或64x64x5的矩阵，4层设为全0的矩阵，对应的年龄那一层设为全1，将其作为条件附加输入上输入网络。


# 思路
以图1为例：
- 将20-30岁128x128x3的原图+128x128x5的条件作输入，生成40-50岁的假图片，再使用年龄分类器判别生成的年龄。（年龄分类器损失）
- 将生成的40-50岁的假图片和数据集中40-50岁的真图片作输入，辨别器预测2者是否是生成了目标的年龄。（身份保持损失）
- 将生成的40-50岁的假图片和其输入20-30岁的原图作输入，2者计算距离。（生成器损失）

## 基于CGANs的面部生成模块 (CGANs based face generation module)
使用最小二乘法生成网络LSGANs计算生成网络和鉴别网络的损失。
$$
L_D = \frac{1}{2} \mathbb{E}_{x \sim p_x(x)} [(D(x|C_t)-1)^2] + \frac{1}{2} \mathbb{E}_{y \sim p_y{y}} [(D(G(y|C_t))^2]
$$

> - 第1项：真实图，真标签。
> - 第2项：真实图，假标签。生成图，真标签。
> - $C_t$：年龄分组。

$$
L_G = \frac{1}{2} \mathbb{E}_{y \sim p_y{y}} [(D(G(y)|C_t)-1)^2]
$$

## 身份保持模块 (Identity-preserved module)
$$
L_{identity} = \sum_{x \in p_x(x)} ||h(x) - h(G(x|C_t))||^2
$$

> - h：网络输出。

- 低特征层保持内容。
- 高特征层保持风格。

选择Alexnet的Conv5作为h(x)判断身份保存的程度。

## 年龄分类模块 (Age classification module)

$$
L_{age} = \sum_{x \in p_x (x)} \ell (G(x|C_t), C_t)
$$

> - $\ell$：softmax损失。

## 目标函数 (Objective function)
$$
\begin{align*}
& G_{loss} = \lambda_1 L_G + \lambda_2 L_{identity} + \lambda_3 L_{age} \\
& D_{loss} = L_D
\end{align*}
$$

生成器损失 = 生成器损失 + 身份信息的损失 + 年龄分类器的损失

- 生成器损失：鉴别器对生成器生成的图的损失
- 身份信息的损失：对生成器身份分类，提取的特征层，2层特征层作均方差可得
- 年龄分类器的损失：对生成器生成图分类的损失


# 细节
选择卷积Conv5作为风格迁移。

合并128x128x3和128x128x5条件特征图作为输入。

使用leakyRelu和Batchnorm。


# more
## note
本文实际是一种风格迁移的做法，对于人脸衰老有一定的效果，但基本上看起来是对图片风格的识别，以更适合的方式给图像贴上皱纹之类的。对于年龄较小的人脸无法生成如骨骼成长之类的效果。

