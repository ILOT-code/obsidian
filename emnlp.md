大语言模型在处理各种语言任务时展现了惊人的性能，其中key, value 向量对过去信息的总结提炼能力发挥了重要作用。然而，Key-Value (KV) cache 的存储空间随着输入序列的长度线性增长，这限制了大模型处理长下文任务的能力。为了解决这个问题，我们提出了 GCJCP，它是一种为长下文场景设计的、无需额外训练的 KV cache 通道剪枝方法。与之前的方法孤立看待各个通道不同，我们综合考虑了各通道之间的相互影响，设计了一种更好的通道选择方法。

### Introduction
Scaling Law 定律表明大模型的性能会随着模型和数据规模的提升提升。除了提升模型的参数规模之外，越来越多的研究都提升了模型能够处理的上下文长度。例如：。。。更长的上下文长度允许大模型处理更多种类、更复杂的问题，允许人类用户和大模型进行更长、更多轮次的对话。

尽管这些大模型有着强大的能力，更长的上下文长度也显著增加了推理的负担，这限制了大模型的应用。在推理过程中，每个 transformer 层需要存储的 KV-cachee 随着生成的 token 数量线性增长。

### Method
在这一章节中，我们会详细介绍本文的方法。在此之前，先进行记号上的规定。为简单考虑，我们只考虑某一解码层的某一个 Head。 $\mathbf{W}_{q} \in \mathbb{}{R}^{d\times d}, \mathbf{W}_{k} \in \mathbb{R}^{d\times d}, \mathbf{W}_{v} \in \mathbb{R}^{d\times d}$ 分别表示某一解码层中注意力模块的权重矩阵， $X_{p}\in \mathbb{R}^{L_{p}\times d}$ 表示 prompts 对应的 token 矩阵， $L_{p}$ 表示 prompts 的总长度。 $\mathbf{X}_p^{obs}=\mathbf{X}_{p}[-L_{obs}:,:] \in \mathbb{R}^{L_{obs} \times d}$ 观察窗口， $L_{obs}$ 表示观察窗口的长度，$L_{prefix}=L_{p}-L_{obs}$ 是前缀的长度；$\mathbf{x}^{t}\in \mathbb{R}^{1\times d}$ 表示第 $t$ 个生成步中得到的 token。$Q_{p} \in \mathbb{R}^{L_{p}\times d}$ 表示 prompt 
#### Motivation
SnapKV 实验证实，在 LLM 中，生成过程中，新的 token 在 prompts 上的注意力模式是一致的，且与观察窗口中对应的模式非常相似，这意味着 $$
#### Align

#### GGJC

