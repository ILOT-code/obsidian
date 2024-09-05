---
zotero-key: ARKSB7UP
zt-attachments:
  - "277"
title: Efficient Dynamic-NeRF Based Volumetric Video Coding with Rate Distortion Optimization
authors:
  - Zhiyu Zhang
  - Guo Lu
  - Huanxiong Liang
  - Anni Tang
  - Qiang Hu
  - Li Song
doi: 10.48550/arXiv.2402.01380
conference: xxx
citekey: zhang2024-efficient
tags: []
---
# Efficient Dynamic-NeRF Based Volumetric Video Coding with Rate Distortion Optimization

**文章链接**: [Zotero](zotero://select/library/items/ARKSB7UP) [attachment](<file:///home/ilot/Zotero/storage/6UZRAARJ/Zhang%20%E7%AD%89%20-%202024%20-%20Efficient%20Dynamic-NeRF%20Based%20Volumetric%20Video%20Codi.pdf>)
**网页链接**: [URL](http://arxiv.org/abs/2402.01380)
## Abstract

本文使用动态的神经辐射场技术来压缩体素视频。
其工作极大程度建立在 [[chen2023-factor|(Anpei Chen, 2023)]] 上。作者将它扩展到时间维度，并引入了 rate-estimate 和加噪声模拟量化的技术，实现了 end-to-end 的训练。

## Intro
作者分析了先前的 SOTA 技术 ReRF 的不足：
1. 在 3 D 层面，进行运动估计是很困难的事，ReRF 中的运动估计也只使用了很少的参数，无法有效利用帧间信息。
2. 它的模型训练以及之后的模型压缩过程是分离的，无法做到端到端。

## Method

$$
(\mathbf{c},\sigma)=g_{\phi}(\mathbf{x},\mathbf{d})
$$
$$
\begin{align*}
\hat{C}(\mathbf{r})&=\sum _{i=1}^{N}T_{i}\alpha_{i}\mathbf{c}_{i}\\
T_{i}&=\prod_{j=1}^{i-1}(1-\alpha _{i}), ~~\alpha_{i}=1-\exp(-\sigma_{i}\delta_{i})
\end{align*}
$$

$T_{i}$ 表示透过率，$\alpha$ 表示透明度。

$$
\begin{aligned}
\mathbf{c(x)}& =\text{interp}\left(\mathbf{x},\mathbf{C}\right) \\
\mathbf{b(x)}& =\text{interp}\left(\gamma(\mathbf{x}),\mathbf{B}\right) \\
(\mathbf{c},\sigma)& =\mathcal{P}(\mathbf{c}(\mathbf{x})\circ\mathbf{b}(\mathbf{x}),\mathbf{d}) 
\end{aligned}
$$
## Comments

### 先前研究的问题

> [!note] Page 1
> 
> However, ReRF separates the modeling from compression process, resulting in suboptimal compression efficiency.
> 
> ---
> 🔤然而，ReRF 将建模与压缩过程分开，导致压缩效率不理想。🔤
> ^8K4DP2TZa6UZRAARJp1

> [!note] Page 1
> 
> However, the acquisition of volumetric video typically requires multiple cameras capturing from different angles, resulting in data volumes that can be several times larger than traditional 2D videos [2], which poses significant challenges in terms of storage and transmission.
> 
> ---
> 🔤然而，体视频的采集通常需要多个摄像机从不同角度进行采集，导致数据量可能比传统2D视频大数倍[2]，这给存储和传输带来了巨大的挑战。🔤
> ^8P43ZWXFa6UZRAARJp1

> [!note] Page 1
> 
> While geometry-based solutions [4] involve the reconstruction and compression of dynamic point clouds, but they are vulnerable to occlusions and textureless regions.
> 
> ---
> 🔤虽然基于几何的解决方案[4]涉及动态点云的重建和压缩，但它们很容易受到遮挡和无纹理区域的影响。🔤
> ^XBVTRXX8a6UZRAARJp1

> [!note] Page 2
> 
> However, accurately estimating the 3D motion grid proves to be a challenging task,
> 
> ---
> 🔤然而，准确估计 3D 运动网格被证明是一项具有挑战性的任务，🔤
> ^ZHIA48STa6UZRAARJp2

> [!note] Page 2
> 
> Moreover, the modeling and compression process of the dynamic radiance field in ReRF are separate.
> 
> ---
> 🔤此外，ReRF中动态辐射场的建模和压缩过程是分开的。🔤
> ^3A8M8FTDa6UZRAARJp2

> [!note] Page 3
> 
> However, such a method not only neglects the temporal continuity but also poses challenges for compression.
> 
> ---
> 🔤然而，这种方法不仅忽略了时间连续性，而且给压缩带来了挑战。🔤
> ^M5HHE6ZKa6UZRAARJp3

### 创新点

> [!note] Page 1
> 
> Specifically, we decompose the NeRF representation into the coefficient fields and the basis fields, incrementally updating the basis fields in the temporal domain to achieve dynamic modeling. Additionally, we perform end-to-end joint optimization on the modeling and compression process to further improve the compression efficiency.
> 
> ---
> 🔤具体来说，我们将 NeRF 表示分解为系数域和基域，在时域中增量更新基域以实现动态建模。此外，我们对建模和压缩过程进行端到端联合优化，进一步提高压缩效率。🔤
> ^JXVQMGNZa6UZRAARJp1

### 技术细节

> [!note] Page 2
> 
> To accelerate training and rendering, DiF [9] decomposes the representation of NeRF into the coefficient fields and the basis fields. The basis fields capture the commonality of the signal, and the coefficient fields represent the spatial variation of the signal. Specifically, the coefficient fields are represented by a single-scale 3D grid C, while the basis fields are represented by multi-scale 3D grids B.
> 
> ---
> 🔤为了加速训练和渲染，DiF [9] 将 NeRF 的表示分解为系数域和基域。基场捕获信号的共性，系数场表示信号的空间变化。具体来说，系数场由单尺度3D网格C表示，而基场由多尺度3D网格B表示。🔤
> ^SIXKVF9Ta6UZRAARJp2

> [!note] Page 3
> 
> During training, we only update the coefficient fields Ct and the
> 
> ---
> 🔤在训练期间，我们只更新系数域 Ct 和🔤
> ^5CFA6U2Aa6UZRAARJp3

> [!note] Page 3
> 
> residual fields Rt for the current frame.
> 
> ---
> 🔤当前帧的剩余字段 Rt。🔤
> ^URFKITATa6UZRAARJp3

> [!note] Page 3
> 
> Addtionally, to ensure temporal continuity and facilitate compression, we apply L1 regularization to the residual fields:
> 
> ---
> 🔤此外，为了确保时间连续性并促进压缩，我们对残差场应用 L1 正则化：🔤
> ^99YS33D2a6UZRAARJp3

### 紫色

> [!note] Page 4
> 
> In this paper, we conducted experiments on the ReRF dataset [15] and the Dna-rendering dataset [17].
> 
> ---
> 🔤在本文中，我们在 ReRF 数据集 [15] 和 Dna-rendering 数据集 [17] 上进行了实验。🔤
> ^IMTFAPTEa6UZRAARJp4

### 品红

> [!note] Page 5
> 
> Baseline. We consider DiFDiF [9] as the baseline for our method. However, while DiF is designed for static scenes, when extending it to dynamic scenes, we employ DiF to model each time step individually. Additionally, to ensure a fair comparison, we utilize the 7zip which is a lossless compression algorithm to compress the DiF models.
> 
> ---
> 🔤基线。我们将 DiFDiF [9] 作为我们方法的基准。然而，虽然 DiF 是为静态场景设计的，但当将其扩展到动态场景时，我们使用 DiF 单独建模每个时间步。此外，为了确保公平比较，我们使用无损压缩算法7zip来压缩DiF模型。🔤
> ^9L6M9Z36a6UZRAARJp5

