---
zotero-key: DLRXUZCF
zt-attachments:
  - "14"
title: "All in a Row: Compressed Convolution Networks for Graphs"
authors:
  - Junshu Sun
  - Shuhui Wang
  - Xinzhe Han
  - Zhe Xue
  - Qingming Huang
conference: xxx
citekey: sun2023-all
tags: []
---
# All in a Row: Compressed Convolution Networks for Graphs

**文章链接**: [Zotero](zotero://select/library/items/DLRXUZCF) [attachment](<file:///home/ilot/Zotero/storage/BHGPL3GX/Sun%20%E7%AD%89%20-%202023%20-%20All%20in%20a%20Row%20Compressed%20Convolution%20Networks%20for%20.pdf>)
**网页链接**: [URL](https://proceedings.mlr.press/v202/sun23k.html)
## Abstract

>[!abstract]
>> existing graph convolution methods generally fail to learn diverse convolution operators under limited parameter scales and depend on additional treatments of multi-scale feature extraction. The challenges of generalizing Euclidean convolution to graphs arise from the irregular structure of graphs.
>
>由于图的不规则结构，将欧几里得的卷积推广到图卷积很困难，图卷积往往依赖一些特征提取的额外处理。
>
>本文提出了一种 regularization 方法，试图建立连接欧几里得卷积和图卷积的桥梁，继承欧几里得卷积的局部特征学习和全局参数共享的优点。并在此基础上，进一步提出了 CoCN (Compressed Convolution Network)，它是一种具有层次结构的 GNN 模型，能够进行端到端的训练。

## Introduction

尽管 GNN 在图表示的任务上无处不在，它面临如下几个问题：
1. 基于多项式的定制的图卷积操作，在有限的参数情况下表现较差。
2. GNN 无法直接进行分层表示学习，需要借助额外的结构。

而 CNN 天生能够解决这些问题。但 CNN 难以直接用在图卷积上，这需要一个正则化模块来讲欧式卷积从规则的网格结构拓展到不规则的拓扑结构。

作者认为，好的正则化方法需要满足两点要求：
1. 正则化后的图需要为后面的卷积操作提供足够的信息，因此，正则化操作和随后的卷积操作应该要同时训练，不能分开。
2. 排列无关性：CNN 操作对像素的顺序是敏感的，但对于图卷积而言，它仅仅是一种拓扑的结构而已，不同的节点排序应该产生相同的节点特征表达。

总体上看，作者的思路由两部分组成：
1. 正则化（排序生成）：作者将图的正则化问题看成一个对结点的排列问题（也就是将输入节点按照正确顺序排序）。
2. 对角线卷积。

下图展示了一个总体的框架，首先使用学习到的排列矩阵对点进行排序，然后进行对角卷积，同时非对角线上的特征使用别的层进行学习。

![[Pasted image 20240126154126.png]]


## Regularization on Graphs

**符号定义**

图： $\mathcal{G}\quad=\quad(\mathcal{V},\mathcal{E})$，$\mathcal{V}=\{v_1,v_2,\cdots,v_n\}$ 表示节点集，$\mathcal{E}$ 表示边集
每一个节点都由一个长度 $d$ 的特征向量，总体由： $\mathbf{X}\in\mathbb{R}^{n\times d}$ 表示
邻接矩阵： $\mathbf{A}\in\mathbb{R}^{n\times n}$，对角度矩阵： $\mathbf{D}\in\mathbb{R}^{n\times n}$，$\mathbf{D}_{i,i}=\sum_j\mathbf{A}_{i,j}.$
排列矩阵的集合：$\mathcal{P}$,   $\left|\mathcal{P}\right|=n!$，
$\mathbf{P}\in\mathcal{P},\mathbf{P}\in\mathbb{R}^{n\times n},\mathbf{P}\mathbf{1}=\mathbf{1}^\top\mathbf{P}=\mathbf{1},\mathbf{P}_{i, j}\in\{0,1\}$，$\mathbf{P_{i.j}=1}$ 意味着将原来的 $j$ 号节点移到 $i$.


**位置回归**

假设得到了一个由网络得出的预估的排列：$\mathbf{r}_A\in\mathbb{R}^n$
$$\mathbf{r}_A=f\left(\mathbf{X},\mathbf{A}\right)$$
可以通过如下运算得到绝对排列：$\mathbf{r}\in \mathbb{R}^n$
$$\mathbf{r}=\text{ sgn }\left(\mathbf{r}_A\mathbf{1}^\top-\mathbf{1}\mathbf{r}_A^\top\right)\mathbf{1}$$
需要将 $\mathbf{r}$ 转化成一个排列矩阵 $\mathbf{P}$，另外上面这种操作无法传播梯度，需要进行松弛。

由定义可知，排列矩阵 $\mathbf{P}$ 的位置 $(\mathbf{r}_i,i)$ 上的元素是 $1$，其它位置是 $0$.