---
title: >-
  [paper] Coupled CycleGAN: Unsupervised Hashing Network for Cross-Modal
  Retrieval
date: 2019-11-07 00:00:00
categories: 'deep learning'
tags:
    - ai
    - deep learning
    - paper
mathjax: true
---

# 背景
- DCMH,TDH,SPDQ,SSAN展示了深度跨模态hsh方法的有效性。
- SSAN是在DCMH上加了GAN。UGACH也使用了GAN。
- UDCMH自适应无分割学习策略来迭代学习哈希码。


# 效果
![effect](/imgs/deep_learning/paper/paper-Coupled-CycleGAN-Unsupervised-Hashing-Network-for-Cross-Modal-Retrieval/3.png)
![effect](/imgs/deep_learning/paper/paper-Coupled-CycleGAN-Unsupervised-Hashing-Network-for-Cross-Modal-Retrieval/4.png)


# 贡献
使用2个GAN将跨模态索引问题，划分为2部分分开处理，1个GAN用于实现文本图像的表达，1个GAN用于生成hash码。


# 网络
![net](/imgs/deep_learning/paper/paper-Coupled-CycleGAN-Unsupervised-Hashing-Network-for-Cross-Modal-Retrieval/1.png)


# 思路
- 建立2个环状网络，总计4个GAN，外圈（内圈）的文本（图像）的生成器（鉴别器）各自有一个。
- 外圈的生成器提取中间一层的输出，作内圈的网络的输入。
- 内圈的生成器再提取中间一层的输出，作hash编码的输入。


# 细节

$$
\mathcal{L}_f = \mathcal{L}_{adv\_f} + \mathcal{L}_{rec\_f} + \mathcal{L}_{sim\_f} \\
\mathcal{L}_z = \mathcal{L}_{adv\_z} + \mathcal{L}_{rec\_z} + \mathcal{L}_{sim\_z} \\
\mathcal{L}_{Total} = \mathcal{L}_f + \mathcal{L}_z
$$

损失 = 对抗损失（鉴别器） + 重建损失（生成器损失） + 表达/hash码相似性损失

生成器，鉴别器，2个模态编码间的相似性的损失，外圈内圈都是范式2计算误差损失。

> - G：生成器。
> - f：（fake）生成的数据。
> - D：鉴别器。
> - Z：跨模态表示。
> - F：特征。
> - E：抽取特征的网络。

$$
B^* = sign(H^*), * \in \{v, t\} \\
B^* \in {\{-1, 1\}}^{n \times K} \\
$$

> - v：图像表示。
> - t：文本表示。

『algorithm步骤』
![algorithm](/imgs/deep_learning/paper/paper-Coupled-CycleGAN-Unsupervised-Hashing-Network-for-Cross-Modal-Retrieval/2.png)



# more
## note
本文标题含有CycleGAN，却没有原CycleGAN的思维，仅是构建了一个网络结构看起来像环的网络。

由于没提供代码，网络的复现时存在很多细节问题，难以构建。
