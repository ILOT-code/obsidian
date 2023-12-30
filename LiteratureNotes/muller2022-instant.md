---
zotero-key: 98UUMDIL
zt-attachments:
  - "327"
title: Instant neural graphics primitives with a multiresolution hash encoding
authors:
  - Thomas Müller
  - Alex Evans
  - Christoph Schied
  - Alexander Keller
doi: 10.1145/3528223.3530127
conference: ACM Transactions on Graphics
citekey: muller2022-instant
tags:
---
# Instant neural graphics primitives with a multiresolution hash encoding

**文章链接**: [Zotero](zotero://select/library/items/98UUMDIL) 
**网页链接**: [URL](https://dl.acm.org/doi/10.1145/3528223.3530127)
## Abstract

>[!abstract]
>>Neural graphics primitives, parameterized by fully connected neural networks, can be costly to train and evaluate. We reduce this cost with a versatile <mark style="background: #FFF3A3A6;">new input encoding</mark> that permits the use of a smaller network without sacrificing quality,
>
>对于 INR 网络，提出了一种新的可学习的对输入进行编码的方式，使得可以使用很小的网络就能达到很高的重建精度。
>>  a small neural network is augmented by a multiresolution hash table of trainable feature vectors whose values are optimized through stochastic gradient descent.
>
>使用多个分辨率的特征向量表，以哈希的方式进行查询，特征向量可训练

## Introduction

INR 被广泛应用在计算机图形有关的问题中，通过输入坐标来产生该点的 RGB 值、density 等。这些方法的重要共同点是将神经网络输入映射到更高维空间的编码，这是从紧凑模型中提取高近似质量的关键。目前，最成功的编码方法是使用可训练的、对数据集定制的数据结构，这些方法需要试探、经验和修改，限制了对具体问题的应用，
实现对任务的适应性和效率的关键是哈希表的多分辨率层次结构：
- 粗分辨率下，没有碰撞，格点到特征表的映射是满射。细分辨率下，即使有碰撞，那些对重建图像更重要的点（surface 上的点）会主导特征表的更新，这是 hash 表和后面连的 INR 自动实现的。
- 哈希表查询是 $\mathcal O(1)$ 的复杂度，也避免了像树那种结构造成的一些运行上的效率低的问题。


## Background and related work

已有对输入进行编码的方法（频域编码）
$$
\begin{aligned}\mathrm{enc}(x)&=\left(\mathrm{~}\sin(2^0x),\sin(2^1x),\ldots,\sin(2^{L-1}x),\right.\cos(2^0x),\cos(2^1x),\ldots,\cos(2^{L-1}x)\left.\right).\end{aligned}
$$
另外是一些使用核技巧的方法。对于这些方法而言，编码过程是确定的。现在，可训练的编码方法达到了 SOTA，它模糊了经典数据结构核基于神经网络的方法。它们引入了额外的可训练参数在辅助的数据结构上（像树或 grid），通过寻找坐标 $\mathbf{x}$ 相对应的几个特征，并进行插值，得到真正进入 MLP 的输入。


这种方法将本来需要由网络来承担的训练负担转移了很多到特征的自动学习上。尽管为了得到更好的效果，引入的特征的数量要更多，由此带来大的内存占用负担，但实际上，训练时，不需要训练全部的特征参数。例如，对于二维的输入，只需要 4 个特征，对于三维的输入，只需要 8 个特征，除神经网络本身外，单次训练额外引入的训练参数非常少。

但是，传统的 dense grid 具有局限性：
1. 它为全部区域分配相同的重要性（不管是重要的可见表面、边缘还是不重要的区域），这造成了大量特征条目的浪费。随着尺寸的增长，特征条目的增长是 $\mathcal O(N^{3})$，但是重要的区域（可见表面等）的增长是 $\mathcal O(N^2)$。
2. 自然图像展示出的平滑性，暗示了需要多分辨率的引入。

## Multiresolution hash encoding

![[Pasted image 20231210114113.png]]

设置 $L$ 个分辨率等级，每个等级对应着将图像分割的细腻度，设置 $[N_\mathrm{min},N_\mathrm{max}]$，分别是最粗糙的等级和最细致的等级，$N_l$ 对应着将该图像的每一维度都分割成这么多段，自然就产生了 $N_l+1$ 的格点，全部的格点数就是 $(N_l+1)^3$。
以几何级数设定每一级的分辨率：
$$\begin{aligned}N_l&:=\left\lfloor N_{\min}\cdot b^l\right\rfloor,\\b&:=\exp\left(\frac{\ln N_{\max}-\ln N_{\min}}{L-1}\right).\end{aligned}$$

对于输入点：$\mathbf{x}\in\mathbb{R}^d$（经过归一化，在 $[0,1]$ 中），在分辨率等级 $l$ 中，它在一个对角线由 $\lfloor\mathbf{x}_l\rfloor,\lceil\mathbf{x}_l\rceil$ 构成的超正方体中，其中：
$$\lfloor\mathbf{x}_l\rfloor=\lfloor\mathbf{x}\cdot N_l\rfloor,\lceil\mathbf{x}_l\rceil:=\lceil\mathbf{x}\cdot N_l\rceil.$$
我们可以获得这个超正方体顶点的坐标，由它们的坐标进行哈希索引，索引到对应的特征，对它们进行线性插值，就得到了编码结果。其中，哈希函数是：
$$h(\mathbf{x})=\left(\bigoplus_{i=1}^dx_i\pi_i\right)\quad\mathrm{mod~}T,$$
每一个等级对应的哈希表，表项不应该过多，设定一个上线 $T$，对应粗糙等级而言，$(N_l+1)^3<T$，因此它们的映射是 1 对 1 的，而对于更高等级的而言，就会有冲突。每一个特征的长度是 $F$，将所有等级得到的输出进行合并，最终的输出向量长度是：$L\cdot F+E$ ，其中 $E$ 是另外附加的一些描述信息。

对于冲突而言，如果两个 $\mathbf{x}$ 在某个分辨等级下，同属于一个格子，那么自然它们的顶点相同，取相同的特征，这并不算冲突，只有那些不属于同一个格点的才算冲突。

当训练样本以这种方式碰撞时，它们的梯度会取平均。考虑到这些样本的最终重建的重要性很少是相等的。例如，辐射场可见表面上的点将对重建图像有很大贡献（具有高可见度和高密度，两者都会乘法性影响梯度的大小），导致其表条目发生较大变化，而空白空间中的点碰巧引用同一个条目的权重会小得多。因此，更重要样本的梯度主导了碰撞平均值，并且特征表条目自然会以反映较高权重点的需求的方式进行优化。

