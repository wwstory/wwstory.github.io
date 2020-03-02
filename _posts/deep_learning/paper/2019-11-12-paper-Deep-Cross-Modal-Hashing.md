---
title: '[paper] Deep Cross-Modal Hashing'
date: 2019-11-12 00:00:00
categories: 'deep learning'
tags:
    - ai
    - deep learning
    - paper
mathjax: true
---

# 背景
之前大多都是单模态检索，ANN，ANN + hash， MSH， CMH。


# 效果
『DCMH模型在MIRFLICKR-25K数据集的MAP基准』
![accuracy](/imgs/deep_learning/paper/paper-Deep-Cross-Modal-Hashing/4.png)


# 贡献
提供深度学习实现图像和文本模态之间的检索方法。


# 网络
![net](/imgs/deep_learning/paper/paper-Deep-Cross-Modal-Hashing/1.png)

『网络结构的参数』
![net param](/imgs/deep_learning/paper/paper-Deep-Cross-Modal-Hashing/2.png)

图片CNN模型采用的是Alexnet作预训练模型。

『算法』
![algorithm](/imgs/deep_learning/paper/paper-Deep-Cross-Modal-Hashing/3.png)


# 思路

损失函数：
$$
\min_{B,B^{x},B^{y},\theta_x,\theta_y} \mathcal{J}=-\sum_{i,j=1}^{n} {(S_{ij} \Theta_{ij} - log(1+e^{\Theta_{ij}}))} \\
+ \gamma(||B^{(x)}-F||_F^2 + ||B^{(y)}-G||_F^2) \\
+ \eta(||F1||_F^2 + ||G1||_F^2) \\
{\text { s.t. }} B^{(x)} \in \{-1, +1\}^{c \times n}, \\
B^{(y)} \in \{-1, +1\}^{c \times n}, \\
B \in \{-1, +1 \}^{c \times n}, \\
B = B^{(x)} = B^{(y)}. \\
$$


总损失 = 跨模态相似性（负对数似然） + 保持hash码匹配 + 使每个bit都发挥作用

二值化为hash码：
$$
\text{sign}(x) = \left \{ \begin{array}{}
1 & x > 0, \\
-1 & x < 0.
\end{array}\right.
$$

> - F：图像生成的特征码。
> - G：文本生成的特征码。
> - B：二进制哈希编码。


# 细节
使用第二范式计算误差。

使用海明距离评估S的相似性。

DCMH整合了特征学习和哈希编码学习。

训练时，先训练图片模型，再训练文本模型：
- 固定$\Theta_y$和B，优化$\Theta_x$。
- 固定B和$\Theta_x$，优化$\Theta_y$。
- 固定$\Theta_y$和$\Theta_y$，优化$B$。


# more
## note
使用深度学习的方法实现跨模态的索引。

其所用损失函数考虑也很全面。

