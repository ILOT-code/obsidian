---
zotero-key: GHS7ZMTS
zt-attachments:
  - "403"
title: "HAC: Hash-grid Assisted Context for 3D Gaussian Splatting Compression"
authors:
  - Yihang Chen
  - Qianyi Wu
  - Weiyao Lin
  - Mehrtash Harandi
  - Jianfei Cai
doi: 10.48550/arXiv.2403.14530
conference: ECCV2024
citekey: chenHACHashgridAssisted2024
tags:
---
# HAC: Hash-grid Assisted Context for 3D Gaussian Splatting Compression

**文章链接**: [Zotero](zotero://select/library/items/GHS7ZMTS) [attachment](<file:///home/ilot/Documents/Zotero/storage/FPYYAFK8/Chen%20%E7%AD%89%20-%202024%20-%20HAC%20Hash-grid%20Assisted%20Context%20for%203D%20Gaussian%20Splatting%20Compression.pdf>)
**网页链接**: [URL](http://arxiv.org/abs/2403.14530)
## Abstract

>[!abstract]
>本文的工作建立在 [[luScaffoldGSStructured3D2023|(Tao Lu, 2023)]] 之上，旨在提出一种压缩 a 方法，把各个锚点及其属性进行压缩的方法，训练完毕后，把它们压缩存储和传输，在需要进行 a 渲染的时候，把它们解码出来进行前向的计算。
>总体而言，压缩思路基于这样一种构想：空间上相近的锚点应该具有相似的属性，或者说属性在空间上是连续的。这种想法类似于图像或视频压缩，不过后者具有更好的结构性质。为了利用这种性质，作者并没有利用自回归那样的思路来显示的 构建这种关联，而是建立了一个 hashtable, 根据锚点的坐标去其中索引出特征，这样来隐式的构建锚点间的关联。
>作者声称，相对于 3DGS, 减小了 75 倍的存储空间。




![[Pasted image 20241017132853.png]]
利用锚点坐标去哈希 grid 中索引特征 $f^{h}$，该特征被用来对锚点的属性 $\mathcal{A}$ 进行概率建模，也被用来进行自适应量化。

我感觉作者一开始只是想用 $f^{h}$ 去替代 $f^{a}$，但发现结果差了那么一点。虽然结果差了一点，但确实说明了他的假设有一定道理，于是他干脆把 $f^{h}$ 看成 context 来进行概率建模，并二值化哈希 grid, 减少额外信息的存储空间。

![[Pasted image 20241017133821.png]]


**自适应量化**
$\mathcal{A}$ 中的一些属性，比如 $l$ 是小数，没法用普通的 round 量化，于是采用自适应量化的方式：
![[Pasted image 20241017134339.png]]
量化的 bin 被限制在 $[0,2Q_{0}]$ 之间。不同的属性有不同的 $Q_{0}$

**高斯概率模型**
这些属性的分布，看上去很像高斯分布，于是使用高斯分布建模。
![[Pasted image 20241017134843.png]]
**哈希表压缩**
哈希表被量化到 $\{-1,+1\}$，使用 STE 来进行梯度传递。存储哈希表的额外损失：
$$
L_{hash}=M_{+}\times (-\log(h_{f}))+M_{-}\times (-\log(1-h_{f}))
$$
**对 offset 的自适应 mask**
$\mathcal{O}$ 在 0 附近具有较大的概率，说明很多高斯点是不必要的，有必要剔除对这些高斯点，采用了Compact 3d gaussian representation for radiance field. cvpr 24 中的策略：

每一个高斯点，都具有一个参数 $m_{n}\in \mathbb{R}$, $\sigma (m_{n})$ 表达了其重要程度。根据 h 重要程度同时对高斯点的不透明度和尺寸进行 mask:
![[Pasted image 20241017142528.png]]
使用 STE 来使这个二值化过程可微。
再向损失函数中填一项，来鼓励减去更多的高斯点：
![[Pasted image 20241017142736.png]]

**LOSS**
![[Pasted image 20241017142935.png]]

**实验**
![[Pasted image 20241017143055.png]]
![[Pasted image 20241017143110.png]]