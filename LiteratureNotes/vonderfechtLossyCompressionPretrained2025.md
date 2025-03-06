---
zotero-key: RFVNJ 6 JH
zt-attachments:
  - "306"
title: LOSSY COMPRESSION WITH  PRETRAINED DIFFUSION MODELS
authors:
  - Jeremy Vonderfecht
conference: ICLR 2025
citekey: 
tags:
---
# reverse channel coding
如果用概率分布 $p$ 去编码真实分布 $q$ 中的数据，需要的编码长度是它们的交叉熵。这样的编码是我们熟知的熵编码，他可以用 ans, ac 等技术来实现。这种熵编码是“正向的”，也就是先有数据，再有一个对数据分布的估计，然后去进行编解码操作。

而 reverse channel coding 的流程和上面不同，它是先有一个分布，在进行采用，然后进行编码。
它聚焦于如下问题：
$$
\mathbf{Z}=\mathbf{X}+\mathbf{U}\quad\mathrm{~where~}\quad\mathbf{U}\sim\mathcal{N}(0,\mathbf{I}),
$$
（噪声并不一定需要是这个白噪声）。现在，需要从 $Z$ 中进行采样，然后发送它。采样过程和随机数生成器有关，发送端和接收端只要有相同的随机数生成器，就能更容易的编码它。

Bennett 和 Shor（2002）证明了可以用不多于 $I[X, Z]$ 个比特来传递一个 $Z$ 的实例。Li and El Gamal (2018) 设计了一个具体的算法，并证明这个算法编码的上界是：
$$
I[\mathbf{X},\mathbf{Z}_{t}]+\log(I[\mathbf{X},\mathbf{Z}_{t}]+1)+5
$$
有的时候，解码端与编码端无法得知真正的分布 $Z$，但是它们都可以访问到一个概率估计 $p$，此时，编码一个实例 $z$ 的上界就会变成：
$$
C+\log(C+1)+5\quad\mathrm{where}\quad C=\mathbb{E}_\mathbf{X}[D_\mathrm{KL}[q(\mathbf{z}\mid\mathbf{X})\parallel p(\mathbf{z})]]
$$

这是该文献的一篇参考文献中，提到的 reverse channel coding 的大体思路：
![[Pasted image 20250305192513.png]]



## Intro
本文试图利用 reverse channel coding 技术，以及一些大的预训练的 SD 模型，在不进行任何训练以及条件融合的情况下，实现图像的编解码。

## 总体流程与方法

首先是最基本的编解码端如何进行通信的算法：
![[Pasted image 20250305193027.png]]

发送端首先从分布 $q(x_{T}|x_{0})$ 中采样得到 $x_{T}$，接收端接收它。$p(x_{T})$ 几乎是正太分布，发送端和接收端都可以获取到。这一项的 KL 熵几乎是 0.
中间过程中，$p_{\theta}(x_{t}|x_{t+1})$ 是模型给出的概率分布，二者都可以获取到。

实际上，并不需要完全发送这么多数据，仅仅需要还原到某个 $x_{t}$，解码端在进行去噪即可，就能实现可变的码率。
$$
\sum_{i=T}^tD_{KL}(q(\mathbf{x}_{i-1}|\mathbf{x}_i)||p_\theta(\mathbf{x}_{i-1}|\mathbf{x}_i))=-\mathcal{L}(\mathbf{x}_t)
$$

## 编码算法的改进
现有的 reverse channel coding 算法有两个不足。它的算法时间复杂度和 KL 熵大小成指数倍的关系，当 KL 熵很大的时候，会出现问题。其次，在 KL 熵很小的时候，它 bit 位的利用是有很多冗余的，甚至编码一个不那么小的 kl 熵需要的 bit 更少。这两种情况在 ddpm模型中普遍存在，$t$ 很大时，KL 熵很小，$t$ 很小时，KL 熵会变大。

为克服 KL 熵很小的问题，那就进行相邻步的计算了。把步长拉的大一点，使 KL 熵变大。
当 KL 很大时，直接进行维度分解：$p=p_{1}p_{2}\dots p_{n},q=q_{1}q_{2}\dots q_{n}$。这是因为这两种分布都是方差为对角矩阵的高斯分布。由 KL 的计算公式，近似有：
$$
D_{\mathrm{KL}}(q_i\parallel p_i)\approx\frac1nD_{\mathrm{KL}}(q\parallel p)\quad\forall i\in[1,n]
$$
## 贪心策略
根据上面的算法给出的采样策略，有到底一个非常好的性质：从统计的意义上看，我们根本无法分辨 $x_{t}$ 到底是从 $q(x_{t}|x_{0})$ 中采样得到的，还是从 $x_{T}$ 经过一步一步的 $q(x_{t-1}|x_{t},x_{0})$ 中采样得到的，它们二者的分布完全一样。
这意味着这个问题是无后效性的，可以用动态规划去解：
$$
C_\mathrm{opt}[i,j]=min_kC[i,k]_\mathrm{opt}+C[k,j]_\mathrm{opt}
$$
算法流程如下：
需要 $|X|T$ 次完整的传播就能计算完成，最后使用最短路算法去解。
![[Pasted image 20250305200301.png]]

## 去噪过程
在得到 $x_{final}$ 后，让模型去噪，就能还原 $x_{0}$。作者实验了一系列的值：$final\in[900,800,\ldots,100,90,\ldots10]$
得到一系列不同 R-D 的结果。

不同的 SD 模型有不同的去噪过程。