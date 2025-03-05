---
zotero-key: RFVNJ 6 JH
zt-attachments:
  - "306"
title: "DIFFPC: DIFFUSION-BASED HIGH PERCEPTUAL FIDELITY IMAGE COMPRESSION WITH SEMANTIC REFINEMENT"
authors:
  - Yichong Xia

conference: ICLR2025
citekey:
tags:
---
## Abstract
大体上，本文的方法建立在预训练的 stable diffusion 之上，并额外使用图像的语义特征来指导去噪过程，是一个带条件的 ddpm 模型。
在此基础上，作者专门为图像压缩任务设计了一个控制模块，来强化模型对这些条件的学习。
作者实现低比特率下，在 perceptual fidelity 的 sota 结果。

## Introduction
在低比特率下，像素级指标（如均方误差 MSE）并不是好的选择，直接导致此类压缩方案中大量纹理细节 perceptual fidelity的丢失。这意味着在像素域中寻求低比特率下的紧凑表示不可避免地导致人类感知的下降和图像语义一致性的缺失。这样的缺陷降低了低比特率下压缩算法的实用性。

扩散模型多年来已被证明能够生成高真实感图像，表明扩散模型更适合低比特率图像压缩场景。
Latent Diffusion Model (LDM) 在图像分割和超分任务中，被证明具有强大的先验信息以及多模态语义信息的融合能力。

本文建立在 LDM 上，利用图像级和语义级的信息，来控制去噪过程。这两种信息的融合能平衡图像保真度和语义的保真度，进而取得更好的结果。

## Method
### 总体架构
![[Pasted image 20250305145748.png]]
训练分为两个阶段。
第一阶段，训练压缩器和图像域的控制。
第二阶段，联合训练图像域和语义级的控制。

### Muti-feature Compressor
主要用于去编码 $z_{0}$，减少其冗余信息。它的结构类似普通的 end-end R-D 编码器。
![[Pasted image 20250305152250.png]]

作者对这个结构的训练方法有一些奇怪的解释，但最终解释的结果和普通的图像编码器似乎没有任何区别：
> 与图像域中的神经压缩器不同，$M_{{\phi}}$ 重建了条件扩散模型的控制项。为确保条件生成的结果符合预期，我们旨在最小化以下 KL散度：
> $$
D_{\mathrm{KL}}(p(\mathbf{z}_0|\mathbf{x}),p(\mathbf{z}_0|\hat{\mathbf{c}}))=D_{\mathrm{KL}}(p(\mathbf{z}_0|\mathbf{x}),p(\mathbf{z}_0|M_\phi(\mathbf{z}_0,f_1,f_2))).
$$ 通过引理：![[Pasted image 20250305153206.png]]
上面的 KL 散度被代替为：
$$
\underset{\gamma}{\operatorname*{\operatorname*{\operatorname*{\arg\min}}}}D_{{\mathrm{KL}}}(p(\mathbf{z}_{0}|\mathbf{x}),p_{\gamma}(\hat{\mathbf{c}}|\mathbf{z}_{0}))=\underset{\gamma}{\operatorname*{\operatorname*{\operatorname*{\arg\min}}}}\frac{1}{2\sigma_{{\mathbf{z}_{0}}}^{2}}\left[\left\|\boldsymbol{\mu}_{{\mathbf{z}_{0}}}-\boldsymbol{\mu}_{{\hat{\mathbf{c}}}}\right\|_{2}^{2}\right].
$$

这个损失函数，本来就是 end-edn R-D 方法中的重构损失项，这不是没有什么区别。

### IC-ControlNet and Time-aware Decoupling
IC 先融合 $\hat{c}$ 和 $Z_{t}$，并使用 U-Net 的前半部分初始化网络，经过各层处理得到的信息分别被嵌入到 n 后半部分中去。
![[Pasted image 20250305153831.png]]
然而，在低比特率下过于严格的控制也存在弊端：激进的量化可能导致 cˆ的病理性退化，从而增加了去噪模型预测 z 0 的难度。这种倾向可能使去噪模型放弃预测真实数据分布，转而生成类似于条件 cˆ的样本；这种现象被称为条件泄漏（Zhao et al., 2024）。在压缩框架中，这种现象表现为去噪模型失去了其去噪效果，进而重建出失真的图像，如图 3 (b)(1) 所示。为了解决这一问题，我们提出了时间感知解耦（TAD）模块。TAD 的详细信息见附录A.4。TAD 致力于将控制因子 cˆ从 IC-ControlNet 中解耦，从而将扩散模型的噪声预测转化为残差噪声预测：

在低比特率下，$\hat{c}$ 会损失很多信息，增加了去噪的难度。这种倾向可能使去噪模型放弃预测真实数据分布，转而生成类似于条件 $\hat{c}$ 的样本，这种现象被称为条件泄漏（Zhao et al., 2024）。本文提出了 Time-Aware Decoupling (TAD) module 来处理这个问题：
$$
\hat{\epsilon}=\mathcal{DN}_\theta\left(\sqrt{\bar{\alpha}_t}\boldsymbol{z}_0+\sqrt{1-\bar{\alpha}_t}\epsilon,\hat{\boldsymbol{c}},t\right)+\mathrm{TAD}_\eta(\hat{\boldsymbol{c}},t).
$$
其实就是类似跳跃连接的思路。

最终，stage 1 的损失函数是：
$$
\begin{aligned}\mathcal{L}_{stage\:1}&=\quad\lambda_{1}\mathcal{L}_{imp}\left(\boldsymbol{z}_{0},\hat{\boldsymbol{c}}\right)+\lambda_{2}\mathcal{L}_{rate}+\mathcal{L}_{CSD},\\\mathcal{L}_{CSD}&=\quad\mathbb{E}_{\boldsymbol{z}_{0},\hat{\boldsymbol{c}},,t,\epsilon}\left[\left\|\epsilon-\mathcal{D}\mathcal{N}_{\theta}\left(\sqrt{\bar{\alpha}_{t}}\boldsymbol{z}_{0}+\sqrt{1-\bar{\alpha}_{t}}\epsilon,\hat{\boldsymbol{c}},t\right)-\mathrm{TAD}_{\eta}(\hat{\boldsymbol{c}},t)\right\|_{2}^{2}\right].\end{aligned}
$$

### Stage 2
stage 2 会训练这个 Image Enc 模块, 把这个“图像”和 text 输入给 Q-former, 得到一个语义级别的信息，给到 U-Net 的 cross-attention 中去。