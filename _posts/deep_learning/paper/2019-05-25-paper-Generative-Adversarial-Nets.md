---
title: '[paper] Generative Adversarial Nets'
date: 2019-05-25 00:00:00
categories: 'deep learning'
tags:
    - ai
    - deep learning
    - paper
mathjax: true
---

# 背景
传统生成网络使用马尔科夫链做生成网络。


# 效果
![effect](/imgs/deep_learning/paper/paper-Generative-Adversarial-Nets/3.png)


# 贡献
提供全新的网络模型，同时得到生成网络和鉴别网络。


# 网络
『对抗网络算法思路』
![algorithm](/imgs/deep_learning/paper/paper-Generative-Adversarial-Nets/1.png)


# 思路
构建2个网络，生成网络+鉴别网络，本文将其类比造假团队和鉴别警察。

开始时2者都比较弱，造假团队造出的赝品比较粗糙，鉴别警察鉴别假货的能力也比较差。

> 2个网络的关键在于，鉴别网络一开始必须要略强于生成网络。

开始时造假团队造的假货基本能被鉴别警察查出来。

后来，造假团队吸取经验（鉴别网络反馈生成网络哪些部分没做好），造假能力变强，鉴别警察变得无法看出假货。

鉴别警察也开始吸取经验（鉴别网络反馈真假品之间有哪些区别），鉴别假货的能力变强，假货也越来越多被鉴别出来。

重复上2步，造假团队（生成）和警察（鉴别）之间互相博弈，能力互相增进。

博弈到最后，造假团队造出的假货，鉴别警察只有50%的识别率。即达到平衡。

## 公式
$$
\min_G \max_D V(D,G) = \mathbb{E}_{x \sim p_{data}(x)} [log D(x)] + \mathbb{E}_{z \sim p_z(z)} [log(1 - D(G(z)))]
$$

> - data：真实数据。
> - $p_{data}$：真实数据的分布。
> - z：噪音。
> - $p_z$：原始噪音的分布。
> - $p_g$：经过生成器后的数据分布。
> - G：生成器网络。
> - D：鉴别器网络。


# 细节
『生成网络生成数据的假设空间，鉴别器的真伪判别』
![假设空间映射](/imgs/deep_learning/paper/paper-Generative-Adversarial-Nets/2.png)

- 2个网络博弈开始初期，基本都比较弱，但鉴别网络要略胜于生成网络，否则无法推动2者互相增强。
- 2个网络博弈到最终，应该达到一个平衡，鉴别网络只能鉴别一半的生成网络生成的假数据。
- 2个网络互相交叉训练，优化1次D，再优化1次G。（也可以优化2次D，再优化1次G）
- 生成网络和鉴别网络的参数分开，训练也是分开的。


# more
## note
Goodfellow创造性的GAN网络，使用博弈论的原理，让2个网络互相博弈互相增强，也提供了新的生成网络模型。

本文主要讲述了整个GAN网络的思路，和其算法的推理。
