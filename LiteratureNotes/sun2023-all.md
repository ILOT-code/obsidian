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
conference: ICML2023
citekey: sun2023-all
tags:
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
1. 图卷积操作在有限的参数情况下表现较差。
2. GNN 无法直接进行分层次的特征学习，需要借助额外的结构。

而 CNN 天生能够解决这些问题。但 CNN 难以直接用在图卷积上，这需要一个正则化模块来将欧式卷积从规则的网格结构拓展到不规则的拓扑结构。

作者认为，好的正则化方法需要满足两点要求：
1. 正则化后的图需要为后面的卷积操作提供足够的信息，因此，正则化操作应该可微，和随后的卷积操作同时训练。
2. 排列无关性：CNN 对“像素”的排序是敏感的，但是对于图而言，重要的是拓扑结构而已。因此，对于拓扑结构相同但矩阵表现形式不同的图，经过正则化后，应当具有相同的表现形式，否则经过 CNN 卷积后会产生很不同的特征。

总体上看，作者的思路由两部分组成：
1. 正则化（排序生成）：作者将图的正则化问题看成一个对结点的排列问题（也就是将输入节点按照正确顺序排序）。
2. 对角线卷积。

下图展示了一个总体的框架，首先使用学习到的排列矩阵对点进行排序，然后进行对角卷积，同时非对角线上的特征使用别的层进行学习。

![[Pasted image 20240126154126.png]]


## Regularization on Graphs

**符号定义**

图： $\mathcal{G}\quad=\quad(\mathcal{V},\mathcal{E})$，$\mathcal{V}=\{v_1,v_2,\cdots,v_n\}$ 表示节点集，$\mathcal{E}$ 表示边集
每一个节点都有一个长度 $d$ 的特征向量，总体由： $\mathbf{X}\in\mathbb{R}^{n\times d}$ 表示
邻接矩阵： $\mathbf{A}\in\mathbb{R}^{n\times n}$，度矩阵： $\mathbf{D}\in\mathbb{R}^{n\times n}$，$\mathbf{D}_{i,i}=\sum_j\mathbf{A}_{i,j}.$
排列矩阵的集合：$\mathcal{P}$,   $\left|\mathcal{P}\right|=n!$，
$\mathbf{P}\in\mathcal{P},\mathbf{P}\in\mathbb{R}^{n\times n},\mathbf{P}\mathbf{1}=\mathbf{1}^\top\mathbf{P}=\mathbf{1},\mathbf{P}_{i, j}\in\{0,1\}$，$\mathbf{P_{i.j}=1}$ 意味着将原来的 $j$ 号节点移到 $i$.

假设我们有一个排列矩阵 $\mathbf{P}$，那么节点的特征矩阵就变成了 $\mathbf{P}\mathbf{X}$, 邻接矩阵变成了 $\mathbf{PAP^T}$ .
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
\mathbf{m}&\in \mathbb{R}^{n}, \mathbf{m}_i=i
\end{aligned}
$$
这样的生成会使全部 $(\mathbf{r}_i,i)$ 上的元素是 $1$，其它位置是小于 $1$ 的正数。容易得到 $\begin{aligned}\lim_{\tau\to+\infty}\hat{\mathbf{P}}=\mathbf{P},\mathbf{P}\in\mathcal{P}.\end{aligned}$，

另外，需要证明排列无关性：
![[Pasted image 20240126170043.png]]

假设原始排列下，结点特征矩阵和邻接矩阵是 $\mathbf{X} ,\mathbf{A}$, 那么生成 $\mathbf{r}_A=f\left(\mathbf{X},\mathbf{A}\right)$，再生成 $\mathbf{r}=\text{ sgn }\left(\mathbf{r}_A\mathbf{1}^\top-\mathbf{1}\mathbf{r}_A^\top\right)\mathbf{1}$，最后生成近似的排列矩阵 $\hat{\mathbf{P}}=\exp\left\{-\tau\left[\left(\mathbf{m}\mathbf{1}^\top-\mathbf{1}\mathbf{r}^\top+n\right)\quad(\mathrm{mod~}n)\right]\right\}$, 那么最后得到 $\hat{\mathbf{X}}=\hat{\mathbf{P}}\mathbf{X},\textit{and }\hat{\mathbf{A}}=\hat{\mathbf{P}}\mathbf{A}\hat{\mathbf{P}}^\top$

