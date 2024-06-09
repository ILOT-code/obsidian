---
zotero-key: V664IM3U
zt-attachments:
  - "223"
title: Neural Video Compression with Feature Modulation
authors:
  - Jiahao Li
  - Bin Li
  - Yan Lu
doi: 10.48550/arXiv.2402.17414
conference: xxx
citekey: li2024-neural
tags: []
---
# Neural Video Compression with Feature Modulation

**文章链接**: [Zotero](zotero://select/library/items/V664IM3U) [attachment](<file:///home/ilot/Zotero/storage/N4HNN8Z4/Li%20%E7%AD%89%20-%202024%20-%20Neural%20Video%20Compression%20with%20Feature%20Modulation.pdf>)
**网页链接**: [URL](http://arxiv.org/abs/2402.17414)
## Abstract

>[!abstract]
> The emerging conditional coding-based neural video codec (NVC) shows superiority over commonly-used residual coding-based codec and the latest NVC already claims to outperform the best traditional codec. However, there still exist critical problems blocking the practicality of NVC. In this paper, we propose a powerful conditional codingbased NVC that solves two critical problems via feature modulation. The first is how to support a wide quality range in a single model. Previous NVC with this capability only supports about 3.8 dB PSNR range on average. To tackle this limitation, we modulate the latent feature of the current frame via the learnable quantization scaler. During the training, we specially design the uniform quantization parameter sampling mechanism to improve the harmonization of encoding and quantization. This results in a better learning of the quantization scaler and helps our NVC support about 11.4 dB PSNR range. The second is how to make NVC still work under a long prediction chain. We expose that the previous SOTA NVC has an obvious quality degradation problem when using a large intra-period setting. To this end, we propose modulating the temporal feature with a periodically refreshing mechanism to boost the quality. Notably, under single intra-frame setting, our codec can achieve 29.7% bitrate saving over previous SOTA NVC with 16% MACs reduction. Our codec serves as a notable landmark in the journey of NVC evolution. The codes are at https://github.com/microsoft/DCVC.

## Introduction

conditional coding-based neural video codec (NVC) 尽管在率失真上有很好的表现，它面临着一些困境：

1. 如何在单个模型中，允许一个宽的解码质量范围。
    传统编码器可以自定义一个参数 QP 来控制解码质量和码率，但在 NVC 中，单个训练好的模型很难允许一个宽的质量范围。
2. 在长预测链之下，NVC 如何才能稳定工作。
   现有的一些 NVC 方法很难处理好时间误差累积问题，它们很多通过频繁地插入 I-帧来解决这样的问题，但很多情况下对于单个视频，是只允许存在 1 个 I-帧的。

本文设计了相应的策略来解决这些问题。

- We modulate the latent feature via learnable quantization scaler, where a uniform quantization parameter sampling mechanism is proposed to help its learning. It enables our DCVC-FM to support a wide quality range in a single model, and the rate control capability is demonstrated. 
- We not only exploit the training with longer video but also module the temporal feature with a periodically refreshing mechanism to boost the quality. These help our DCVC-FM to tackle the long prediction chain. •
- To further improve the practicality, we enable DCVC-FM to support both RGB and YUV colorspaces within a single model. Moreover, we demonstrate the low-precision inference with negligible bitrate increase. 
- Our DCVC-FM can outperform all traditional codecs under intra-period –1 setting. When compared with the previous SOTA NVC, 29.7% bitrate reduction is achieved with 16% MAC reduction. Our codec is an important milestone in the development of NVC.


本文是建立在作者 23 年发表在 cvpr 的工作上的: [[li2023-neural|(Jiahao Li, 2023)]]


## Method

### Overview
和 DDVC-DC 类似
![[Pasted image 20240609130927.png]]

### Wide Quality Range in a Single Model
首先，有一个质量区间 $[0,q_{num}-1]$，用户可以选择一个此区间的数 $q$ 来决定帧的质量。
编解码被分为了两段过程，高分辨率和低分辨率，$s_t^{enc}$ 和 $s_t^{dec}$ 分别被用来调制和解调两者中间的 latent。$s_t^{enc}$ 和 $s_t^{dec}$ 类似于 $\hat{I}=QS\cdot\lfloor\frac I{QS}\rceil$ 中的 $QS$，只是没有限定这个量化参数在编解码过程中需要相同，作者认为这样能够更加灵活地调整图像质量。也因此，量化过程并没有使用除法，而是使用乘法运算。
$s_t^{enc}$ 由两个可学习的参数 $s_{min}^{enc},s_{max}^{enc}$ 和自定义的 $q$ 来生成：
$$
\begin{align*}
s_t^{enc}=s_{min}^{enc}\cdot(\frac{s_{max}^{enc}}{s_{min}^{enc}})^{\frac{q_t}{q_num-1}}\\
s_t^{enc}=e^{\ln s_{min}^{enc}+\frac{q_t}{q_num-1}\cdot(\ln s_{max}^{enc}-\ln s_{min}^{enc})}
\end{align*}
$$
同时，$Loss_{RD}=R+\lambda D$ 中的 $\lambda$ 也由 $q$ 来决定：
$$
\lambda=e^{\ln\lambda_{min}+\frac{q_t}{q-num-1}\cdot(\ln\lambda_{max}-\ln\lambda_{min})}
$$
$\lambda_{max},\lambda_{min}$ 是预先就固定的数。
在实际训练过程中，会随机在 $[0,q_{num}-1]$ 取样得到 $q$，进行实验。$q$ 越大，$\lambda$ 越大，图像质量占比就越大，就会指导 $s_t^{enc}$ 的生成越趋于 1 的数。通过调整 $\lambda$ 的范围，就能实现很宽的图像质量范围。

$s_t^{enc}$ 对所有空间坐标的点都是一致的，这会导致忽略一些空间特征。为此，使用熵模型生成 spatial-channel-wise 的量化参数 $w_t^{enc}$, 它不仅有助于在每个位置实现精确调制，而且可以适应每个帧的视频内容，这种内容自适应的动态调制也可以提高最终的压缩效率。
![[Pasted image 20240609133159.png]]

通过对不同的帧给与不同的 $q$，可以实现质量的控制：
![[Pasted image 20240609135454.png]]

### Long Prediction Chain
即使DCVC-DC 引入分层质量结构来周期性地提高帧的质量，以减轻时间特征误差累积问题，当仅使用单个 I-帧时，问题依旧严重。
第一个改进是在训练期间增加视频帧数。虽然是一个简单的修改，但是还是很有帮助的。较长的视频可以识别长距离内的相似模式，然后更好地探索时间相关性。
第二个改进是对时间特征 $F$ 进行调制，定期更新它，以去除累积误差和不相关信息。
在新周期开始时，使用 FE 从 $\hat{x}_{t-1}$ 中提取时间特征，替换原来一直
![[Pasted image 20240609141247.png]]
![[Pasted image 20240609140704.png]]