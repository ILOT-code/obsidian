---
zotero-key: IW 2 FDBIC
zt-attachments:
  - "145"
title: "CLDMIC: LEARNING-BASED DISTRIBUTED MULTIVIEW IMAGE CODING"
authors: Xinjie Zhang
doi: 
conference: ICLR2023
citekey: 
tags:
---
# LDMIC: LEARNING-BASED DISTRIBUTED MULTIVIEW IMAGE CODING


> [!abstract]
> 在多视角视频压缩方面，现有压缩器多采用预测编码的模式，这需要联合所有相机同时进行编码。并且，这些方法往往对相机的排列布置具有严格的要求，这使得在具有随机重叠视场的分布式相机系统中部署这些方法变得具有挑战性。
> distributed source coding theory 指出，分布式编码与联合解码的方式可以达到很好的数据压缩效果，基于此，本文就设计了这样的编码架构，独立编码，并在解码端引入注意力机制来构建视角间的联系。


现有的各种 MIC 具有以下问题：
1. 编码时需要进行通信
2. 大多数主流方案都是基于视差相关性来开发的，这通常需要提前知道相机的内部和外部参数。
3. 现有的基于 DNN 的多视角编码器没有取得很好的效果。

Slepian-Wolf 理论指出：两个或多个相关源的单独编码和联合解码理论上可以达到与无损压缩下联合编解码方案相同的压缩率。


## Method
定义多视角图像集合 $\mathbf{x}_{\mathbb{K}}=\{x_1,x_{2},\dots x_{k}\}$

编码端和熵模型和单图像的模型没有什么区别，在解码端，设计了 JCT 模块来提取其他视角的信息。

![[Pasted image 20241211161143.png]]


### JCT 模块：

![[Pasted image 20241211162532.png]]

多视角融合：
$$
\tilde{\boldsymbol{f}}_k^{^{\prime}}=\frac{1}{K-1}\sum_{i\in\mathbb{K}\setminus\{k\}}\boldsymbol{f}_i^{^{\prime}},
$$
只采用了很简单的融合方式。实际上，融合方式应该和各个相机的相对位置有关。设计一些复杂点的融合方式应该可以获得更好的效果。

设 $\mathbf{f}'_{k}\in \mathbb{R}^{H\times W\times d}，\tilde{\mathbf{f}}'_{k}\in \mathbb{R}^{H\times W\times D}$，采用了一种快速计算交叉注意力的方法。设有 $h$ 个注意力头，$n=H\times W$
使用核为 1 的卷积和 reshape 操作，得到：
$$
\begin{align}
\mathbf{Q}_{k} &=Conv(\mathbf{f'_{k}}) \in \mathcal{R}^{n\times h\times d_{1}} \\
\mathbf{K}_{k}&=Conv(\tilde{\mathbf{f}}'_{k}) \in \mathcal{R}^{n\times h\times d_{1}} \\
\mathbf{V}_{k} &= Conv(\tilde{\mathbf{f}}'_{k}) \in \mathcal{R}^{n\times h\times d_{2}} \\

\boldsymbol{A}_{k,i} & =\sigma_{row}(\boldsymbol{Q}_{k,i})(\sigma_{col}(\boldsymbol{K}_{k,i})^\mathsf{T}\boldsymbol{V}_{k,i}),\forall i=1,\cdots,h \\
\boldsymbol{f}_{\mathbb{K}\setminus\{k\}\to k}^{^{\prime}} & =\mathrm{Conv}(\boldsymbol{A}_{k,1}\oplus\cdots\oplus\boldsymbol{A}_{k,h}),
\end{align}
$$

最后得到模块的输出：
$$\boldsymbol{f}_k^*=\boldsymbol{f}_k+F(\boldsymbol{f}_{\mathbb{K}\setminus\{k\}\to k}^{^{\prime}}\oplus\boldsymbol{f}_k^{^{\prime}}),$$
## Exp