上面这个定理的意思是，如果对于原来的结点进行重新的排序，那么最后生成的 $\hat{\mathbf{X}} and \hat{\mathbf{A}}$ 依旧是不变的，这就满足了引言部分提出的一个重要性质：排列无关性。

设我们以排列矩阵 $\mathbf{P}$ 对原始节点进行重新排列，那么结点特征矩阵就变成了 $\mathbf{PX}$，邻接矩阵变成了 $\mathbf{PAP^T}$, 那么：
$$
\begin{aligned}
f(\mathbf{PX},\mathbf{PAP^{T})}&= \mathbf{P}f(\mathbf{X},\mathbf{A})\\
&=\mathbf{Pr_A}\\
\text{继续生成绝对排序:}\\
&\operatorname{sgn}\left(\mathbf{Pr}_A\mathbf{1}^\top-\mathbf{1r}_A^\top\mathbf{P}^\top\right)\mathbf{1} \\
&= \operatorname{sgn}\left(\mathbf{Pr}_A\mathbf{1}^\top\mathbf{P}^\top-\mathbf{P1r}_A^\top\mathbf{P}^\top\right)\mathbf{1}  \\
&= \mathbf{P}\text{sgn}\left(\mathbf{r}_A\mathbf{1}^\top-\mathbf{1}\mathbf{r}_A^\top\right)\mathbf{P}^\top\mathbf{1}  \\
&= \mathbf{P}\text{sgn}\left(\mathbf{r}_A\mathbf{1}^\top-\mathbf{1}\mathbf{r}_A^\top\right)\mathbf{1}  \\
&= \mathbf{Pr}\\
\text{再生成排列矩阵:}\\
&\exp\left\{-\tau\left[\left(\mathbf{m}\mathbf{1}^\top-\mathbf{1}\mathbf{r}^\top\mathbf{P}^\top+n\right)\quad(\mathrm{mod~}n)\right]\right\} \\
&= \exp\left\{-\tau\left[\left(\mathbf{m}\mathbf{1}^\top\mathbf{P}^\top-1\mathbf{r}^\top\mathbf{P}^\top+n\right)\quad(\mathrm{mod~}n)\right]\right\}  \\
&= \exp\left\{-\tau\left[\left(\mathbf{m}\mathbf{1}^\top-\mathbf{1}\mathbf{r}^\top+n\right)\quad(\mathrm{mod~}n)\right]\right\}\mathbf{P}^\top   \\
&= \hat{\mathbf{P}}\mathbf{P}^\top  \\
\text{最后得到两个矩阵:}\\
&\hat{\mathbf{X}^{1}}=\hat{\mathbf{P}}\mathbf{P}^T{(\mathbf{P}\mathbf{X})=\hat{\mathbf{P}}\mathbf{X}}= \hat{\mathbf{X}}\\
&\hat{\mathbf{A}^{1}} = \hat{\mathbf{P}}\mathbf{P}^T(\mathbf{P}\mathbf{A}\mathbf{P}^T)\mathbf{P}\hat{\mathbf{P}}^\top=\mathbf{\hat{P}}\mathbf{A}\hat{\mathbf{P}}^{T}=\hat{\mathbf{A}}\\
\text{得证}
\end{aligned}
$$

## Compressed Convolution Network

**Permutation Generation**

用下面的式子来生成 $\mathbf{r_A}$, 其中 $\tilde{A}$ 是拉普拉斯操作，指数 $t$ 调整其平滑程度。
$$
\begin{align*}
\mathbf{r}_A=\tilde{\mathbf{A}}^{t} MLP(\mathbf{X})\\\\
\tilde{\mathbf{A}}=\mathbf{D}^{-\frac12}\mathbf{A}\mathbf{D}^{-\frac12}
\end{align*}
$$

MLP 操作可以看成是矩阵乘法加上激活函数，容易得到 $MLP(\mathbf{PX})=\mathbf{P}MLP(\mathbf{X})$, 进一步地，根据 $\mathbf{PP^T}=\mathbf{I}$ ，可以证明此操作满足 $f(\mathbf{X},\mathbf{A})=f(\mathbf{PX},\mathbf{PAP^T})$.

然后根据前文的方法，得到排列矩阵和 $\hat{\mathbf{X}},\hat{\mathbf{A}}$ .

**Diagonal Convolution**

排列操作可以看成是一种映射，将拓扑结构映射到欧几里得空间，排列之后，无论边的连接情况如何，相邻点都可以是“邻居”，具有类似的结构。接着将 CNN 的操作拓展过来。

