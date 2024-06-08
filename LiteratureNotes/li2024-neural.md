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