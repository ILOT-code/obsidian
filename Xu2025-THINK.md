---
zotero-key: 4XIGHJ9W
zt-attachments:
  - "75"
title: "THINK: THINNER KEY CACHE BY QUERY-DRIVEN  PRUNING"
authors:
  - Yuhui Xu
doi: 10.48550/arXiv.2211.06689
conference: ICLR2025
citekey: 
tags:
---
# THINK: THINNER KEY CACHE BY QUERY-DRIVEN  PRUNING
本文也致力于kv-cache的缩减，和之前方法不同的是，它是从channel的维度进行缩减的，因此，他和其他在时间维度上进行缩减的方法正交。

**为什么对channel进行削减**
在不同的层上，key的通道间，数据的分布都极不平衡，少数几个通道占据了很大的值，这意味着这些通道对attention 的计算影响重大。
![[Pasted image 20250326151951.png]]

对attention矩阵进行SVD分解，仅有少数奇异值占据了大部分能量，意味着attention是低秩的，低秩近似是有效的。
![[Pasted image 20250326152356.png]]

## Method

对Key,作者首先使用了基于数值幅度的削减方法，使用1范数和2范数作为测度，只保留key中前$1-\lambda$部分的channel。结果如下：

![[Pasted image 20250326152832.png]]
在这种方式下，$\lambda$从0.3到0.4会带来显著的性能下降。效果并不理想。
单独对key进行考虑是不足的，attention的计算需要综合考虑query和key，因此，这个问题被重写为一个优化问题,$\mathbf{Q}_i,\mathbf{K}_i,\mathbf{V}_i\:\in\:\mathbb{R}^{S\times D}, \mathbf{S}\in\{0,1\}^{D\times D}$
$$
\begin{aligned}\min_{\mathbf{S}}&\left\|\mathbf{Q}_i\mathbf{K}_i^T-\mathbf{Q}_i\mathbf{S}\mathbf{K}_i^T\right\|_F\\\text{subject to}&\operatorname{trace}(\mathbf{S})=\lfloor(1-\lambda)D\rfloor\\&\mathbf{S}=\operatorname{diag}(s_1,s_2,\ldots,s_D),\text{ where }s_j\in\{0,1\}\end{aligned}
$$
它是NP的，采用贪心算法：
$$
\mathrm{Score}_i[j]\:=\:\left\|\mathbf{Q}_i[:,j]\mathbf{K}_i[:,j]^T\right\|_F,\quad I_i\:=\:\mathbf{Top}_\lambda(\mathrm{Score}_i,\lambda)
$$
对query和key同时进行缩减。

当然，在推理的过程中，$\mathbf{Q}$的形状是$\mathbb{R}^{1\times D}$

对于最近的一些 token生成的key,不对它们进行维度上的削减，而是采用全维度计算，然后和前面的部分cat一下。
![[Pasted image 20250326154358.png]]