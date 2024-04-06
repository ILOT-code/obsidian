---
zotero-key: DFAT6T8W
zt-attachments:
  - "141"
title: "DSelect-k: Differentiable Selection in the Mixture of Experts with Applications to Multi-Task Learning"
authors:
  - Hussein Hazimeh
  - Zhe Zhao
  - Aakanksha Chowdhery
  - Maheswaran Sathiamoorthy
  - Yihua Chen
  - Rahul Mazumder
  - Lichan Hong
  - Ed H. Chi
doi: 10.48550/arXiv.2106.03760
conference: NIPS2021
citekey: hazimeh2021-dselectk
tags:
---
# DSelect-k: Differentiable Selection in the Mixture of Experts with Applications to Multi-Task Learning

**文章链接**: [Zotero](zotero://select/library/items/DFAT6T8W) [attachment](<file:///home/ilot/Zotero/storage/I5NQ7X3N/Hazimeh%20%E7%AD%89%20-%202021%20-%20DSelect-k%20Differentiable%20Selection%20in%20the%20Mixture.pdf>)
**网页链接**: [URL](http://arxiv.org/abs/2106.03760)
## Abstract

>[!abstract]
>MoE架构在提升网络的容量和在多任务学习中改善参数共享方面展现了巨大的潜力。但如今，最先进的MoE结构（如Top-K gating）是不光滑的的，这影响了收敛的性能，参数会出现振荡的现象。
>本文提出了DSelect-k, 一种可微的、稀疏的门控结构，能够使用一阶的优化方法来进行优化，在多个数据集合上表现的比Top-K更好。

## Comments


## MoE中的门结构--历史
MoE结构具有门控结构和一系列专家网络，网络的输出由它们共同组成：
$$
\sum\limits_{i=1}^{i=n}f_{i}(x)g(x)_i
$$
门控结构 $g(x)_i$ 有一些经典的做法：
1. Softmax Gate: 采用 Softmax 函数生成一维概率向量 : $\sigma(Ax+b)$
2. Top-K Gate：是 Softmax 结构的一种稀疏化变体，$\sigma(KeepTopK(Ax+b))$, $KeepTopK(t)$ 把向量 $t$ 的前 K 大元素保持不变，其它的置 0，这种方法有助于训练很多个专家，因为在前向和反向过程中它仅仅选取了部分的专家。

本文提出的 DSelect 和 Tok-k 类似，根据用户选取的 k 来稀疏的选取一些专家，并且此结构是光滑的，参数量和计算量也会更少。

![[Pasted image 20240406151600.png]]



## Differentiable and Sparse Gating

本文需要解决的问题是：在 $n$ 个专家网络中，如果选取不超过 $k$ 个网络，来使得损失函数最小：
$$\begin{aligned}\min_{f_1,...f_n,w}&\frac1N\sum_{(x,y)\in\mathcal{D}}\ell\Big(y,\sum_{i=1}^nf_i(x)w_i\Big)\\\text{s.t.}&\|w\|_0\leq k\\&\sum_{i=1}^nw_i=1, w\geq0.\end{aligned}$$