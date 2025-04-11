---
title: "MEDA: Dynamic KV Cache Allocation for Efficient Multimodal Long-Context Inference"
conference: NAACL 2025
---
# Abstract
之前看过一些针对 text-only 大模型的 kv-cache 汰换策略，MEDA 则为多模态模型引入 kv-cache 汰换策略。在多模态的语境下，本文设计了一种分配策略，来为不同的 decoder layer 分配不同大小的 kv-cache size。在层的内部，设计了 ka-cache 的淘汰、融合机制。在 llava 等视觉模型上取得了更好的结果。

在多模态 LLM 的不同层中，attention score 的分布，以及“cross-modal attention entropy”的分布很不一样。
![[Pasted image 20250411112839.png]]

## 层间 ka-cache size 划分
如果一个层，他在生成新的 token 时，attention score 是均匀分布的（对过去 $n$ 个 kv-cache 的分数都是 $\frac{1}{n}$），那么这一层就很混乱，需要保留更多的 kv-cache，反之，只需保留少量 kv-cache。因此可以利用 attention score 的熵作为划分的依据。

