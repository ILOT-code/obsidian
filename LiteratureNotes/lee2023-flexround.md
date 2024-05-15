---
zotero-key: HGG5B42S
zt-attachments:
  - "176"
title: "FlexRound: Learnable Rounding based on Element-wise Division for Post-Training Quantization"
authors:
  - Jung Hyun Lee
  - Jeonghoon Kim
  - Se Jung Kwon
  - Dongsoo Lee
doi: 10.48550/arXiv.2306.00317
conference: ICML2023
citekey: lee2023-flexround
tags:
---
# FlexRound: Learnable Rounding based on Element-wise Division for Post-Training Quantization

**文章链接**: [Zotero](zotero://select/library/items/HGG5B42S) [attachment](<file:///home/ilot/Zotero/storage/3FJYBIWV/Lee%20%E7%AD%89%20-%202023%20-%20FlexRound%20Learnable%20Rounding%20based%20on%20Element-wis.pdf>)
**网页链接**: [URL](http://arxiv.org/abs/2306.00317)
## Abstract

>[!abstract]
>Post-training quantization (PTQ) has been gaining popularity for the deployment of deep neural networks on resource-limited devices since unlike quantization-aware training, neither a full training dataset nor end-to-end training is required at all. As PTQ schemes based on reconstructing each layer or block output turn out to be effective to enhance quantized model performance, recent works have developed algorithms to devise and learn a new weight-rounding scheme so as to better reconstruct each layer or block output. In this work, we propose a simple yet effective new weight-rounding mechanism for PTQ, coined FlexRound, based on element-wise division instead of typical element-wise addition such that FlexRound enables jointly learning a common quantization grid size as well as a different scale for each pre-trained weight. Thanks to the reciprocal rule of derivatives induced by element-wise division, FlexRound is inherently able to exploit pre-trained weights when updating their corresponding scales, and thus, flexibly quantize pre-trained weights depending on their magnitudes. We empirically validate the efficacy of FlexRound on a wide range of models and tasks. To the best of our knowledge, our work is the first to carry out comprehensive experiments on not only image classification and natural language understanding but also natural language generation, assuming a per-tensor uniform PTQ setting. Moreover, we demonstrate, for the first time, that large language models can be efficiently quantized, with only a negligible impact on performance compared to half-precision baselines, achieved by reconstructing the output in a block-by-block manner.

## Comments

