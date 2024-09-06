---
zotero-key: FW8IM9EK
zt-attachments:
  - "282"
title: "Factor Fields: A Unified Framework for Neural Fields and Beyond"
authors:
  - Anpei Chen
  - Zexiang Xu
  - Xinyue Wei
  - Siyu Tang
  - Hao Su
  - Andreas Geiger
doi: 10.48550/arXiv.2302.01226
conference: SIGGRAPH 2023
citekey: chen2023-factor
tags:
---
# Factor Fields: A Unified Framework for Neural Fields and Beyond

**文章链接**: [Zotero](zotero://select/library/items/FW8IM9EK) [attachment](<file:///home/ilot/Zotero/storage/9WNVVIJM/Chen%20%E7%AD%89%20-%202023%20-%20Factor%20Fields%20A%20Unified%20Framework%20for%20Neural%20Fiel.pdf>)
**网页链接**: [URL](http://arxiv.org/abs/2302.01226)
## Abstract

>[!abstract]
>We present Factor Fields, a novel framework for modeling and representing signals. Factor Fields decomposes a signal into a product of factors, each represented by a classical or neural field representation which operates on transformed input coordinates. This decomposition results in a unified framework that accommodates several recent signal representations including NeRF, Plenoxels, EG3D, Instant-NGP, and TensoRF. Additionally, our framework allows for the creation of powerful new signal representations, such as the "Dictionary Field" (DiF) which is a second contribution of this paper. Our experiments show that DiF leads to improvements in approximation quality, compactness, and training time when compared to previous fast reconstruction methods. Experimentally, our representation achieves better image approximation quality on 2D image regression tasks, higher geometric quality when reconstructing 3D signed distance fields, and higher compactness for radiance field reconstruction tasks. Furthermore, DiF enables generalization to unseen images/3D scenes by sharing bases across signals during training which greatly benefits use cases such as image regression from sparse observations and few-shot radiance field reconstruction.


## Intro
![[Pasted image 20240905140546.png]]
NeRF 技术衍生出了很多改进技术，它们具有共性。本文构建了一种范式，使得 NeRF, Instant-NGP 等方法都成为了该范式下的一个特例。在此基础上，提出了 Dictionary Field 方法，把信号分解到 coefficient 域和 basis 域，能够跨空间利用相似的信号特征。

## Factor Fields
$$
\hat{\mathbf{s}}(\mathbf{x})=\mathcal{P}\left(\prod_{i=1}^N\mathbf{f}_i\left(\boldsymbol{\gamma}_i(\mathbf{x})\right)\right)
$$

将坐标 $\mathbf{x}$ 使用坐标变换 $\gamma_{i}$，$\mathbf{f}_{i}$ 再把它映射到对应的域中去，连乘符号表示 element-wise, $\mathcal{P}$ 把得到的值投影到目标上去。

### 坐标变换 $\gamma$ 的选择
存在多种多样的选择，包括周期性的、哈希的、本征的。
![[Pasted image 20240905140448.png]]
但是 $\gamma$ 的选择被它所使用的域给限制了。
1. 当 $\mathbf{f}$ 表示 coefficients 时: coefficients 通常在不同的坐标上十分不一样，因此对应的 $\gamma$ 尽量使用本征变换。
2. 当 $\mathbf{f}$ 表示 basis 时：通常希望在不同的空间上能够使用相同的基，因此对应的 $\gamma$ 应是周期性的。
3. 在一些技术中，设置了一系列不同空间分辨率下的基，设有 $L$ 个不同的 level，$\gamma_i^{\mathrm{PR}}(\mathbf{x})=(\gamma_i(\mathbf{x}f_1),\ldots,\gamma_i(\mathbf{x}f_L))$
![[Pasted image 20240905142141.png]]


### DiF
取 $N=2,\gamma_{1}(x)=x,\gamma_{2}(x)$ 为一个周期性的函数，$\mathbf{f}_{1},\mathbf{f}_{2}$ 取 grid-based 的映射，就组成了 DiF。使用 DCT 来初始化 $\mathbf{f}_{2}$ 的网格。

## Comments

### 先前研究的问题

> [!note] Page 3
> 
> Representing the signal s(x) using a global set of basis functions is inefficient as information cannot be shared spatially.
> 
> ---
> 🔤使用全局基函数集表示信号 s(x) 效率低下，因为信息无法在空间上共享。🔤
> ^8Z9ANMS7a9WNVVIJMp3

### 创新点

> [!note] Page 2
> 
> In order to gain a better understanding of existing representations, make comparisons across their design principles, and create powerful new representations, we propose Factor Fields, a novel mathematical framework that unifies many previous neural representations for multi-dimensional signals. This framework offers a simple formulation for modeling and representing signals.
> 
> ---
> 🔤为了更好地理解现有的表示，比较它们的设计原理，并创建强大的新表示，我们提出了因子场，这是一种新颖的数学框架，它统一了许多先前的多维信号神经表示。该框架提供了用于建模和表示信号的简单公式。🔤
> ^DD5ZJGU5a9WNVVIJMp2

> [!note] Page 5
> 
> This forces the signal to be represented with random combinations of features at every iteration, encouraging sparsity and preventing co-adaptation of features.
> 
> ---
> 🔤这迫使信号在每次迭代时都用特征的随机组合来表示，从而鼓励稀疏性并防止特征的共同适应。🔤
> ^3A4ULBR6a9WNVVIJMp5

### 性能成绩

> [!note] Page 2
> 
> Our framework accommodates most previous neural representations. Many of them can be represented in our framework as a single factor with a domain transformation
> 
> ---
> 🔤我们的框架适应了大多数以前的神经表征。其中许多可以在我们的框架中表示为具有域转换的单个因素🔤
> ^9Q8Y46LBa9WNVVIJMp2

### 技术细节

> [!note] Page 2
> 
> Our framework decomposes a signal by factorizing it into multiple factor fields (f1, . . . , fN ) operating on suitably chosen coordinate transformations (γ1, . . . , γN ) as illustrated in Fig. 1. More specifically, each factor field decodes multi-channel features at any spatial location of a coordinate-transformed signal domain. The target signal is then regressed from the factor product via a learned projection function (e.g., MLP).
> 
> ---
> 🔤我们的框架通过将信号分解为在适当选择的坐标变换（γ1，...，γN）上运行的多个因子字段（f1，...，fN）来分解信号，如图1所示。更具体地说，每个因子字段解码坐标变换信号域的任何空间位置的多通道特征。然后通过学习的投影函数（例如 MLP）从因子乘积回归目标信号。🔤
> ^DR2NUX6Sa9WNVVIJMp2

