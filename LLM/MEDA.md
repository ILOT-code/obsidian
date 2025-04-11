---
title: "MEDA: Dynamic KV Cache Allocation for Efficient Multimodal Long-Context Inference"
conference: NAACL 2025
---
# Abstract
之前看过一些针对单语言模态的 kv-cache 汰换策略，MEDA 则为多模态模型引入 kv-cache 汰换策略。在多模态的语境下，本文设计了一种分配策略，来为不同的 decoder layern 分配不同大小的 kv-cache size。在层的内部，设计了 ka-cache 的淘汰、融合机制。在 llava 这个视觉模型上取得了更好的结果。


