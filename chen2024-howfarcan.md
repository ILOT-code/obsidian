---
zotero-key: IW 2 FDBIC
zt-attachments:
  - "145"
title: How Far Can We Compress Instant-NGP-Based NeRF?
authors: Yihang Chen
doi: 10.48550/arXiv. 2402.097
conference: CVPR024
citekey: chenHowFarCan2024
tags:
---

# How Far Can We Compress Instant-NGP-Based NeRF?

---
## Abstract      
![[Pasted image 20241111154144.png|500]]
本文是对 Instant-NGP 的压缩。构建了一种上下文概率模型来对 grid 进行压缩。

---
## Preliminaries

#### NeRF
![[Pasted image 20241111154900.png]]

### Instant-NGP

![[Pasted image 20241111154955.png]]
![[Pasted image 20241111155024.png]]


### BiRF
![[Pasted image 20241111155353.png]]
![[Pasted image 20241111155432.png]]

### Compress Embeddings with Context Model
本文的方法建立在 BiRF 之上，因此，grid 中的每一个值，对应的概率只有 $p(\theta=+1)$ 和 $1-p(\theta=+1)$，概率模型也只预测符号+1 的概率。在得到该概率后，损失熵就可以写为：
![[Pasted image 20241111160152.png]]

首先，熵模型本身不需要很复杂，因为熵模型本身就占有很大的参数量。在实际实现中，对于 3D 的 grid, 仅仅采用了 3 层 mlp，对于 2 D 的 grid, 采用了 1 层的 mlp.

上下文该如何选取？作者提出了 level 级别和 Dimension 级别的上下文。对于前者，使用低 level 的 grid 作为上下文信息，对于后者，采用 3Dgrid 作为 2Dgrid 的上下文信息。

### Level-Wise Context Models
![[Pasted image 20241111160731.png]]

如果当前在解码 level 4 的网格，那么它只会参考前 $L_{c}$ 个 level 网格的信息，$L_{c}$ 是常数。
其次，除参考之前的网格外，还会参考一个标量 $f_{G}$，它是统计前一个 level 中，+1 符号的占比。

假设当前 level 有 $K$ 个 feature。feature 的维度是 $F$。对于第 $k$ 个条目，找到对应该条目的那些顶点。首先假设该顶点只有一个。那么就使用该顶点在当前网格的相对位置，去前面 $L_{c}$ 个 level 中找到对应的位置，并通过插值得到上下文。这些上下文被 $C_{p}$ 处理后得到一个长度是 $F$ 的向量，表示了各个位置上的 $p(\theta)=+1$

但是，也有可能这样的顶点不止一个，这是由于哈希碰撞造成的。这时候，就可以使用 Occupancy Grid 来帮助做出判断。各个顶点的重要性是不一致的，那些处于空的顶点，一点用都没有。


![[Pasted image 20241111162319.png]]
![[Pasted image 20241111162339.png]]

如果某个