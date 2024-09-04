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
>Learning-based video compression is currently a popular research topic, offering the potential to compete with conventional standard video codecs. In this context, Implicit Neural Representations (INRs) have previously been used to represent and compress image and video content, demonstrating relatively high decoding speed compared to other methods. However, existing INR-based methods have failed to deliver rate quality performance comparable with the state of the art in video compression. This is mainly due to the simplicity of the employed network architectures, which limit their representation capability. In this paper, we propose HiNeRV, an INR that combines light weight layers with novel hierarchical positional encodings. We employs depth-wise convolutional, MLP and interpolation layers to build the deep and wide network architecture with high capacity. HiNeRV is also a unified representation encoding videos in both frames and patches at the same time, which offers higher performance and flexibility than existing methods. We further build a video codec based on HiNeRV and a refined pipeline for training, pruning and quantization that can better preserve HiNeRV's performance during lossy model compression. The proposed method has been evaluated on both UVG and MCL-JCV datasets for video compression, demonstrating significant improvement over all existing INRs baselines and competitive performance when compared to learning-based codecs (72.3% overall bit rate saving over HNeRV and 43.4% over DCVC on the UVG dataset, measured in PSNR).

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

