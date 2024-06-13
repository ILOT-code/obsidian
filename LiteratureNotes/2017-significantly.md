---
zotero-key: ITE5L9D4
zt-attachments:
  - "182"
title: Significantly improving lossy compression for scientific data sets based on multidimensional prediction and error-controlled quantization
authors: []
conference: xxx
citekey: 2017-significantly
tags: []
---
# Significantly improving lossy compression for scientific data sets based on multidimensional prediction and error-controlled quantization

**文章链接**: [Zotero](zotero://select/library/items/ITE5L9D4) [attachment](<file:///home/ilot/Zotero/storage/86ZI4D8E/Tao%20%E7%AD%89%20-%202017%20-%20Significantly%20improving%20lossy%20compression%20for%20scie.pdf>)
**网页链接**: [URL](https://ieeexplore.ieee.org/abstract/document/7967203/)
## Abstract

>[!abstract]
>当今的科学模拟计算正在产生极其大量的数据，使得数据存储和分析对科学研究变得更具挑战性。本文为大规模科学数据设计了一种新的错误控制有损压缩算法。
>设计了一种预测器模式，显著提升了预测的准确率。





## Prediction Model for Multidimensional Scientific Data Sets
证明见原文，感觉很古怪，它认为 $0^0=1$ 从而得到证明。

## In-Depth Analysis of the Best Layer for Multilayer Prediction Model
一方面，通过增加层数可以实现更准确的预测，这将在多个维度上带来更多有用的信息。另一方面，来自更远距离的数据会给预测带来更多不相关的信息（噪声），这意味着太多的层会降低我们预测的准确性。

## Error-Controlled Quantization
![[Pasted image 20240613163524.png]]
以预测值为中心，间隔为 2\*bound，扩展出 $2^m-1$ 个间隔。实际的数落到第 $i$ 个间隔，就用 $i$ 的二进制数去编码。如果不能落到这些间隔，用 $0$ 来编码，并采取特殊处理。
实际上，分布不均匀，实际采用了哈夫曼编码来对这 255 个数进行编码。
![[Pasted image 20240613164024.png]]
## Adaptive Scheme for Number of Quantization Intervals
