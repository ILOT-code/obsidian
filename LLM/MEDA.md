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

作者对每一层分别计算 text token 查询 visual token 的 attention，以及 visual token 查询 text token 的 attention.
$$
\begin{aligned}
\mathbf{A}_{\mathrm{TV}}^l=\mathrm{Softmax}\left(\mathbf{Q}_T^l\left(\mathbf{K}_V^l\right)^\top/\sqrt{D}\right),\\\mathbf{A}_{\mathrm{VT}}^l=\mathrm{Softmax}\left(\mathbf{Q}_V^l\left(\mathbf{K}_T^l\right)^\top/\sqrt{D}\right)
\end{aligned}
$$

最终，该层的“分配”分数被写为：
$$
\begin{align}
\mathbf{E}_{TV}^l&=\frac1{|T|}\sum_{i=1}^{n_T}\sum_{j=1}^{n_V}\mathbf{A}_{\mathrm{TV}}^l[i,j]\log\mathbf{A}_{\mathrm{TV}}^l[i,j],\\\mathbf{E}_{VT}^l&=\frac1{|V|}\sum_{i=1}^{n_T}\sum_{j=1}^{n_V}\mathbf{A}_{\mathrm{VT}}^l[i,j]\log\mathbf{A}_{\mathrm{VT}}^l[i,j],\\\mathbf{E}_{CM}^l&=-(\mathbf{E}_{TV}^l+\mathbf{E}_{VT}^l),
\end{align}
$$

这个值越大，表明这一层越混乱，需要分配更多的 kv-cache。
最终，第 $l$ 层分配的 kv-cache size 是：
$$
S_l=\alpha_l\cdot S,\:\alpha_l=\frac{\exp\left(\mathbf{E}_{\mathrm{CM}}^l\right)}{\sum_{k=1}^L\exp\left(\mathbf{E}_{\mathrm{CM}}^k\right)}\cdot L\cdot\rho,
$$
