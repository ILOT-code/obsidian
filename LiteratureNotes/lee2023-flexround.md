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

PTQ 方法，一般而言就是把原来的权重进行放缩，然后直接舍如到最近的整数，$\boldsymbol{\widehat{W}}=s\lfloor \frac{\boldsymbol{W}}{s}\rceil$。这种简单的量化方式会带来严重的性能退化，这种量化可以看成给原来的 $\boldsymbol{W}$ 添加一个扰动项，这一层的输出可以以权重为变量展开到二阶项，性能退化主要来源于二阶项上的误差。最近的一些研究都建议设计或学习一种新的 weight-rounding 策略，而不仅仅是近似到最近的整数，来提高模型量化后的性能。


>[!记号说明]
>![[Pasted image 20240515153907.png]]




