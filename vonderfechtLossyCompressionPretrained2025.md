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

而 reverse channel coding 的流程和上面不同，它聚焦于如下问题：
$$
\mathbf{Z}=\mathbf{X}+\mathbf{U}\quad\mathrm{~where~}\quad\mathbf{U}\sim\mathcal{N}(0,\mathbf{I}),
$$
（噪声并不一定需要是这个白噪声）。现在，需要从 $Z$ 中进行采样，然后发送它。采样过程和随机数生成器有关，发送端和接收端只要有相同的随机数生成器，就能更容易的编码它。

Bennett 和 Shor（2002）证明了可以用不多于 $I[X, Z]$ 个比特来传递一个 $Z$ 的实例。Li and El Gamal (2018) 设计了一个具体的算法，并证明这个算法编码的上界是：
$$
I[\mathbf{X},\mathbf{Z}_{t}]+\log(I[\mathbf{X},\mathbf{Z}_{t}]+1)+5
$$
有的时候，解码端与编码端无法得知真正的分布 $Z$，但是它们都可以访问到一个概率估计 $p_{t}$，此时，编码一个实例 $z_{t}$ 的上界就会变成：
$$
C_t+\log(C_t+1)+5\quad\mathrm{where}\quad C_t=\mathbb{E}_\mathbf{X}[D_\mathrm{KL}[q(\mathbf{z}_t\mid\mathbf{X})\parallel p_t(\mathbf{z}_t)]]
$$
