---
zotero-key: IW 2 FDBIC
zt-attachments: 
title: Neural Image Compression Using Masked Sparse Visual Representation
authors: Wei Jiang
doi: 
conference: WACV2024
citekey: 
tags:
---
# Neural Image Compression Using Masked Sparse Visual Representation

## Introduction
使用 VQ 技术的压缩方案具有一些优点：
1. 编解码端就算处于不同平台，也具有好的鲁棒性。在基于先验的框架中，浮点运算带来的一些扰动会影响 hyperpriors 的解码结果，而熵模型是十分敏感的，进而对解码的图像造成很大干扰。VQ 技术用整数来传递 latent, 避免了这个问题。
2. 训练过程中，对有噪声干扰的训练图像更有鲁棒性。
3. 可以通过扩展特征的维度，在不改变比特率的情况下，改善图像质量。


但现有的基于 VQ 技术的图像压缩方案缺少平衡码率和失真的机制，导致它们走向了两个极端：
1. 低码率，高失真。代表：MAGE
2. 高码率，低失真。代表：AdaCode

MAGE 使用了单个 semantic-class-agnostic 的码本，AdaCode 使用了多个 semantic-class-dependent 的码本，并对多个码本的索引结果和权重 h 相乘。它们的设计无法动态地调控码率。本文则设计了一种可以动态调节码率的压缩方案。


## Method
![[Pasted image 20250102162539.png]]

这个模型建立在 AdaCode 之上。

Weight-Predictor 根据 $Y$ 直接来生成一个权重 $W$。设码本数量是 $K$, masking 模块会对每个 super-pixel 保留 $m$ 个权重，mask 掉其它 $K-m$ 个权重，得到 $\tilde{W}$ ,mask 的策略是比较 weight 的绝对值。

weight 采用 16 位 float，在不 mask 的情况下，每个 super-pixel共需要 $16K$ 的码流来传递 $W$，在 mask 之后，需要 $(16+floor(\log_{2}K))\times m$ 的码流。

在解码端，首先根据索引去各自的码本中得到 $Y_{q}$，使用 $\tilde{W}$ 和其相乘，得到 $\tilde{Y}$。Weight Filler 模块和 Weight Predictor 类似，重新生成新的 weight, 并进一步得到重建的 $Y$。

在 $m=1$ 的特殊情况下，Weight-Predictor 会做出改进，此时 weight 的大小并不重要了，仅仅需要传递一个码流大小为 $u\times v\times floor(\log K)$ 的权重。

## 训练
采用 AdaCode 中训练好的 $E^{emb}$ 和码本，并固定它们。
使用 L1 损失 $\mathcal{L}_{1}(\hat{x},x)$, pereptual loss $\mathcal{L}_{per}(\hat{x},x)$，对抗损失 $\mathcal{L}_{adv}(\hat{x},x)$, 以及 Y 的重建损失 $\mathcal{L}_{con}(\hat{Y},Y)$ 来训练生成器（）