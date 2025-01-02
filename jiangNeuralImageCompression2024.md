---
zotero-key: IW 2 FDBIC
zt-attachments: 
title: Neural Image Compression Using Masked Sparse Visual Representation
authors: Wei Jiang
doi: 
conference: WACV2024
citekey: 
tags:
---
# Neural Image Compression Using Masked Sparse Visual Representation

使用 VQ 技术的压缩方案具有一些优点：
1. 编解码端就算处于不同平台，也具有好的鲁棒性。


现有的基于 VQ 技术的图像压缩方案缺少平衡码率和失真的机制，导致它们走向了两个极端：
1. 低码率，高失真。代表：MAGE
2. 高码率，低失真。代表：AdaCode

MAGE 使用了单个 semantic-class-agnostic 的码本，AdaCode 使用了多个 semantic-class-dependent 的码本，并对多个码本的索引结果和权重 h 相乘。它们的设计无法动态地调控码率。
