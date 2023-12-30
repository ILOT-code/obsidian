---
zotero-key: HSTB42UP
zt-attachments:
  - "323"
title: "SHACIRA: Scalable HAsh-grid Compression for Implicit Neural Representations"
authors:
  - Sharath Girish
  - Abhinav Shrivastava
  - Kamal Gupta
conference: ICCV2023
citekey: girish2023-shacira
tags:
---
# SHACIRA: Scalable HAsh-grid Compression for Implicit Neural Representations

**文章链接**: [Zotero](zotero://select/library/items/HSTB42UP) 
**网页链接**: [URL](https://openaccess.thecvf.com/content/ICCV2023/html/Girish_SHACIRA_Scalable_HAsh-grid_Compression_for_Implicit_Neural_Representations_ICCV_2023_paper.html)
## Abstract

>[!abstract]
>>Recently, learnable feature grids proposed by M ̈ uller et al.  have allowed significant speed-up in the training as well as the sampling of INRs by replacing a large neural network with a multi-resolution look-up table of feature vectors and a much smaller neural network.
>
>在 INR 方法中，基于可学习的特征网格的方法，能够大大加速训练过程，减少网络的大小。
>>However, these feature grids come at the expense of large memory consumption which can be a bottleneck for storage and streaming applications.
>
>在引入特征网格时，极大地增加了内存负担。
>>we propose SHACIRA, a simple yet effective task-agnostic framework for compressing such feature grids with no additional posthoc pruning/quantization stages.
>
>本文提出的方法是对 [[muller2022-instant|(Thomas Müller, 2022)]] 的改进，它对于特征网格进行了进一步压缩，而不损失性能。

## Comments

### 前置工作

> [!note] Page 2
> 
> We use an entropy regularization loss on the latent features to reduce the size of the discrete latents without significantly affecting the reconstruction performance.
> 
> ---
> 🔤我们对潜在特征使用熵正则化损失来减小离散潜在特征的大小，而不会显着影响重建性能。🔤
> ^QCRZFZFKaDVJ9KBESp2

### 黄色

> [!note] Page 2
> 
> Unsurprisingly, several methods have been proposed to compress INRs using quantization [10, 14, 18], pruning, or a combination of both [13]. The focus of these works is to compress the weights of the MLP, which often leads to either a big drop in the reconstruction quality, or slow convergence for high resolution signals.
> 
> ---
> 🔤毫不奇怪，已经提出了几种使用量化[10,14,18]、修剪或两者组合[13]来压缩INR的方法。这些工作的重点是压缩 MLP 的权重，这通常会导致重建质量大幅下降，或者高分辨率信号的收敛速度变慢。🔤
> ^8JVC9VYVaDVJ9KBESp2

> [!note] Page 2
> 
> This shifts the burden of representing the signal to the feature grid instead of the MLP.
> 
> ---
> 🔤这将表示信号的负担转移到特征网格而不是 MLP。🔤
> ^QMXD2C32aDVJ9KBESp2

> [!note] Page 2
> 
> However, the size of the feature grids can be very large which is not memory efficient and impractical for many real-world applications with network bandwidth or storage constraints.
> 
> ---
> 🔤然而，特征网格的大小可能非常大，这对于许多具有网络带宽或存储限制的实际应用程序来说内存效率不高且不切实际。🔤
> ^9W3JI8Q8aDVJ9KBESp2

> [!note] Page 2
> 
> Our feature grid consists of quantized feature vectors and parameterized decoders which transform the feature vectors into continuous values before passing them to MLP.
> 
> ---
> 🔤我们的特征网格由量化特征向量和参数化解码器组成，参数化解码器在将特征向量传递给 MLP 之前将其转换为连续值。🔤
> ^FGSY9JMNaDVJ9KBESp2

> [!note] Page 2
> 
> we employ an annealing approach to the discrete latents which improves the training stability of the latents,
> 
> ---
> 🔤我们对离散潜在变量采用退火方法，提高了潜在变量的训练稳定性，🔤
> ^I6MZQT2AaDVJ9KBESp2

> [!note] Page 2
> 
> As seen in Figure 1, the proposed approach is able to compress feature-grid methods such as Instant-NGP [1] with almost an order of magnitude while retaining the performance in terms of PSNR for gigapixel images and 3D scenes from the RTMV dataset [20].
> 
> ---
> 🔤如图 1 所示，所提出的方法能够将特征网格方法（例如 Instant-NGP [1]）压缩几乎一个数量级，同时保留来自 RTMV 数据集的十亿像素图像和 3D 场景的 PSNR 性能[ 20]。🔤
> ^NKBUE4E4aDVJ9KBESp2

> [!note] Page 2
> 
> The key contribution of our work is to directly compress the learnable feature grid with proposed entropy regularization loss and highlight its adaptibility to diverse signals.
> 
> ---
> 🔤我们工作的关键贡献是利用提出的熵正则化损失直接压缩可学习的特征网格，并突出其对不同信号的适应性。🔤
> ^FXBUCKBCaDVJ9KBESp2

> [!note] Page 2
> 
> highlight the effectiveness of our approach to compress feature-grid based INRs and its advantages over MLP-based INRs.
> 
> ---
> 🔤强调我们压缩基于特征网格的 INR 的方法的有效性及其相对于基于 MLP 的 INR 的优势。🔤
> ^3KGLCFIMaDVJ9KBESp2

> [!note] Page 4
> 
> While feature-grid INRs can converge faster than pureMLP approaches, the space required to store all the parameters at different levels can rise very rapidly if we want highfidelity reconstructions for high-resolution inputs.
> 
> ---
> 🔤虽然特征网格 INR 可以比 pureMLP 方法收敛得更快，但如果我们想要对高分辨率输入进行高保真度重建，则存储不同级别的所有参数所需的空间可能会迅速增加。🔤
> ^6D7DTIP2aDVJ9KBESp4

> [!note] Page 4
> 
> we propose a parameterized decoder dθ
> 
> ---
> 🔤我们提出了一个参数化解码器 dθ🔤
> ^MBXXPXCTaDVJ9KBESp4

> [!note] Page 4
> 
> we observed that a single shared decoder parameterized as a linear transform across all L levels works pretty well
> 
> ---
> 🔤我们观察到，参数化为跨所有 L 层的线性变换的单个共享解码器效果很好🔤
> ^89F3IHKPaDVJ9KBESp4

> [!note] Page 4
> 
> utilize the Straight-Through Estimator [48](STE.md).
> 
> ---
> 🔤利用直通估计器[48]（STE）。🔤
> ^I232WE77aDVJ9KBESp4

> [!note] Page 4
> 
> The gradients are propagated through the samples using the Gumbel reparameterization trick
> 
> ---
> 🔤使用 Gumbel 重新参数化技巧在样本中传播梯度🔤
> ^HQF7PFXRaDVJ9KBESp4

> [!note] Page 5
> 
> we optimize the parameters φ of MLP gφ, discrete feature grid b Q, discrete to continuous decoder θ, and the probability models {Pd}
> 
> ---
> 🔤我们优化 MLP gφ 的参数 φ、离散特征网格 b Q、离散到连续解码器 θ 和概率模型 {Pd}🔤
> ^AZFUB9ZCaDVJ9KBESp5

> [!note] Page 5
> 
> Kodak dataset
> 
> ---
> 🔤柯达数据集🔤
> ^TLTGHLFVaDVJ9KBESp5

> [!note] Page 5
> 
> varying from 2M pixels to 1G pixels
> 
> ---
> 🔤从2M像素到1G像素不等🔤
> ^UCK4QS27aDVJ9KBESp5

> [!note] Page 5
> 
> RTMV dataset
> 
> ---
> 🔤RTMV 数据集🔤
> ^6KSV6FIYaDVJ9KBESp5

> [!note] Page 5
> 
> UVG dataset
> 
> ---
> 🔤UVG数据集🔤
> ^VHD7LZCKaDVJ9KBESp5






## Introduction

针对 INR 模型的压缩方法与局限性：
1. 对网络的权重进行压缩。这容易导致重建质量的下降。
2. 设置一系列不同分辨率的可学习的 feature grid, 将网络的训练负担转移一部分到 feature grid 上，能够减小网络的大小。但存储 feature grid 需要巨大带宽，限制了在一些方面的应用。

本文提出的方法概要：
1. faeture grid 不直接存储，而是由更小的 feature vector 经过解码器生成。
2. 使用熵模型对 feature vector 进行估计，减小存储空间。
3. 在 feature vector 的量化过程中，设计了一种退火方法，提高了训练的稳定性。

feature vector，网络参数，解码器参数，熵模型可以一起训练，是端到端的可学习框架。

## Approach

### Feature-grid INRs

流水线的最后一部分，此部分即是 [[muller2022-instant|(Thomas Müller, 2022)]] 提出的方法。


![[Pasted image 20231210113748.png]]

定义：
- feature grid 由 $\mathbf{Z}=\{\mathbf{Z}_1,\ldots,\mathbf{Z}_l\}$ 表示，$\mathbf Z_i$ 表示不同分辨率对应的矩阵。
- $\mathbf{Z}_l\in\mathbb{R}^{T_l\times F},\mathrm{~where~}T_l=\min(R_l^2,T)$，设置哈希表条目上限 T，特征长度 $F$ 固定。


$$
\begin{align*}
\mathbf{f}_l(\mathbf{x})&=\text{interp}(\mathbf{Z}_l[tl],\mathbf{Z}_l[tr],\mathbf{Z}_l[bl],\mathbf{Z}_l[br])\\
\widehat{\mathbf{y}}&=g_\phi(\mathrm{concat}[\mathbf{f}_1(\mathbf{x}),\ldots,\mathbf{f}_L(\mathbf{x})])
\end{align*}
$$
### Feature-grid reparameterization

![[Pasted image 20231210115018.png]]

对于每个矩阵 $\mathbf Z_{l}\in\mathbb{R}^{T_{l} \times F}$ ，由对应的 $\mathbf{Q}_l\in\mathbb{R}^{T_l\times D}$ 来压缩，由解码器来 $d_\theta:\mathbb{R}^D\to\mathbb{R}^F$ 转换，全部解码器共享参数。
尽管可以对不同分辨率层使用不同的解码器，作者观察得出结论：共享参数的线性转换解码器在平衡训练推理时间和重建质量上最好。

$\mathbf Q$ 由 $\widehat{\mathbf{Q}}$ 量化得到（round 寻找最近的整数），此过程不可微，作者提出使用 [[STE]] 来解决此问题。<mark style="background: #FF5582A6;">但是 STE 中简单的近似会带来较大的舍入误差</mark>：$\|\mathbf{Q}-\widehat{\mathbf{Q}}\|$ 。作者提出了一个 soft rounding operation。

以一定的概率来选择 $\lfloor\widehat{\mathbf{Q}}\rfloor$ 和 $\lceil\widehat{\mathbf{Q}}\rceil$，使用一个 one-hot 编码的向量 $b$：
$$\mathbf{Q}=b\lfloor\widehat{\mathbf{Q}}\rfloor+(1-b)\lceil\widehat{\mathbf{Q}}\rceil.$$
其中 $b$ 取 $0，1$ 的概率分别是：
$$\begin{aligned}&\mathrm{Prob}(b=0)\propto\exp\left\{-\tanh^{-1}\left(\widehat{\mathbf{Q}}-\lfloor\widehat{\mathbf{Q}}\rfloor\right)/\tau\right\}\\&\mathrm{Prob}(b=1)\propto\exp\left\{-\tanh^{-1}\left(\lceil\widehat{\mathbf{Q}}\rceil-\widehat{\mathbf{Q}}\right)/\tau\right\}\end{aligned}$$
使用 [[Gumbel Softmax]] 来生成 one-hot 向量 b.
在训练的初期，$\tau$ 比较大（接近 1），此软化操作并不能很好的近似 round 函数，但能提供很好的梯度传递，在训练后期，$\tau$ 趋于 0，此软化操作无限接近 round 函数。

对此操作所做的消融实验：
以此软化操作的持续时间为变量作图
![[Pasted image 20231210134639.png]]

### Feature-grid compression
隐空间有 $D$ 维，为每一维引入一个概率模型：$P_d:\mathbb{R}\rightarrow[0,1]$。
$$
\begin{align*}
 \mathcal{L}_I (\widehat{\mathbf{Q}})&=-\frac 1 T\sum_{d=1}^D\sum_{i=1}^T\log_2\left (P_d\left (\widehat{\mathbf{Q}}[i, d]+n\right)\right)\\
where \ &n \sim \mathcal{U}[-1,1] 
\end{align*}
$$
消融实验：
![[Pasted image 20231210140606.png]]
### End-to-end optimization
总体流程：
$$
\begin{aligned}\mathbf{Q}&=\mathrm{discretize}(\widehat{\mathbf{Q}})\\\mathbf{Z}&=d_\theta(\mathbf{Q})\\\mathbf{\widehat{y}}&=g_\phi\left(\mathrm{concat}\left[\mathrm{interp}(\mathbf{Z},\mathbf{x})\right]\right)\end{aligned}
$$
使用损失函数：
$$
\mathcal{L}_{\mathrm{MSE}}(\widehat{\mathbf{y}},\mathbf{y})+\lambda_I\mathcal{L}_I(\widehat{\mathbf{Q}})
$$
来训练全部参数。最后使用算术编码，取 $P_d$ 的概率，来无损编码 $Q$。

## Experiments
在 Kodak 数据集上的结果：
![[Pasted image 20231210140113.png]]

对不同分辨率图片的结果：
![[Pasted image 20231210140307.png|500]]
收敛速度对比：
![[Pasted image 20231210140730.png]]