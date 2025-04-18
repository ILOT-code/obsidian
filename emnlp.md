大语言模型在处理各种语言任务时展现了惊人的性能，其中key, value 向量对过去信息的总结提炼能力发挥了重要作用。然而，Key-Value (KV) cache 的存储空间随着输入序列的长度线性增长，这限制了大模型处理长下文任务的能力。为了解决这个问题，我们提出了 GCJCP，它是一种为长下文场景设计的、无需额外训练的 KV cache 通道剪枝方法。与之前的方法孤立看待各个通道不同，我们综合考虑了各通道之间的相互影响，设计了一种更好的通道选择方法。

### Introduction
Scaling Law 定律表明大模型的性能会随着模型和数据规模的提升提升。除了提升模型的参数规模之外，越来越多的研究都提升了模型能够处理的上下文长度。例如：。。。更长的上下文长度允许大模型处理更多种类、更复杂的问题，允许人类用户和大模型进行更长、更多轮次的对话。

尽管这些大模型有着强大的能力，更长的上下文长度也显著增加了推理的负担，这限制了大模型的应用。在推理过程中，每个 transformer 层需要存储的 KV-cachee 随着生成的 token 数量线性增长。

### Method
在这一章节中，我们会详细介绍本文的方法。在此之前，先进行记号上的规定。为简单考虑，我们只考虑某一解码层的某一个 Head。 $\mathbf{W}_{q} \in \mathbb{}{R}^{d\times d}, \mathbf{W}_{k} \in \mathbb{R}^{d\times d}, \mathbf{W}_{v} \in \mathbb{R}^{d\times d}$ 分别表示某一解码层中注意力模块的权重矩阵， $X_{p}\in \mathbb{R}^{L_{p}\times d}$ 表示 prompts 对应的 token 矩阵， $L_{p}$ 表示 prompts 的总长度。 $\mathbf{X}_p^{obs}=\mathbf{X}_{p}[-L_{obs}:,:] \in \mathbb{R}^{L_{obs} \times d}$ 观察窗口， $L_{obs}$ 表示观察窗口的长度，$L_{prefix}=L_{p}-L_{obs}$ 是前缀的长度；$\mathbf{x}^{t}\in \mathbb{R}^{1\times d}$ 表示第 $t$ 个生成步中得到的 token。$\mathbf{K}_{p} = \mathbf{X}_{p}\mathbf{W}_{k}\in \mathbb{R}^{L_{p}\times d}$ 表示 prompt 得到的 key 的缓存, $\mathbf{Q}_{p}=\mathbf{X}_{p}\mathbf{W}_{q}\in \mathbb{R}^{L_{p}\times d}$ 表示 prompt 得到的 query 矩阵。$\mathbf{Q}_{p}^{obs}=\mathbf{Q}_{p}[-L_{obs}:,:],\mathbf{K}_{p}^{obs}=\mathbf{K}_{p}[-L_{obs}:,:]$ 表示观察窗口对应的 query 和 key 矩阵。
#### Motivation
先前研究发现，Key cache 中，只有部分的通道有着很大的值，而其它通道的值则偏小，这意味着这些 significant 通道相较于其它通道有着更重要的影响，暗示着可以通过对 key cache 进行通道剪枝的来节省显存空间。在某个 generation step 中，$\mathbf{Q}, \mathbf{K}\in \mathbb{R}^{L\times d}$ 分别是该 attention module 中的 query 和 key 矩阵，注意力分数计算方式为：$softmax(\frac{\mathbf{Q}\mathbf{K}^{T}}{\sqrt{ d }})$。
基于此，Thinkh 通过求解这个最优化问题来找到需要被删除的通道。
$$
\begin{aligned}
\min_{\mathbf{S}}&\left\|\mathbf{Q}\mathbf{K}^{T}-\mathbf{Q}\mathbf{S}\mathbf{K}^T\right\|_F\\\text{subject to}&\operatorname{trace}(\mathbf{S})=\lfloor(1-\lambda)d\rfloor\\&\mathbf{S}=\operatorname{diag}(s_1,s_2,\ldots,s_D),\text{ where }s_j\in\{0,1\}
\end{aligned}
$$
其中 $\lambda$ 是通道削减的比率，$\mathbf{S}$ 是对角矩阵，对角线上为 1 的元素对应着需要保留的通道。

为了实际上真正减少显存的利用，每个 generation step 对应的 $\mathbf{S}$ 应当相同，否则我们仍需要保存全通道的 Key cache 以供后续的步骤使用，并且这种结构化剪枝才能在硬件上实现计算加速。这意味着在较早的阶段我们就应该计算出 $\mathbf{S}$。

