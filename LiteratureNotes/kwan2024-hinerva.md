---
zotero-key: ELZIKZKE
zt-attachments:
  - "290"
title: "HiNeRV: Video Compression with Hierarchical Encoding-based Neural Representation"
authors:
  - Ho Man Kwan
  - Ge Gao
  - Fan Zhang
  - Andrew Gower
  - David Bull
doi: 10.5555/3666122.3669299
conference: xxx
citekey: kwan2024-hinerva
tags: []
---
# HiNeRV: Video Compression with Hierarchical Encoding-based Neural Representation

**文章链接**: [Zotero](zotero://select/library/items/ELZIKZKE) [attachment](<file:///home/ilot/Zotero/storage/NXTAQGZ4/Kwan%20%E7%AD%89%20-%202024%20-%20HiNeRV%20Video%20Compression%20with%20Hierarchical%20Encodi.pdf>)
**网页链接**: [URL](http://arxiv.org/abs/2306.09818)
## Abstract

>[!abstract]
>在视频压缩领域，现有的 INR-based 方法无法达到最先进的性能，这可能和它们简单的结构有关，限制了它们的容量。本文提出了一种具有多个结构、层次复杂的网络结构，大大提升了性能。
>在 UVG 和 MCL-JCV 数据集上，比 HNeRV 节省 72%, 比 DCVC 节省 43.4%。

## Method

视频 $V\in \mathbb{R}^{T\times H\times W\times C}$ . 视频中的每一帧都被分成了 $M\times M$ 的块，patch 坐标的坐标是 $(i,j,t),0\leq t<T,0\leq j<\frac{H}{M},0\leq i<\frac{W}{M}$
该 patch在经过一系列卷积之后，大小会变，$M_{1}\times M_{1}$。但是依然可以把这个 patch 坐标映射到全局的坐标，假设一个 patch-based 坐标是 $u_{patch},v_{{patch}}$，那么它映射回的全局坐标 (frame -based)
就是 $u_{frame}=j\times M_{1}+u_{patch},v_{frame}=i\times M_{1}+v_{patch}$



Grid 采用了 [[lee2023-ffnerv|(Joo Chan Lee, 2023)]] 中的多时间分辨率 grid，不同的是，[[lee2023-ffnerv|(Joo Chan Lee, 2023)]]
是 frame-wise 的，一次生成一整个帧，只在时间轴上进行线性插值，而本文是 patch-wise 的 (当然也能扩展到 frame-wise)，会使用 frame-based 的三个坐标轴进行插值。这些 grid 的形状是 $\lfloor\frac{T_{grid}}{2^l}\rfloor\times H_{grid}\times W_{grid}\times(C_{grid}\times2^l),\mathrm{for~}0\leq l<L_{grid}.$

在每一个 HiNeRV 块中，会获得两个输入。
$X_{n}\in \mathbb{R}^{M_{n}\times M_{n}\times C_{n}}$, $C_{n}=\left\lfloor  \frac{C_{0}}{R^{n-1}}  \right\rfloor$ 使用双线性插值上采样(作者认为，无参的双线性插值相比使用卷积，在压缩任务中更好)，$M_{{n+1}}=M_{n}\times S_{n+1}$。$S$ 是上采样系数。双线性插值会产生平滑的图像，会导致后续的网络难以产生高频的输出，图像的高频细节难以维持。grid-based encoding 是解决这个问题的一种方法，但它同时意味着巨大的空间损耗，对于 N 个块而言难以接受。本文提出了一种分层编码方法。

每一个 HiNeRV 块，也存在着一个 temporal local grid. 具有 $L_{local}$ 个形状是 $\left\lfloor  \frac{T_{local}}{2^l}  \right\rfloor *S_{n}\times S_{n}\times \left( \left\lfloor   \frac{C_{local}}{R^{n-1}}  \right\rfloor \times 2^l \right )$，$S_{n}$ 很小，这些网格的参数量很小。对于 patch 中的坐标 $(u_{p},v_{p})$，由于给了 patch-index，可以先把它们映射到全局坐标 $(u_{f},v_{f})$, 最后得到 local 坐标 $(u_{l}=u_{f}~MOD~ S_{n},v_{l}=v_{f}~MOD~S_{n})$。
使用该坐标 $(t,u_{l},v_{l})$ 在 temporal local grid 上取得对应的值（三线性插值）

![[Pasted image 20240904162739.png]]
## Comments

### 先前研究的问题

> [!note] Page 1
> 
> However, existing INR-based methods have failed to deliver rate quality performance comparable with the state of the art in video compression.
> 
> ---
> 🔤然而，现有的基于 INR 的方法未能提供与视频压缩领域的最新技术相当的速率质量性能。🔤
> ^ZDU6Y7D6aNXTAQGZ4p1

> [!note] Page 1
> 
> However, existing INR-based algorithms remain significantly inferior to state-of-the-art standard-based [58, 52, 9] and learning-based codecs [28, 46, 29, 30, 37]. For example, none of these INR-based codecs can compete with HEVC x265 [3] (veryslow preset).
> 
> ---
> 🔤然而，现有的基于 INR 的算法仍然明显不如最先进的基于标准的 [58,52,9] 和基于学习的编解码器 [28,46,29,30,37]。例如，这些基于 INR 的编解码器都无法与 HEVC x265 [3]（非常慢的预设）竞争。🔤
> ^6PZNCQENaNXTAQGZ4p1

> [!note] Page 2
> 
> In addition, most existing work employs Fourier-based positional encoding [40]; this has a long training time and can only achieve sub-optimal reconstruction quality [12, 31, 6]
> 
> ---
> 🔤此外，大多数现有工作都采用基于傅里叶的位置编码[40]；这有很长的训练时间并且只能达到次优的重建质量[12,31,6]🔤
> ^MEG52AWMaNXTAQGZ4p2

### 创新点

> [!note] Page 2
> 
> We replace commonly used sub-pixel conventional layers [47] in existing INRs for upsampling [12, 31, 6, 27, 11] by a new upsampling layer which embodies bilinear interpolation with hierarchical encoding that is sampled from multi-resolution local feature grids.
> 
> ---
> 🔤我们用新的上采样层替换了现有 INR 中常用的子像素传统层 [47]，用于上采样 [12,31,6,27,11]，该层体现了从多分辨率局部特征网格采样的分层编码的双线性插值。🔤
> ^CWLJZA9NaNXTAQGZ4p2

### 疑惑点

> [!note] Page 7
> 
> using quantization-aware training with Quant-Noise [51] to minimize the quantization error.
> 
> ---
> 🔤使用量化感知训练和 Quant-Noise [51] 来最小化量化误差。🔤
> ^VCXYDLZBaNXTAQGZ4p7

### 性能成绩

> [!note] Page 1
> 
> The proposed method has been evaluated on both UVG and MCL-JCV datasets for video compression, demonstrating significant improvement over all existing INRs baselines and competitive performance when compared to learning-based codecs (72.3% overall bit rate saving over HNeRV and 43.4% over DCVC on the UVG dataset, measured in PSNR). 1
> 
> ---
> 🔤所提出的方法已在用于视频压缩的 UVG 和 MCL-JCV 数据集上进行了评估，与基于学习的编解码器相比，证明了所有现有 INR 基线和竞争性能的显着改进（比 HNeRV 总体比特率节省 72.3%，比 DCVC 节省 43.4%）在 UVG 数据集上，以 PSNR 为单位测量）。 1🔤
> ^XJ764926aNXTAQGZ4p1

### 技术细节

> [!note] Page 4
> 
> In FFNeRV, linear interpolation over the temporal dimension is used to obtain a slice that is used as the input feature map. In our case, we utilize both of the frame index and the frame-based coordinates, i.e., (uframe, vframe, t), for interpolating the feature patches.
> 
> ---
> 🔤在 FFNeRV 中，使用时间维度上的线性插值来获取用作输入特征图的切片。在我们的例子中，我们利用帧索引和基于帧的坐标，即（uframe，vframe，t）来插值特征块。🔤
> ^JLM9R3WGaNXTAQGZ4p4

> [!note] Page 4
> 
> To maintain a compact multi-resolution grid, we increase the number of channels when reducing the temporal resolution at each grid level
> 
> ---
> 🔤为了保持紧凑的多分辨率网格，我们在降低每个网格级别的时间分辨率时增加通道数🔤
> ^LH3HIX4AaNXTAQGZ4p4

> [!note] Page 5
> 
> Previous work [12] has shown that bilinear interpolation does not perform as well as convolutional layers. However, we observed that it is actually a better choice when the parameter count is fixed.
> 
> ---
> 🔤之前的工作 [12] 表明双线性插值的性能不如卷积层。然而，我们观察到，当参数数量固定时，它实际上是一个更好的选择。🔤
> ^4H4PUXC9aNXTAQGZ4p5

> [!note] Page 5
> 
> While we can generate high-resolution maps using parameter-free bilinear interpolation, the resulting maps are smoothed, and subsequent neural network layers may struggle to produce high-frequency output from them. One way to model high-frequency signals is to introduce positional encoding [40] during upsampling.
> 
> ---
> 🔤虽然我们可以使用无参数双线性插值生成高分辨率地图，但生成的地图是平滑的，后续的神经网络层可能难以从中产生高频输出。对高频信号建模的一种方法是在上采样期间引入位置​​编码[40]。🔤
> ^Z7J67SEIaNXTAQGZ4p5

> [!note] Page 5
> 
> To address this limitation, we introduce a novel grid-based encoding approach called hierarchical encoding, which boosts the upsampling capability of bilinear interpolation without significantly increasing the storage cost.
> 
> ---
> 🔤为了解决这个限制，我们引入了一种新颖的基于网格的编码方法，称为分层编码，它增强了双线性插值的上采样能力，而不会显着增加存储成本。🔤
> ^RJNNZ8FQaNXTAQGZ4p5

> [!note] Page 6
> 
> When configuring HiNeRV as a patch-wise representation, we perform computation in overlapped patches, where we refer to the overlapped part as paddings, and the amount of padding pixels depends on the network configuration (e.g. the kernel sizes and/or the number of bilinear interpolation/convolutional layers).
> 
> ---
> 🔤当将 HiNeRV 配置为 patch-wise 表示时，我们在重叠的 patch 中执行计算，其中我们将重叠部分称为填充，填充像素的数量取决于网络配置（例如内核大小和/或双线性的数量）插值/卷积层）。🔤
> ^7KXXMTLZaNXTAQGZ4p6

