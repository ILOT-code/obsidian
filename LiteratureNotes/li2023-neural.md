---
zotero-key: MSI2FV5F
zt-attachments:
  - "227"
title: Neural Video Compression with Diverse Contexts
authors:
  - Jiahao Li
  - Bin Li
  - Yan Lu
doi: 10.48550/arXiv.2302.14402
conference: xxx
citekey: li2023-neural
tags: []
---
# Neural Video Compression with Diverse Contexts

**文章链接**: [Zotero](zotero://select/library/items/MSI2FV5F) [attachment](<file:///home/ilot/Zotero/storage/8JGHTHU7/Li%20%E7%AD%89%20-%202023%20-%20Neural%20Video%20Compression%20with%20Diverse%20Contexts.pdf>)
**网页链接**: [URL](http://arxiv.org/abs/2302.14402)
## Abstract

>[!abstract]
>For any video codecs, the coding efficiency highly relies on whether the current signal to be encoded can find the relevant contexts from the previous reconstructed signals. Traditional codec has verified more contexts bring substantial coding gain, but in a time-consuming manner. However, for the emerging neural video codec (NVC), its contexts are still limited, leading to low compression ratio. To boost NVC, this paper proposes increasing the context diversity in both temporal and spatial dimensions. First, we guide the model to learn hierarchical quality patterns across frames, which enriches long-term and yet high-quality temporal contexts. Furthermore, to tap the potential of optical flow-based coding framework, we introduce a group-based offset diversity where the cross-group interaction is proposed for better context mining. In addition, this paper also adopts a quadtree-based partition to increase spatial context diversity when encoding the latent representation in parallel. Experiments show that our codec obtains 23.5% bitrate saving over previous SOTA NVC. Better yet, our codec has surpassed the under-developing next generation traditional codec/ECM in both RGB and YUV420 colorspaces, in terms of PSNR. The codes are at https://github.com/microsoft/DCVC.

## Method
### overview
![[Pasted image 20240608182623.png]]
主要有 3 个模块 $f_{frame},f_{Tcontext},f_{motion}$
$f_{motion}$ 是一个光流算法模块，它通过对上一帧的解码图像 $\hat{x}_{t-1}$ 和当前帧 $x_t$ 计算得到运动向量 $v_t$，它被解码后得到 $\hat{v}_t$.
$f_{Tcontext}$ 提取使用运动向量修正后的时间上下文 $C_t$
$f_{frame}$ 根据 $C_t$ 进行编解码，解码完成后生成重建帧 $\hat{x}_t$ 和 $F_t$

### Hierarchical Quality Structure
传统编码器广泛使用这种结构，也就是所有帧被分到不同的层，不同层使用不同的 QP 来编码。这种设计带来了总体性能的提升。首先，它周期性的提升了一些帧的质量，缓和了误差在帧间的传递。其次，在使用多个参考帧进行加权预测的情况下，同时参考最近帧和较远的高质量帧更能实现好的预测结果。

本文使用联合训练多帧的损失函数来实现这种层次质量结构：
$$
L_{all}=\frac{1}{T}\sum_{t}^{T}(w_{t}\cdot\lambda\cdot dist(x_{t}-\hat{x}_{t})+r_{t})
$$
为不同帧设置权重参数，使其周期性地生成高质量解码帧 $\hat{x}_t$ 和传递的特征 $F_t$.
另外，通过这种级联多帧的训练方式，在编码帧 $x_t$ 时，特征 $F_{t-1}$ 不仅包含短程的上下文，还包含了长程的不断更新的高质量上下文。
![[Pasted image 20240608185226.png]]

### Group-Based Offset Diversity