SnapKV 实验证实，在 LLM 中，生成过程中，新的 token 在 prompts 上的注意力模式是一致的，且与观察窗口中对应的模式非常相似，这意味着 $Softmax(x^{t}\mathbf{W}_{q} \mathbf{K}_{p}^{T}[: L_{prefix},:]) \approx Sum(Softmx(\mathbf{X}_{p}^{obs}\mathbf{W}_{q}\mathbf{K}_{p}^{T}[: L_{prefix},:]))$。

因此， Think 将优化问题改为：
$$
\begin{aligned}
\min_{\mathbf{S}}&\left\|\mathbf{Q}_{p}^{obs}\mathbf{K}_{p}^{T}-\mathbf{Q}_{p}^{obs}\mathbf{S}\mathbf{K}_{p}^T\right\|_F\\\text{subject to}&\operatorname{trace}(\mathbf{S})=\lfloor(1-\lambda)d\rfloor\\&\mathbf{S}=\operatorname{diag}(s_1,s_2,\ldots,s_D),\text{ where }s_j\in\{0,1\}
\end{aligned}
$$
这样在 prompt 上就能得到 $\mathbf{S}$，同时观察窗口的引入也降低了计算量。

为解决这个优化问题，Think 为每个通道进行评分，通道 $j$ 的评分为 $score[j]=\| \mathbf{Q}_{p}^{obs}[:,j]\mathbf{K}_{p}^{obs}[:,j]^{T}\|_{F}$，并保留分数最高的那些通道。
#### Align
先前的研究表明，最近的一些 token 对结果生成有着重要影响，因此很多方法(SnapKV, Think)都完整保留了最近的一些 token, 而不对它们进行通道或数量上的削减。因此优化方程的优化目标应当由 $\mathbf{Q}_{p}^{obs}\mathbf{K}_{p}^{T}$ 改为 $\mathbf{Q}_{p}^{obs}K_{p}[:L_{p}-recent\_size,:]^{T}$。这一项调整被我们称为对齐。
#### GGJC
ThinkKV 给出的贪心算法只单独考虑了各个通道的分数，实际上对于这个优化问题，每个通道的选择对误差的影响也其它的通道相关，发掘这种相关性能设计出更好的优化算法解决该问题。

用 $A=\{i|s_{i}=1\}, B=\{i|s_{i}=0\}$, $A \cap B = \varnothing$，分别表示被保留通道和舍弃通道的下标集合，用 $\mathbf{q}_{i},\mathbf{k}_{i}$ 表示 $\mathbf{Q}_{p}^{obs}, \mathbf{K}_{p}$ 的第 $i$ 列。那么可以得到(详细的推导见附录)：
$$
\begin{aligned}

\left\|\mathbf{Q}_{p}^{obs}\mathbf{K}_{p}^{T}-\mathbf{Q}_{p}^{obs}\mathbf{S}\mathbf{K}_{p}^T\right\|_F^{2}= 
=\sum_{i\in B} \sum_{j\in B} \mathbf{k}_{i}^{T} \mathbf{k}_{j} \times \mathbf{q}_{i}^{T}\mathbf{q}_{j}

\end{aligned}
$$
单独考虑 $B$ 集合中的某个 $i$，它带来的误差是：
$$
\begin{aligned}
\Delta_{i} &=\sum_{j\in B}\mathbf{k}_{i}^{T} \mathbf{k}_{j} \times \mathbf{q}_{i}^{T}\mathbf{q}_{j} \\
&= \sum_{j\in B-i}\mathbf{k}_{i}^{T} \mathbf{k}_{j} \times \mathbf{q}_{i}^{T}\mathbf{q}_{j} + \|\mathbf{q}_{i}\mathbf{k}_{i}^{T}\|_{F}^{2}
\end{aligned}
$$
这里的第一项描述了通道之间的相互作用产生的影响，第二项描述了单个通道自身对误差的影响（也就是 Think 中只考虑的 score 的计算方式）。

这个优化问题可以看成这样的问题：一张有 $d$ 个节点的完全图 $\mathcal{G}$，节点 $i$ 自身的权重为 $\|\mathbf{q}_{i}\mathbf{k}_{i}^{T}\|_{F}^{2}$，节点 $i,j$ 之间边的权重是 $\mathbf{k}_{i}^{T} \mathbf{k}_{j} \times \mathbf{q}_{i}^{T}\mathbf{q}_{j}$；如何从 $\mathcal{G}$ 中选择具有 $\lambda d$ 个节点的子图 $\mathcal{G}'$，使得 $\mathcal{G}'$ 的权重和最小。最终，$\mathcal{G'}$ 中的节点对应着需要删除的通道。

我们为该问题设计了考虑边权的贪心算法。
