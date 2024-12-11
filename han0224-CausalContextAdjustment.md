---
zotero-key: IW 2 FDBIC
zt-attachments:
  - "145"
title: Causal Context Adjustment Loss for Learned Image Compression
authors:
  - Minghao Han
doi: 
conference: None
citekey: 
tags:
---
# Causal Context Adjustment Loss for Learned Image Compression

## 问题分析
LIC 的成功基于 VAE 模式，并引入各种 causal context 来进一步减少码流。
对于 causal context 为什么能 work, 可以很直观的进行解释：它为当前待解码的 latent 引入了更多的参考，而 latent 在空间上、通道上都具有一定的关联。

z 的引入其实已经为 y(latent) 之间的联系进行了建模了，继续引入 context 的话，就是希望利用更多的信息来减少当前 y 的不确定性。因此，应当鼓励编码器，在生成的早阶段的 y 中，包含更多的多预测后阶段 y 有益的信息。目前各种 causal context 的设计仅仅通过总体的损失函数 $D+\lambda R$ 来隐式的进行。

本文则提出了一种损失函数，它可以被应用在任何 causal context 的模型中，直接来调整 encoder, 为早期的 y 赋予更多信息。

## Method



