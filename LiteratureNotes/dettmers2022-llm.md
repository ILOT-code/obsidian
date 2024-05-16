---
zotero-key: B68PDARC
zt-attachments:
  - "150"
title: "LLM.int8(): 8-bit Matrix Multiplication for Transformers at Scale"
authors:
  - Tim Dettmers
  - Mike Lewis
  - Younes Belkada
  - Luke Zettlemoyer
doi: 10.48550/arXiv.2208.07339
conference: NIPS2022
citekey: dettmers2022-llm
tags:
---
# LLM.int8(): 8-bit Matrix Multiplication for Transformers at Scale

**文章链接**: [Zotero](zotero://select/library/items/B68PDARC) [attachment](<file:///home/ilot/Zotero/storage/CCLIH73H/Dettmers%20%E7%AD%89%20-%202022%20-%20LLM.int8()%208-bit%20Matrix%20Multiplication%20for%20Transf.pdf>)
**网页链接**: [URL](http://arxiv.org/abs/2208.07339)
## Abstract

>[!abstract]
>本文为 Transforemt 的前馈层和注意力层设计了一种 INT 8 的矩阵乘法。
>主要使用了两种方法：
>为矩阵乘的每一对内积使用单独的尺度缩放因子，
>对一些数据异常大的特征向量进行全精度计算。

## Introduction

对矩阵乘法，只使用第一种方法，也就是分别为 $\boldsymbol{XW}$ 中 $\boldsymbol{X}$ 的每一行和 $\boldsymbol{W}$ 的每一列设计单独的因子，在参数量在 2.7B 以下时，可以保持很好的精度，但更多参数时，性能会快速下降。这主要是由于某些特征上的极端大的异常值造成的，对这些异常值的量化导致了性能的减退。

> To this end, we provide a new descriptive analysis which shows that large features with magnitudes up to 20x larger than in other dimensions first appear in about 25% of all transformer layers and then gradually spread to other layers as we scale transformers to 6 B parameters. At around 6.7 B parameters, a phase shift occurs, and all transformer layers and 75% of all sequence dimensions are affected by extreme magnitude features. These outliers are highly systematic: at the 6.7 B scale, 150,000 outliers occur per sequence, but they are concentrated in only 6 feature dimensions across the entire transformer. Setting these outlier feature dimensions to zero decreases top-1 attention softmax probability mass by more than 20% and degrades validation perplexity by 600-1000% despite them only making up about 0.1% of all input features. In contrast, removing the same amount of random features decreases the probability by a maximum of 0.3% and degrades perplexity by about 0.1%.


![[Pasted image 20240516153000.png]]


## 8 比特数据以及量化的形式

Absmax quant:
$$
\mathbf{X}_{i8}=\left\lfloor\frac{127\cdot\mathbf{X}_{f16}}{\max_{ij}(|\mathbf{X}_{{f16_{ij}}}|)}\right\rceil=\left\lfloor\frac{127}{\|\mathbf{X}_{f16}\|_{\infty}}\mathbf{X}_{f16}\right\rceil=\left\lfloor s_{{x_{f16}}}\mathbf{X}_{f16}\right\rceil,
$$

显然，从 $\boldsymbol{X}_{i8}$ 转化回去，除以尺度因子就行。
这种形式的量化，会导致 $[-127,127]$ 中的数不能被完全利用。比如如果所有数都是正的，那么负的半边就是空的。


Zeropoint quant:
使用 scale 和 zeropoint 进行转化。

$$
\begin{align*}
-127 &= scale \times \boldsymbol{X}_{min}+zeropoint\\\\
127&= scale \times \boldsymbol{X}_{max} + zeropoint
\end{align*}
$$
解得：
$$
\begin{align*}
scale &=  \frac{2\times 127}{(\boldsymbol{X}_{max}-\boldsymbol{X}_{min})}\\
zeropoint &= -\lfloor scale \times \boldsymbol{X}_{min}\rceil -127
\end{align*}
$$
$$
\begin{align*}
\boldsymbol{X}_{q}&=\lfloor scale \times \boldsymbol{X} +zeropoint\rceil \\
\boldsymbol{X}_{deq}&= \frac{\boldsymbol{X}_{q}-zeropoint}{scale}
\end{align*}
$$
在 LLM.int8 技术中，实际上这个 $zeropoint$ 似乎没啥用，还只是保存一个 $X_{i8}=\lfloor scale \boldsymbol{X}_{f16}\rceil$
从 $\boldsymbol{X}_{i8}$ 转化回去，只需要除以一个尺度因子就行了，和 $zeropoint$ 没啥关系。



假设要计算两个 16 位浮点数的乘法，$\boldsymbol{X}_{f16}\boldsymbol{W}_{f16}$，分别为 $\boldsymbol{X}_{f16}$ 的每一行，$\boldsymbol{W}_{f16}$ 的每一列单独进行量化（使用 Adsmax 和 zeropoint 都行），都计算出尺度因子，并计算出 $\boldsymbol{X}_{i8},\boldsymbol{W}_{i8}$，就有：
$$
\boldsymbol{X}_{f16}\boldsymbol{W}_{f16}=\boldsymbol{C}_{f16}
\approx \frac1{\mathbf{c}_{x_{f16}}\otimes\mathbf{c}_{w_{f16}}}\boldsymbol{X}_{i8}\boldsymbol{W}_{i8}
$$


![[Pasted image 20240516174531.png]]
## 对异常维度的分离
异常值虽然多，但他们集中在几个维度，因此，可以把原来的矩阵乘法分解开来，这些异常维度单独使用全精度的计算。最后再拼接到一起。
![[Pasted image 20240516164025.png]]
设 $\boldsymbol{X}_{f16}\in \mathbb{R}^{s\times h},\boldsymbol{W}_{f16} \in \mathbb{R}^{s\times o}$, $O=\{i|i\in\mathbb{Z},0\leq i\leq h\}$ 表示那些值很大的维度，那么数学化表达就是：
$$\mathbf{C}_{f16}\approx\sum_{h\in O}\mathbf{X}_{f16}^{h}\mathbf{W}_{f16}^{h}+\mathbf{S}_{f16}\cdot\sum_{h\not\in O}\mathbf{X}_{i8}^{h}\mathbf{W}_{i8}^{h}$$

## 如何确定异常维度 ：
![[Pasted image 20240516174700.png]]
假设一个 L 层的 transformer 模块，它的一个隐藏的状态 $\boldsymbol{X}_{l}\in \mathbb{R}^{s\times h}，l=0,1,...L$，$s$ 表示序列长度，$h$ 表示特征长度。如果某个维度 $h_i$，它对应的 $s$ 个数中，至少有数 $\geq 6$，并且在全部的 $L$ 个隐藏状态中，至少有 25%的层在这个维度上，都出现了这样的 $\geq 6$ 的异常值，并且在全部的 L 层上，至少有 6%的 $s$ 维度都有这样的异常值。

## 异常维度的影响
![[Pasted image 20240516174146.png]]
a 表明，随着困惑度的下降，异常值的中值大小猛然上升，这也是为什么之前普通的量化方法失败的原因。b 表明异常维度数量随着困惑度下降严格单调上升。