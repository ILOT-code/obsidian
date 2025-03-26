# TreeKV: Smooth Key-Value Cache Compression with Tree Structures

## Introduction
KV-cache 随着 generation step 的增长，线性增长，若无法摆脱线性增长的机制，就难以面对长序列生成。本文设计了一种 KV-cache 的淘汰策略，它的 KV-cache size 是固定的。

现有的相关研究，大多基于全局的注意力分数来进行淘汰。这种基于全局选择的机制存在着明显的区域偏好，也就是侧重于保存某些位置的 kv-cache，这会限制 LLM 在生成过程中保持好的全局视野。

本文先使用小波分解来分析 kv-cache，基于发现的规律提出了 TreeKV，他的 kv-cacheuj 保留结果更平滑。总体上还是多保留相近的 kv-cache, 而去除远处的 kv-cache.
![[Pasted image 20250326122215.png]]

## Preliminary
**KV-cache**
$\mathbf{x}^{(t)}\in \mathbb{R}^{1\times d}$ 是新来的 token，通过它生成新的 q, k, v

$$
\mathbf{q}^{(t)}=\mathbf{x}^{(t)}\mathbf{W}_Q,\mathbf{k}^{(t)}=\mathbf{x}^{(t)}\mathbf{W}_K,\mathbf{v}^{(t)}=\mathbf{x}^{(t)}\mathbf{W}_V.
$$
然后进行拼接：
$$
\mathbf{K}^{(t)}=\begin{pmatrix}\mathbf{K}^{(t-1)}\\\mathbf{k}^{(t)}\end{pmatrix},\quad\mathbf{V}^{(t)}=\begin{pmatrix}\mathbf{V}^{(t-1)}\\\mathbf{v}^{(t)}\end{pmatrix}.
$$
最后进行 attention 分数的计算，生成新的 token.
$$
\mathbf{a}^{(t)}=\text{SoftMax}\left( \frac{\mathbf{q}^{(t)}\mathbf{K}^{(t)^{T}}}{\sqrt{d}}
\right)
,\quad\mathbf{o}^{(t)}=\mathbf{a}^{(t)}\cdot\mathbf{V}^{(t)}.
$$
**多级小波分解**
采用哈尔小波分解：
$$
\mathbf{A}_1[n]=\sum_{k=-\infty}^\infty\mathbf{s}[k]\mathbf{g}[2n-k],\\\mathbf{D}_1[n]=\sum_{k=-\infty}^\infty\mathbf{s}[k]\mathbf{h}[2n-k].
$$
$$
\mathbf{g}[i]=\left\{\begin{matrix}\sqrt{2}/2&i=0,1\\0&\text{otherwise}\end{matrix}\right.,\mathbf{h}[i]=\left\{\begin{matrix}-\sqrt{2}/2&i=0\\\sqrt{2}/2&i=1\\0&\text{otherwise}\end{matrix}\right..
$$
复原的过程为：
$$
R(\mathbf{A}_1,\mathbf{D}_1)[n]=\begin{cases}\frac{\sqrt{2}}{2}\left(\mathbf{A}_1\left[\frac{n+1}{2}\right]+\mathbf{D}_1\left[\frac{n+1}{2}\right]\right)&\text{odd}n\\\frac{\sqrt{2}}{2}\left(\mathbf{A}_1\left[\frac{n}{2}\right]-\mathbf{D}_1\left[\frac{n}{2}\right]\right)&\text{even}n\end{cases}
$$
它不仅能表达频率信号，也能表达更 local 的信息。
经过多级的分解，原始信号被分解为：$[\mathbf{A}_L,\mathbf{D}_L,\mathbf{D}_{L-1},\cdots,\mathbf{D}_1]$ 它们的频率逐渐上升。


在 self-attention 中，输出是注意力权重向量与 V 矩阵的乘，因此考虑对下面这个式子进行分解：
$$
\mathbf{s}=\mathbf{a}^{(t)^{T}}\circ\mathbf{V}^{(t)}=\begin{pmatrix}\mathbf{a}^{(t)}(1)\mathbf{V}^{(t)}(1)\\\vdots\\\mathbf{a}^{(t)}(t)\mathbf{V}^{(t)}(t)\end{pmatrix}.
$$
沿着时间维度，对它进行小波分解。并使用各级的信号 $\mathbf{D}_{L}$ 对它进行复原，得到平均的幅值大小。
$$
\mathrm{Rec}(\mathbf{D}_L)=R(R(\cdots R(\mathbf{0},\mathbf{D}_L)\cdots,\mathbf{0}),\mathbf{0}).
$$

随着序列的增长，各频率分量的幅值在上涨，并且越高频的成分增长越大。这就意味着，随着序列的增长，相邻的 token 的贡献差异越来越大。这很可能是因为新的 token 的贡献会比之前大，暗示着从远到近，token 的贡献光滑增长。
![[Pasted image 20250326124835.png]]

## TreeKV
因此，设计的结构应该满足两个要求：
1. 光滑增长
2. 局部比较与淘汰

### Decoding stage
也就是正常 generation 的过程。
![[Pasted image 20250326130516.png]]
可视化的淘汰过程：
![[Pasted image 20250326130600.png]]

每次