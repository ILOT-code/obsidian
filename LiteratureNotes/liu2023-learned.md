---
zotero-key: L2I99PB7
zt-attachments:
  - "185"
title: Learned Image Compression with Mixed Transformer-CNN Architectures
authors:
  - Jinming Liu
  - Heming Sun
  - Jiro Katto
doi: 10.48550/arXiv.2303.14978
conference: xxx
citekey: liu2023-learned
tags:
  - ObsCite
---
# Learned Image Compression with Mixed Transformer-CNN Architectures

**文章链接**: [Zotero](zotero://select/library/items/L2I99PB7) 
**网页链接**: [URL](http://arxiv.org/abs/2303.14978)
## Abstract

>[!abstract]
>Learned image compression (LIC) methods have exhibited promising progress and superior rate-distortion performance compared with classical image compression standards. Most existing LIC methods are Convolutional Neural Networks-based (CNN-based) or Transformer-based, which have different advantages. Exploiting both advantages is a point worth exploring, which has two challenges: 1) how to effectively fuse the two methods? 2) how to achieve higher performance with a suitable complexity? In this paper, we propose an efficient parallel Transformer-CNN Mixture (TCM) block with a controllable complexity to incorporate the local modeling ability of CNN and the non-local modeling ability of transformers to improve the overall architecture of image compression models. Besides, inspired by the recent progress of entropy estimation models and attention modules, we propose a channel-wise entropy model with parameter-efficient swin-transformer-based attention (SWAtten) modules by using channel squeezing. Experimental results demonstrate our proposed method achieves state-of-the-art rate-distortion performances on three different resolution datasets (i.e., Kodak, Tecnick, CLIC Professional Validation) compared to existing LIC methods. The code is at https://github.com/jmliu206/LIC_TCM.

## Comments

### 前置工作

> [!note] Page 1
> 
> For CNNbased example, Cheng et al. [6] proposed a residual blockbased image compression model. For transformer-based example, Zou et al. [37] tried a swin-transformer-based image compression model. These two kinds of methods have different advantages.
> 
> ---
> 🔤对于基于CNN的例子，Cheng等人[6]提出了一种基于残差块的图像压缩模型。对于基于变换器的例子，Zou等人[37]尝试了一种基于swin-transformer的图像压缩模型。这两种方法有不同的优点。🔤
> ^89J8R5NZaFDXJXK2Wp1

> [!note] Page 4
> 
> The Residual Block with stride (RBS), Residual Block Upsampling (RBU) and subpixel conv3x3 are proposed by [6].
> 
> ---
> 🔤[6]提出了具有步长的残差块（RBS）、残差块上采样（RBU）和子像素conv3x3。🔤
> ^DF3G3WY3aFDXJXK2Wp4

### 黄色

> [!note] Page 1
> 
> 1) how to effectively fuse the two methods? 2) how to achieve higher performance with a suitable complexity?
> 
> ---
> 🔤1） 如何有效地融合这两种方法？2） 如何在适当的复杂度下实现更高的性能？🔤
> ^GR9KAJ7TaFDXJXK2Wp1

### 绿色

> [!note] Page 1
> 
> we propose an efficient parallel Transformer-CNN Mixture (TCM) block with a controllable complexity to incorporate the local modeling ability of CNN and the non-local modeling ability of transformers to improve the overall architecture of image compression models.
> 
> ---
> 🔤我们提出了一种具有可控复杂性的高效并行变换器CNN混合（TCM）块，以结合CNN的局部建模能力和变换器的非局部建模能力来改进图像压缩模型的整体架构。🔤
> ^E8LCCYVRaFDXJXK2Wp1

> [!note] Page 2
> 
> To overcome that problem, we try to move attention modules to the channel-wise entropy model which has 1/16 input size compared with that of main path to reduce complexity.
> 
> ---
> 🔤为了克服这个问题，我们试图将注意力模块转移到信道熵模型中，与主路径相比，该模型的输入大小为1/16，以降低复杂性。🔤
> ^43WNX8WQaFDXJXK2Wp2

> [!note] Page 4
> 
> In order to combine the residual network with the Swin-Transformer more effectively, we divide TCM into two stages with similar processes.
> 
> ---
> 🔤为了更有效地将残差网络与Swin变压器相结合，我们将TCM分为两个过程相似的阶段。🔤
> ^BPW5EY6NaFDXJXK2Wp4

> [!note] Page 4
> 
> The benefit of this is that the residual networks are inserted into the common two consecutive Swin-transformer blocks, which can be more effective for feature fusion.
> 
> ---
> 🔤这样做的好处是将残差网络插入到公共的两个连续的Swin变换器块中，这可以更有效地进行特征融合。🔤
> ^UNJS86FRaFDXJXK2Wp4

### 蓝色

> [!note] Page 4
> 
> This suggests that our model pays more attention to neighbor regions, and has a similar local modeling ability of CNN.
> 
> ---
> 🔤这表明我们的模型更加关注相邻区域，并且具有与CNN相似的局部建模能力。🔤
> ^S5PXNVA6aFDXJXK2Wp4

> [!note] Page 5
> 
> This means that our model also has the long-distance modeling ability of transformers.
> 
> ---
> 🔤这意味着我们的模型也具有变压器的远程建模能力。🔤
> ^LRAHEFYLaFDXJXK2Wp5

> [!note] Page 5
> 
> This shows that our model has better modeling ability compared to the CNNbased model.
> 
> ---
> 🔤这表明，与基于细胞神经网络的模型相比，我们的模型具有更好的建模能力。🔤
> ^F2YUER95aFDXJXK2Wp5

### 紫色

> [!note] Page 2
> 
> efficiently incorporate the local modeling ability of CNN and the non-local modeling ability of transformers, while maintaining controllable complexity.
> 
> ---
> 🔤有效地结合了CNN的局部建模能力和变压器的非局部建模能力，同时保持可控的复杂性。🔤
> ^8J4JQ9NFaFDXJXK2Wp2

> [!note] Page 6
> 
> It suggests that the model with SWAtten can have less information loss and get a higher quality decompressed image.
> 
> ---
> 🔤结果表明，SWAtten模型可以减少信息损失，得到更高质量的解压缩图像。🔤
> ^84KQ5QWQaFDXJXK2Wp6

