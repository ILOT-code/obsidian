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

假设我们有一个排列矩阵 $\mathbf{P}$，那么节点的特征矩阵就变成了 $\mathbf{P}\mathbf{X}$, 邻接矩阵变成了 $\mathbf{PXP^T}$ .
还有一条性质是 $\mathbf{PP^T}=\mathbf{I}$

**位置回归**

假设得到了一个由网络得出的预估的排列：$\mathbf{r}_A\in\mathbb{R}^n$
$$\mathbf{r}_A=f\left(\mathbf{X},\mathbf{A}\right)$$
可以通过如下运算得到绝对排列：$\mathbf{r}\in \mathbb{R}^n$
$$\mathbf{r}=\text{ sgn }\left(\mathbf{r}_A\mathbf{1}^\top-\mathbf{1}\mathbf{r}_A^\top\right)\mathbf{1}$$
$sgn$ 操作无法传播梯度，使用 $sigmod$ 进行松弛。

现在需要将 $\mathbf{r}$ 转化成一个排列矩阵 $\mathbf{P}$，由定义可知，排列矩阵 $\mathbf{P}$ 的位置 $(\mathbf{r}_i,i)$ 上的元素是 $1$，其它位置是 $0$，因此这种生成一定不可微，作者采取了一种松弛生成的手段：
$$
\begin{aligned}
\hat{\mathbf{P}}&=\exp\left\{-\tau\left[\left(\mathbf{m}\mathbf{1}^\top-\mathbf{1}\mathbf{r}^\top+n\right)\quad(\mathrm{mod~}n)\right]\right\}\\
\mathbf{b}&\in \mathbb{R}^{n}, \mathbf{m}_i=i
\end{aligned}
$$
这样的生成会使全部 $(\mathbf{r}_i,i)$ 上的元素是 $1$，其它位置是小于 $1$ 的正数。容易得到 $\begin{aligned}\lim_{\tau\to+\infty}\hat{\mathbf{P}}=\mathbf{P},\mathbf{P}\in\mathcal{P}.\end{aligned}$，

另外，需要证明排列无关性：
![[Pasted image 20240126170043.png]]

假设原始排列下，结点特征矩阵和邻接矩阵是 $\mathbf{X} ,\mathbf{A}$, 那么生成 $\mathbf{r}_A=f\left(\mathbf{X},\mathbf{A}\right)$，再生成 $\mathbf{r}=\text{ sgn }\left(\mathbf{r}_A\mathbf{1}^\top-\mathbf{1}\mathbf{r}_A^\top\right)\mathbf{1}$，最后生成近似的排列矩阵 $\hat{\mathbf{P}}=\exp\left\{-\tau\left[\left(\mathbf{m}\mathbf{1}^\top-\mathbf{1}\mathbf{r}^\top+n\right)\quad(\mathrm{mod~}n)\right]\right\}$, 那么最后得到 $\hat{\mathbf{X}}=\hat{\mathbf{P}\mathbf{X}},\textit{and }\hat{\mathbf{A}}=\hat{\mathbf{P}\mathbf{A}}\hat{\mathbf{P}}^\top$

上面这个定理的意识是，如果对于原来的结点进行重新的排序，那么最后生成的 $\hat{\mathbf{X}} and \hat{\mathbf{A}}$ 依旧是不变的，这就满足了引言部分提出的一个重要性质：排列无关性。

设我们以排列矩阵 $\mathbf{P}$ 对原始节点进行重新排列，那么结点特征矩阵就变成了 $\mathbf{PX}$，邻接矩阵变成了 $\mathbf{PAP^T}$, 那么：
$$
\begin{aligned}
f(\mathbf{PX},\mathbf{PAP^{T})}&= \mathbf{P}f(\mathbf{X},\mathbf{A})\\
&=\mathbf{Pr_A}\\
\text{继续生成绝对排序}\\
&\operatorname{sgn}\left(\mathbf{Pr}_A\mathbf{1}^\top-\mathbf{1r}_A^\top\mathbf{P}^\top\right)\mathbf{1} \\
&= \operatorname{sgn}\left(\mathbf{Pr}_A\mathbf{1}^\top\mathbf{P}^\top-\mathbf{P1r}_A^\top\mathbf{P}^\top\right)\mathbf{1}  \\
&= \mathbf{P}\text{sgn}\left(\mathbf{r}_A\mathbf{1}^\top-\mathbf{1}\mathbf{r}_A^\top\right)\mathbf{P}^\top\mathbf{1}  \\
&= \mathbf{P}\text{sgn}\left(\mathbf{r}_A\mathbf{1}^\top-\mathbf{1}\mathbf{r}_A^\top\right)\mathbf{1}  \\
&= \mathbf{Pr}\\
\text{再生成排列矩阵}\\
&\exp\left\{-\tau\left[\left(\mathbf{m}\mathbf{1}^\top-\mathbf{1}\mathbf{r}^\top\mathbf{P}^\top+n\right)\quad(\mathrm{mod~}n)\right]\right\} \\
&= \exp\left\{-\tau\left[\left(\mathbf{m}\mathbf{1}^\top\mathbf{P}^\top-1\mathbf{r}^\top\mathbf{P}^\top+n\right)\quad(\mathrm{mod~}n)\right]\right\}  \\
&= \exp\left\{-\tau\left[\left(\mathbf{m}\mathbf{1}^\top-\mathbf{1}\mathbf{r}^\top+n\right)\quad(\mathrm{mod~}n)\right]\right\}\mathbf{P}^\top   \\
&= \hat{\mathbf{P}}\mathbf{P}^\top  \\
\text{最后得到两个矩阵}\\
&\hat{\mathbf{X}^{1}}=\hat{\mathbf{P}}\mathbf{P}^T{(\mathbf{P}\mathbf{X})=\hat{\mathbf{P}}\mathbf{X}}= \hat{\mathbf{X}}\\
&\hat{\mathbf{A}^{1}} = \hat{\mathbf{P}}\mathbf{P}^T(\mathbf{P}\mathbf{A}\mathbf{P}^T)\mathbf{P}\hat{\mathbf{P}}^\top=\mathbf{\hat{P}}\mathbf{A}\hat{\mathbf{P}}^{T}=\hat{\mathbf{A}}\\
\text{得证}
\end{aligned}
$$

## Compressed Convolution Network
