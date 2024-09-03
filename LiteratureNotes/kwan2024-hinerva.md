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

### 代研究点

> [!note] Page 1
> 
> lity and speed [12]. To address this limitation, recent works have employed Convolutional Neural Networks
> 
> ---
> 🔤强度和速度[12]。为了解决这个限制，最近的工作采用了卷积神经网络🔤
> ^88RZQTGYaNXTAQGZ4p1

### 创新点

> [!note] Page 1
> 
> mploys depth-wise convolutional, MLP and interpolation layers to build the deep and wide network architecture with high capacity. HiNeRV is also a unified representation encoding videos in both frames and patches at the same time, which offers higher performance and flexibility than existing methods. We further build a video codec based on H
> 
> ---
> 🔤采用深度卷积、MLP 和插值层来构建具有高容量的深而宽的网络架构。 HiNeRV 也是一种同时对帧和补丁中的视频进行统一表示编码的方法，它比现有方法提供了更高的性能和灵活性。我们进一步构建了基于H的视频编解码器🔤
> ^5LYUFALNaNXTAQGZ4p1

### 疑惑点

> [!note] Page 1
> 
> index to video frame mapping [12, 31, 6, 27, 11]. These CNN-based INRs are capable of reconstructing video content with higher quality and with a faster decoding speed, when compared to MLP-based appro
> 
> ---
> 🔤视频帧映射的索引[12,31,6,27,11]。与基于 MLP 的应用程序相比，这些基于 CNN 的 INR 能够以更高的质量和更快的解码速度重建视频内容。🔤
> ^DF3FEUDKaNXTAQGZ4p1

### 性能成绩

> [!note] Page 1
> 
> ion. The proposed method has been evaluated on both UVG and MCL-JCV datasets for video compression, demonstrating significant improvement over all existing INRs baselines and competitive performance when compared to learning-based codecs (72.3% overall
> 
> ---
> 🔤离子。所提出的方法已在用于视频压缩的 UVG 和 MCL-JCV 数据集上进行了评估，与基于学习的编解码器（总体 72.3％）相比，证明了所有现有 INR 基线和竞争性能的显着改进🔤
> ^S89BMHUMaNXTAQGZ4p1

### 技术细节

> [!note] Page 1
> 
> ] and videos [50, 12]. INRs typically learn a coordinate to value mapping (e.g. mapping a pixel or voxel index to its color and/or occupancy) to support implicit reconstruction of the original signal. While the
> 
> ---
> 🔤] 和视频 [50, 12]。 INR 通常学习坐标到值的映射（例如，将像素或体素索引映射到其颜色和/或占用）以支持原始信号的隐式重建。虽然🔤
> ^JTKSTWA8aNXTAQGZ4p1

