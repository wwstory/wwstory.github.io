---
title: '[paper] Cycle-Consistency for Robust Visual Question Answering'
date: 2019-11-22 00:00:00
categories: deep learning
tags:
    - ai
    - deep learning
    - paper
mathjax: true
---


# 背景
- 尽管可视化问答发展显著，但VQA的鲁棒性不足。
- 通过引入新的数据集和评估方式，证明当前最好的VQA模型对语言的变化的效果是脆弱的。
- VQA发展：LSTM+CNN，attention networks，module networks，relational networks，multimodal fusion。
- 发现VQA领域缺乏量化测量鲁棒性的基准。


# 效果

『之前的VQA模型，鲁棒性展示：同一语义，回答结果不同』
![show robust](/imgs/deep_learning/paper/paper-Cycle-Consistency-for-Robust-Visual-Question-Answering/2.png)

『采用CycleGAN后，鲁棒性展示』
![show robust](/imgs/deep_learning/paper/paper-Cycle-Consistency-for-Robust-Visual-Question-Answering/3.png)


# 贡献

- **提出循环一致性（cycle-consistent）训练方式，使VQA模型应对语言变化更具鲁棒性**。
- 提出评估VQA模型鲁棒性的方法。
- 提供新的数据集。（VQA-Rephrasings）
- 验证使用这种训练方法下，最先进的模型表现。


# 网络

![net](/imgs/deep_learning/paper/paper-Cycle-Consistency-for-Robust-Visual-Question-Answering/1.png)


# 思路

网络原理基本同Cycle GAN。

## 过程
- 数据集问题$Q$，通过F:VQA模型生成回答$A^{\prime}$。
- 使用生成的回答$A^{\prime}$，通过G:VQG模型生成问题$Q^{\prime}$。
- 使用生成的问题$Q^{\prime}$，通过F:VQA模型生成回答$A^{\prime \prime}$。（与前面的F是同一个VQA模型）

## 损失函数
- 对比 原问题$Q$ 和 生成的问题$Q^{\prime}$，计算损失（Question Consistency Loss）。
- 对比 从原始问题得到回答的$A^{\prime}$ 和 数据标签$A$，计算损失（VQA Loss）。
- 对比 数据标签$A$ 和 从生成的问题$Q^{\prime}$得到的回答$A^{\prime \prime}$，计算损失（Answer Consistency Loss）。

$$
\begin{aligned}
\mathcal{L}_{\text {total}}=\mathcal{L}_{F}\left(A, A^{\prime}\right)+\lambda_{G} \mathcal{L}_{G}\left(Q, Q^{\prime}\right) \\
+\lambda_{C} \mathcal{L}_{\text {cycle}}\left(A, A^{\prime \prime}\right)
\end{aligned}
$$

> $\lambda$各个损失项的系数。

# 细节

## 门机制 (Gating Mechanism)
并非所有生成的问题都与I-Q-A一致，为此，作者提出一种门控机制，过滤掉一些不合适的问题。对于生成的问题$Q^{\prime}$，只保留答案与原答案的余弦相似性阈值大于$T_{sim}$的问题。(文中设$T_{sim}=0.9$)

## 后期激活 (Late Activation)
cycle-consistent关键的环节是防止模式坍塌（mode collapse）现象。为了确保各个子网络之间正常工作，作者通过在训练的最后阶段激活循环一致来解决。

## 其它
### VQA改写数据集
VQA模型要在同一个问题的不同改述之间保持一致，对所有改述的答案应该相同。使用CS(K)一致性评分来衡量。（其实就是简单计算改写问题有几个生成的答案一致的比例）

$$
C S(k)=\sum_{Q^{\prime} \subset Q,\left|Q^{\prime}\right|=k} \frac{\mathcal{S}\left(Q^{\prime}\right)}{^{n} C_{k}}
$$

$$
\mathcal{S}\left(Q^{\prime}\right)=\left\{\begin{array}{ll}
{1} & {\text { if } \forall q \in Q^{\prime} \theta(q)>0} \\
{0} & {\text { otherwise }}
\end{array}\right.
$$

> - 问题集合Q有n个改写，选取其中k个。C是n里去k个的组合数量。
> - $\theta$是VQA的准确性。


# more
## note
这是一篇2019CVPR facebook的论文，将用于图像风格迁移的Cycle GAN应用到了VQA领域，实则是将其作为一种在线数据增强的手段，来达到其鲁棒性。


## ref

[ref 1][101]

---

[101]: https://blog.csdn.net/xiasli123/article/details/102887043
