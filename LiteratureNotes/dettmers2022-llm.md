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
conference: xxx
citekey: dettmers2022-llm
tags: []
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


## B 比特数据以及量化的形式

Absmax quant:
$$
\mathbf{X}_{i8}=\left\lfloor\frac{127\cdot\mathbf{X}_{f16}}{\max_{ij}(|\mathbf{X}_{{f16_{ij}}}|)}\right\rceil=\left\lfloor\frac{127}{\|\mathbf{X}_{f16}\|_{\infty}}\mathbf{X}_{f16}\right\rceil=\left\lfloor s_{{x_{f16}}}\mathbf{X}_{f16}\right\rceil,
$$

假设计算 $\boldsymbol{X}_{f16}\boldsymbol{W}_{f16}$，
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
实际上这个 $zeropoint$ 似乎没啥用，还只是保存一个 $X_{i8}=\lfloor scale \boldsymbol{X}_{f16}\rceil$

假设要计算两个 16 位浮点数的乘法，$\boldsymbol{X}_{f16}\boldsymbol{W}_{f16}$
