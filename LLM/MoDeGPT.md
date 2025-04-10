---
title: "MODEGPT: MODULAR DECOMPOSITION FOR  LARGE LANGUAGE MODEL COMPRESSION"
conference: ICLR 2025
---
## Abstract
本文利用矩阵分解技术来压缩大模型的权重。
其特点在于，他对一对权重进行联合的分解，也并不需要额外的参数来进行复原。在推理阶段也不需要额外的计算，同时保留了 LLM 大部分性能。

![[Pasted image 20250410151800.png]]
上图展示了两种“极端”的策略，MoDeGPT 看上去像是两种策略的折衷。
此图展示了 decoder layer 的一个流程，也就是 MHA+MLP 的形式。
**SVD**：svdLLM 方法单独来考虑每一个权重矩阵，单独把他们分解成一对低秩小矩阵的形式。这种做法