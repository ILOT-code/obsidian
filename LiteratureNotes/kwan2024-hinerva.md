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

> [!note] Page 3
> 
> uality of INRs by using adaptive pruning and quantization-aware training. 4) The compression performance of the proposed method is superior to existing INR models, and is comparable to many conventional/le
> 
> ---
> 🔤通过使用自适应修剪和量化感知训练来提高 INR 的质量。 4）所提出的方法的压缩性能优于现有的INR模型，并且可以与许多常规/le🔤
> ^RZ8U4VXUaNXTAQGZ4p3

### 创新点

> [!note] Page 2
> 
> ive pruning technique to reduce the negative impact of model pruning. Secondly, quantization-aware training is applied for fine-tuning the model performance before quantization. This enables lower bit depth quantization which achieves an improve rate-distortion trade-off. The proposed method has been tested against existing INR-based video coding methods and state-ofthe-art conventional and learning-based video codecs on the UVG [38] and MCL-JCV [57] datasets. Notwithstanding the fact tha
> 
> ---
> 🔤ive 剪枝技术，减少模型剪枝的负面影响。其次，量化感知训练用于在量化之前微调模型性能。这实现了较低的位深度量化，从而实现了改进的率失真权衡。该方法已在 UVG [38] 和 MCL-JCV [57] 数据集上针对现有的基于 INR 的视频编码方法以及最先进的传统和基于学习的视频编解码器进行了测试。尽管事实上🔤
> ^L46KFEL7aNXTAQGZ4p2

### 技术细节

> [!note] Page 2
> 
> ession, the training of INR models is equivalent to the encoding process, implying that most INR-based codecs require a long encoding runtime to obtain a satisfactory rate-quality performance [12]. However, some
> 
> ---
> 🔤会话中，INR模型的训练相当于编码过程，这意味着大多数基于INR的编解码器需要较长的编码运行时间才能获得令人满意的码率质量性能[12]。然而，一些🔤
> ^KUGH44LUaNXTAQGZ4p2

