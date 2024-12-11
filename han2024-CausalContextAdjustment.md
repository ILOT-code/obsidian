---
zotero-key: IW 2 FDBIC
zt-attachments:
  - "145"
title: Causal Context Adjustment Loss for Learned Image Compression
authors:
  - Minghao Han
doi: 
conference: None
citekey: 
tags:
---
# Causal Context Adjustment Loss for Learned Image Compression

## 问题分析
LIC 的成功基于 VAE 模式，并引入各种 causal context 来进一步减少码流。
对于 causal context 为什么能 work, 可以很直观的进行解释：它为当前待解码的 latent 引入了更多的参考，而 latent 在空间上、通道上都具有一定的关联。

z 的引入其实已经为 y(latent) 之间的联系进行了建模了，继续引入 context 的话，就是希望利用更多的信息来减少当前 y 的不确定性。因此，应当鼓励编码器，在生成的早阶段的 y 中，包含更多的多预测后阶段 y 有益的信息。目前各种 causal context 的设计仅仅通过总体的损失函数 $D+\lambda R$ 来隐式的进行训练，对于各阶段 y 的组织并不能进行显示的干预。

本文则提出了一种损失函数，它可以被应用在任何 causal context 的模型中，直接来调整 encoder, 为早期的 y 赋予更多信息。

## Method
### preliminary
**VAE**: ![[Pasted image 20241211150222.png]]
**with hyperprior**
![[Pasted image 20241211150302.png]]

**Autoregressive**
![[Pasted image 20241211150340.png]]

### method
考虑一个分为两阶段进行的 AR 模型，$\mathbf{y}=\{\mathbf{y}_{1},\mathbf{y}_{2}\}$
令 $q(\hat{\mathbf{y}}|\hat{\mathbf{z}})$ 表示当前模型在当前数据集下给出的真正分布，而 $p(\hat{\mathbf{y}}|\hat{\mathbf{z}})$ 则是预测的分布。在仅有 z 和有 z 和 context 的情况下，交叉熵数损失写成：
$$
\begin{align}
H_{HP}(q(\hat{\mathbf{y}}|\hat{\mathbf{z}}),p(\hat{\mathbf{y}}|\hat{\mathbf{z}}))=H(q(\hat{\mathbf{y}_{1}}|\hat{\mathbf{z}}),p(\hat{\mathbf{y}_{1}}|\hat{\mathbf{z}}))+H(q(\hat{\mathbf{y}_{2}}|\hat{\mathbf{z}}),p(\hat{\mathbf{y}_{2}}|\hat{\mathbf{z}}))  \\
H_{HP+AR}(q(\hat{\mathbf{y}}|\hat{\mathbf{z}}),p(\hat{\mathbf{y}}|\hat{\mathbf{z}}))=H(q(\hat{\mathbf{y}_{1}}|\hat{\mathbf{z}}),p(\hat{\mathbf{y}_{1}}|\hat{\mathbf{z}}))+H(q(\hat{\mathbf{y}_{2}}|\hat{\mathbf{z}},\hat{\mathbf{y}}_{1}),p(\hat{\mathbf{y}_{2}}|\hat{\mathbf{z}},\hat{\mathbf{y}}_{1}))

\end{align}
$$

context 带来的信息增益就是
$$H_{\mathrm{HP}}-H_{\mathrm{HP+AR}}=H(q(\hat{\boldsymbol{y}}_2|\hat{\boldsymbol{z}}),p(\hat{\boldsymbol{y}}_2|\hat{\boldsymbol{z}}))-H(q(\hat{\boldsymbol{y}}_2|\hat{\boldsymbol{z}},\hat{\boldsymbol{y}}_1),p(\hat{\boldsymbol{y}}_2|\hat{\boldsymbol{z}},\hat{\boldsymbol{y}}_1)),$$

在一个 HP+AR 的框架中，后一项是模型能直接给出来的，前一项则不能。为此，作者设计了一个辅助熵模型，它仅仅以 z 作为输入，来预测 y 的概率。并以此信息增益作为损失。

扩展到多阶段的 AR 模型中，该项损失就是：
$$\mathcal{L}_{CCA}=\sum_i\mathbb{E}_{\hat{\boldsymbol{y}}\sim p_{\hat{\boldsymbol{y}}|\boldsymbol{z}}}\mathbb{E}_{\hat{\boldsymbol{z}}\sim p_{\hat{\boldsymbol{z}}|\boldsymbol{\psi}}}[-\log p_{\hat{\boldsymbol{y}}_i|\hat{\boldsymbol{z}},\hat{\boldsymbol{y}}_{<i-1}}(\hat{\boldsymbol{y}}_i|\hat{\boldsymbol{z}},\hat{\boldsymbol{y}}_{<i-1})+\log p_{\hat{\boldsymbol{y}}_i|\hat{\boldsymbol{z}},\hat{\boldsymbol{y}}_{<i}}(\hat{\boldsymbol{y}}_i|\hat{\boldsymbol{z}},\hat{\boldsymbol{y}}_{<i})].$$

![[Pasted image 20241211152547.png]]


这个辅助的熵模型同样也需要训练。但作者并没有明确说明是如何训练的，如果这个辅助的熵模型没学习好的话，这个 CCA loss 岂不是一点意义没有。


### Channel-wise Unevenly Grouped Entropy Model
作者设计的熵模型是分通道进行的，捕获通道间的信息增益。此外，由于 CCA 损失可以明确地将重要信息调整到早期通道中，作者探索了一种不均匀分组的策略，以充分利用前几个信息渠道。非均匀分组策略还为带来了参数数量和运行时间方面的优势。


## EXP

下图比较了 latent 的编码长度，随着slice 增长，CCA  loss 降低了 latent 的编码长度。
![[Pasted image 20241211153809.png]]

![[Pasted image 20241211153944.png]]