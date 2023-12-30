---
zotero-key: WG8YPVCQ
zt-attachments:
  - "250"
title: Compression with Bayesian Implicit Neural Representations
authors:
  - Zongyu Guo
  - Gergely Flamich
  - Jiajun He
  - Zhibo Chen
  - José Miguel Hernández-Lobato
doi: 10.48550/arXiv.2305.19185
conference: NIPS2023
citekey: guo2023-compression
tags:
  - INR
  - Astar
---
# Compression with Bayesian Implicit Neural Representations

**文章链接**: [Zotero](zotero://select/library/items/WG8YPVCQ) 
**网页链接**: [URL](http://arxiv.org/abs/2305.19185)
## Abstract

>[!abstract]
>Many common types of data can be represented as functions that map coordinates to signal values, such as pixel locations to RGB values in the case of an image. Based on this view, data can be compressed by overfitting a compact neural network to its functional representation and then encoding the network weights. However, most current solutions for this are inefficient, as quantization to low-bit precision substantially degrades the reconstruction quality. To address this issue, we propose overfitting variational Bayesian neural networks to the data and compressing an approximate posterior weight sample using relative entropy coding instead of quantizing and entropy coding it. This strategy enables direct optimization of the rate-distortion performance by minimizing the $\beta$-ELBO, and target different rate-distortion trade-offs for a given network architecture by adjusting $\beta$. Moreover, we introduce an iterative algorithm for learning prior weight distributions and employ a progressive refinement process for the variational posterior that significantly enhances performance. Experiments show that our method achieves strong performance on image and audio compression while retaining simplicity.

## Comments






## Contributions
• We propose variational Bayesian implicit neural representations for modality-agnostic data compression by encoding INR weight samples using relative entropy coding. We call our method Compression with Bayesian Implicit Neural Representations (COMBINER).
• We propose an iterative algorithm to learn a prior distribution on the weights, and a progressive strategy to refine posteriors, both of which significantly improve performance. 
• We conduct experiments on the CIFAR-10, Kodak and LibriSpeech datasets, and show that COMBINER achieves strong performance despite being simpler than related methods.
## 网络结构

有两个模型，分别是先验 $w$ 模型和后验 $w$ 的模型。
### 后验模型
![[Drawguo2023-compression]]

权重 $w,b$ 被看成完全分解的高斯分布的形式，完全独立，每一个分量具有对应的均值和方差。前向传递的过程中，使用了重参数技巧，与 [[VAE]] 类似，但它是对权重参数进行的采样，[[VAE]] 是对输出值进行采样。

### 先验模型
这个模型的结构和后验一样，不同的是，它的层与层之间没有连接，只有单纯的参数。用来与后验模型中的参数计算 KL 散度。

## 运行流程

若要对图像进行压缩与解压，依次进行以下四个步骤。

### 先验分布确定

权重的先验分布是参数化的完全分解的高斯分布，此步骤旨在确定先验分布一个较好的参数。
在整个图片集上训练，最小化如下损失函数：

$$
\begin{aligned}
\bar{\mathcal{L}}_{\beta}(\boldsymbol{\theta}_{p},\{\theta_{\mathbf{w}}^{(i)}\})& =\frac{1}{M}\sum_{i=1}^{M}\mathcal{L}_{\beta}(\mathcal{D}_{i},q_{\mathbf{w}}^{(i)},p_{\mathbf{w}})  \\
&=\frac{1}{M}\sum_{i=1}^{M}\{\sum_{(\boldsymbol{x},\boldsymbol{y})\in\mathcal{D}}\mathbb{E}_{\boldsymbol{w}\sim q_{\boldsymbol{w}}}[\Delta(\boldsymbol{y},f(\boldsymbol{x}\mid\boldsymbol{w})]+\beta\cdot D_{\mathrm{KL}}[q_{\mathbf{w}^{(i)}}\|p_{\mathbf{w}}]\}.
\end{aligned}
$$
有两类变量：先验的参数，后验的参数。
使用 EM 算法来更新：先固定先验参数，使用梯度下降的方法来更新后验参数；然后固定后验参数，此时，先验参数有闭式解：
$$
\begin{aligned}\mu_p=\frac{1}{M}\sum_{i=1}^{M}\mu_q^{(i)},\quad\sigma_p=\frac{1}{M}\sum_{i=1}^{M}[\sigma_q^{(i)}+(\mu_q^{(i)}-\mu_p)^2]\end{aligned}
$$

迭代多次，将优化好的先验参数模型保存。

**分组**
对所有的参数进行随机分组。并使得每一组的 KL 散度接近预先的指定 `args.kl2_budget`。将改分组信息保存 。

在这之后，$M$ 个后验模型都可以丢弃。

### 后验参数的确定

在此后，后验 $w$ 按照之前的分组，分为了各个小块。图片的压缩与解压就是在对后验模型中的权重进行编码与解码，在这里，按照分组来依次进行这个过程。 

训练的过程中，保证每一组的 KL 散度 $\delta$ 接近 `args.kl2_budget`，这是因为每一组的编码和解码的时间复杂度是 $\mathcal{O}(2^\delta)$，限定每一组的 KL 散度接近一致有利于时间上的稳定性。

此时，损失函数改为：
$$
\mathcal{L}_{\lambda_{k:K}}(\mathcal{D},q_{\mathbf{W}|\tilde{\boldsymbol{w}}_{1:k-1}},p_{\mathbf{w}})=\sum_{(\boldsymbol{x},\boldsymbol{y})\in\mathcal{D}}\mathbb{E}_{\boldsymbol{w}\sim q_{\mathbf{w}}}[\Delta(\boldsymbol{y},f(\boldsymbol{x}\mid\boldsymbol{w})]+\sum_{i=k}^{K}\lambda_{i}\cdot\delta_{i},
$$

$\lambda^i$ 会 动态调整以限定 $\delta_i$ 的值。

按照上面的损失函数进行训练（先验是确定的）。并在训练过程中动态调整 $\lambda^i$。

最后，将得到的 $M$ 个后验模型和向量 $\lambda$ 的值保存。

### 对单张图片进行编码

有了前面的两个过程，才能对图像进行编码。

编码过程是按照顺序依次进行的，依次对每一组进行编码。在编码 $w_k$ 时，前面的 $w_{1:k-1}$ 中的权重全部是确定的。

编码是，首先将图像的 $h,w$ 写入压缩文件。解码时才能根据它来生成坐标。

对块 $i$ 进行编码时，使用 A* coding:

![[Pasted image 20231101144556.png]]

将最佳索引 $N^*$ 写入压缩文件。并将块 $i$ 中的权重用 $w^*$ 确定下来（这使用 mask 来实现）。然后继续迭代几次，进行微调。完成后，进行下一个块的编码。

### 解码

使用 A* deconding 进行解码：
![[Pasted image 20231101145210.png]]

得到模型的权重后，正向传播一次即可重建图像。



