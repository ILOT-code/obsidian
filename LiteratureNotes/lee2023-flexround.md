---
zotero-key: HGG5B42S
zt-attachments:
  - "176"
title: "FlexRound: Learnable Rounding based on Element-wise Division for Post-Training Quantization"
authors:
  - Jung Hyun Lee
  - Jeonghoon Kim
  - Se Jung Kwon
  - Dongsoo Lee
doi: 10.48550/arXiv.2306.00317
conference: ICML2023
citekey: lee2023-flexround
tags:
---
# FlexRound: Learnable Rounding based on Element-wise Division for Post-Training Quantization

**文章链接**: [Zotero](zotero://select/library/items/HGG5B42S) [attachment](<file:///home/ilot/Zotero/storage/3FJYBIWV/Lee%20%E7%AD%89%20-%202023%20-%20FlexRound%20Learnable%20Rounding%20based%20on%20Element-wis.pdf>)
**网页链接**: [URL](http://arxiv.org/abs/2306.00317)
## Abstract

>[!abstract]
>Post-training quantization (PTQ) 是直接将模型量化后，直接应用到推理阶段的技术。这种方法被证明可以有效的增强模型量化性能。对它的研究都关注在如何设计出有效的量化手段上。
>本文提出了一种基于逐元素除的量化方法，来量化模型的权重，使用可学习的方法自动调整量化参数，能够根据原来的（量化前的）权重大小进行灵活的量化。



## Introduction
网络的量化技术可以被分为两类：

quantization-aware training (QAT)：网络量化过程和网络的训练过程是共同进行的，它需要在完整的训练数据集上进行端到端的再训练或微调，花费很大。
post-training quantization (PTQ)：不需要整个数据集上进行训练，也不需要端到端的学习，模型量化完成后直接使用。

PTQ 方法，一般而言就是把原来的权重进行放缩，然后直接舍如到最近的整数，$\boldsymbol{\widehat{W}}=s\lfloor \frac{\boldsymbol{W}}{s}\rceil$。这种简单的量化方式会带来严重的性能退化，这种量化可以看成给原来的 $\boldsymbol{W}$ 添加一个扰动项 $\boldsymbol{\widehat{W}}=\boldsymbol{W}+\delta W$，这一层的输出可以以权重为变量展开到二阶项，性能退化主要来源于二阶项上的误差。最近的一些研究都建议设计或学习一种新的 weight-rounding 策略，而不仅仅是近似到最近的整数，来提高模型量化后的性能。


>[!记号说明]
>![[Pasted image 20240515153907.png]]


本文和现有的一些研究一样，希望能够逐层的去取最小值：
$$\|\boldsymbol{W}X-\widehat{\boldsymbol{W}}\widetilde{\boldsymbol{X}}\|_F^2$$
现有的研究一般都是以逐元素相加的方式：
$$
\begin{align*}
\widehat{\boldsymbol{W}}&=s_1(\lfloor\frac{\boldsymbol{W}}{s_1}\rfloor+h(\boldsymbol{V}))\\
\widehat{\boldsymbol{W}}&=s_1\bigg\lfloor\frac{\boldsymbol{W}+\boldsymbol{V}}{s_1}\bigg\rceil 
\end{align*}

$$
函数 $h$ 和连续变量 $\boldsymbol{V}$ 可以学习得到。

作者提出的确实另一种逐元素除的形式：
![[Pasted image 20240515161445.png]]
$\boldsymbol{S}$ 是和 $\boldsymbol{W}$ 形状相等的。$\boldsymbol{S}$ 当然可以采用最简单的形式：各个位置的元素完全独立，并且作者声称这样的 tensor-wise 也能达到和全精度模型相同的性能。作者还通过改进 $\boldsymbol{S}$ 的结构来提供性能。
$$
\widehat{\boldsymbol{W}}=\begin{cases}s_1\left\lfloor\frac {\boldsymbol{W}}{s_1\odot\boldsymbol{S}_2\odot\boldsymbol{s}_3}\right\rceil&\text{for a linear layer}\\ \\
s_1\left\lfloor\frac {\boldsymbol{W}}{s_1\odot\boldsymbol{S}_2\odot\boldsymbol{s}_3\odot\boldsymbol{s}_4}\right\rceil&\text{for a 2D convolution}\end{cases}
$$
对于线性层，$\boldsymbol{W}\in \mathbb{R}^{C_{out}\times C_{in}},\boldsymbol{s_{3}}\in \mathbb{R}^{C_{out}\times 1}_{>0}$。
对应卷积层，$\boldsymbol{W}\in\dot{\mathbb{R}^{C_{out}\times C_{in}\times H\times W}},\boldsymbol{s}_3 \in \mathbb{R}_{>0}^{C_{out}\times1\times1\times1},\boldsymbol{s}_4 \in \mathbb{R}_{>0}^{1\times C_{in}\times1\times1}$
引入了一些分块、分 vector 的特征。全部参数一开始全部置 1，以使 $\widehat{\boldsymbol{W}}$ 近似于 $\boldsymbol{W}$.

令 $S^{\prime}=\boldsymbol{S_{2}\odot \boldsymbol{s_3}}$ 在这样的设计下，$\left|\frac{\partial\mathcal{L}}{\partial S_{(i, j)}^{\prime}}\right|$ 正比于 $\left|W_{(i, j)}\frac{\partial\mathcal{L}}{\partial\widehat{W}_{(i, j)}}\right|$ (采用 STE 的反向求导模式)，一个越大的权重 $\boldsymbol{W}_{ij}$, 越容易带来对应 $\boldsymbol{S}^{\prime}$ 的大幅度变化。定义 $\overline{W}_{(i, j)}=\left\lfloor\frac{W_{(i, j)}}{s_1\odot S_{(i, j)}^{\prime}}\right\rceil$，$W_{ij}$ 越大，前者就有越大可能受到一个大的更新。大的权重一般重要性大一点，令他们有更多机会去量化到一个更远的整数而不仅仅是最近的两个整数，这是合理的。

下面的图说明了这一点。


![[Pasted image 20240515165159.png]]



toread: AdaRound, AdaQuant, BRECQ, QDrop