矩阵 $\hat{\mathbf{A}}$ 可以看成是图的拓扑特征矩阵，使用对角卷积来进行操作：
$$\mathcal{K}_i^{str}\left(\hat{\mathbf{A}},k\right)=\sum_{p=0}^{k-1}\sum_{q=0}^{k-1}\mathbf{w}_{p,q}\hat{\mathbf{A}}_{i+p,i+q},$$

进一步地，为了引入节点特征，可以拓展上面的式子：
$$\mathcal{K}_i\left(\hat{\mathbf{A}},\hat{\mathbf{X}},k\right)=\sum_{p=0}^{k-1}\left(\sum_{q=0}^{k-1}\mathbf{w}_{p,q}\hat{\mathbf{A}}_{i+p,i+q}+\sum_{t=0}^{d-1}\mathbf{v}_{p,t}\hat{\mathbf{X}}_{i+p,t}\right)$$

对角卷积也能具有步长，上面的这个式子能够保证两部分生成的矩阵有相同的行数。最后，可以概括写成：
$$\begin{aligned}\mathbf{S}&=\mathcal{K}\left(\hat{\mathbf{A}},\hat{\mathbf{X}},k,s\right),\\\mathbf{S}_j&=\mathcal{K}_i\left(\hat{\mathbf{A}},\hat{\mathbf{X}},k\right),\mathrm{~}i=sj.\end{aligned}$$

在一次卷积过程后，得到了一个向量，和 CNN 一样，可以引入多个卷积核，来生成新的矩阵。

**Compressed Convolution Layer**

本文提出的网络是深度的，具有结构层次。$\hat{\mathbf{A}}$ 矩阵包含了图的拓扑特征 (边的特征)，$\hat{\mathbf{X}}$ 包含了节点特征，作者提出的多层架构就是在不停地更新这两张矩阵。

令 $\mathbf{H}^{(0)}=\hat{\mathbf{X}},\mathbf{E}^{(0)}=\hat{\mathbf{A}}$, 对于前者，直接使用前面的对角卷积更新，而对于后者，我们注重其表示的结构特征。对于 $\mathbf{E}$ 而言，其对角线上的元素是不重要的，而那些非对角线上的元素才真正表达了它的结构特征，这些元素才是需要关注的。因此，作者对它们采用了别的更新策略。
$$\begin{aligned}\mathbf{H}^{(l)}&=\sigma\left(\mathcal{K}\left(\mathbf{E}^{(l-1)},\mathbf{H}^{(l-1)},k^{(l)},s^{(l)}\right)\right),\\\\\mathbf{E}^{(l)}&=\begin{cases}\text{Tri}\left(\mathbf{E}^{(l-1)},k^{(l)}\right),&\mathrm{if~}s^{(l)}=1;\\\text{MaxPool}\left(\mathbf{E}^{(l-1)},k^{(l)},s^{(l)}\right),&\mathrm{otherwise}.&\end{cases}\end{aligned}$$
其中 $\text{Tri}\left(\mathbf{E}^{(l-1)},k^{(l)}\right)$ 表示将主对角线上面和下面的 $k^{(l)}$ 条对角线排除后，将剩下的两个三角形矩阵拼接在一起得到的矩阵。

更新后，$\mathbf{E}^{(l)}\in\mathbb{R}^{n^{(l)}\times n^{(l)}}，\mathbf{H}^{(l)}\in\mathbb{R}^{n^{(l)}\times c^{(l)}}$，其中 $c^{(k)}$ 表示卷积核的数量。

**Network Architecture**


下图分别展示了对于两种任务的结构。它们很像，都是 $L_1$ 层单位步长的卷积，和 $L_2$ 层步长和核的大小一样的卷积，最后是一层单位步长的卷积，
![[Pasted image 20240126192949.png]]

其中，作者还加入了虚线，这表示加上一个平均池化，前面的更新式子可以重新表示为：
$$\begin{aligned}
\mathbf{H}^{(l)}=& \begin{aligned}\sigma\left(\mathcal{K}\left(\mathbf{E}^{(l-1)},\mathbf{H}^{(l-1)},k^{(l)},s^{(l)}\right)\right)\end{aligned}  \\
&+\text{ AvgPool }\left(\mathbf{H}^{(l-1)},k^{(l)},s^{(l)}\right).
\end{aligned}$$


## Experiments

在图分类任务的结果：除了 MUTAG 数据集，其它数据集上均表现最好
![[Pasted image 20240127130752.png]]

在节点分类任务的结果：总体上表现很好
![[Pasted image 20240127130706.png]]