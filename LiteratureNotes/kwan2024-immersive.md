---
zotero-key: F9DGBMFT
title: Immersive Video Compression using Implicit Neural Representations
authors:
  - Ho Man Kwan
  - Fan Zhang
  - Andrew Gower
  - David Bull
doi: 10.48550/arXiv.2402.01596
conference: preprint,arxiv2024
citekey: kwan2024-immersive
tags:
---
# Immersive Video Compression using Implicit Neural Representations

**文章链接**: [Zotero](zotero://select/library/items/F9DGBMFT) 
**网页链接**: [URL](http://arxiv.org/abs/2402.01596)

## Intro
本文提出了一种多视角视频的压缩方法。多视角视频一般由多台摄影机对一场景同时录制而来。
作者和 [[kwan2024-hinerva|(Ho Man Kwan, 2024)]] 是同一人，方法也是其扩展。
## Method
为每一个视角单独使用 Grid 和 temporal local grid，而网络则共享。

![[Pasted image 20240904194217.png]]



权重量化：对于模型参数 $\boldsymbol{\theta}=\{\theta_{i}\}$, 使用可学习的参数 $\boldsymbol{\delta} = \{\delta_i \}$ 来量化。
$$
\hat{\theta_i}=\delta\times\lfloor\frac{\theta_i}{\delta_i}\rceil.
$$
使用 Quant-Noise 技术来克服不可导问题。

Entropy regularisation：使用多元高斯分布对模型参数分布进行建模，$R=\sum_{i=0}^{|\boldsymbol{\hat{\theta}}|}-\mathrm{log}_2p(\hat{\theta_i})$
## Comments 
