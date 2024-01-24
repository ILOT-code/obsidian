---
zotero-key: AMLDJ9JG
zt-attachments:
  - "11"
title: "Compressing Explicit Voxel Grid Representations: Fast NeRFs Become Also Small"
authors:
  - Chenxi Lola Deng
  - Enzo Tartaglione
conference: xxx
citekey: deng2023-compressing
tags: []
---
# Compressing Explicit Voxel Grid Representations: Fast NeRFs Become Also Small

**文章链接**: [Zotero](zotero://select/library/items/AMLDJ9JG) [attachment](<file:///home/ilot/Zotero/storage/Y8M3WHZP/Deng%20%E5%92%8C%20Tartaglione%20-%202023%20-%20Compressing%20Explicit%20Voxel%20Grid%20Representations%20F.pdf>)
**网页链接**: [URL](https://openaccess.thecvf.com/content/WACV2023/html/Deng_Compressing_Explicit_Voxel_Grid_Representations_Fast_NeRFs_Become_Also_Small_WACV_2023_paper.html)
## Abstract

>[!abstract]
>

## Comments



## Methods

本文提出的方法以迭代的方式，不断删除一些不那么重要的参数，因此，评判什么样的参数是重要的就很重要。作者回顾了该方向的研究发展历程，并最后提出了自己的方法。

首先，出现的最简单也具有广阔应用的减去参数的方法：

$$
\left.w_i=\left\{\begin{array}{ll}w_i&if|w_i|>\mathcal{Q}_{|\boldsymbol{w}|}(\gamma)\\0&otherwise,\end{array}\right.\right.
$$
其中：$\mathcal{Q}_{|\boldsymbol{w}|}(\gamma)$，$\gamma  \in [0,1]$,  表示以一阶范数来排序参数 $\boldsymbol{w}$, 并以 $\gamma$ 为为分位点取得的数。

这种方法的不足很明显，它仅仅以参数的大小作为评判的标准。实际上，有可能有些参数虽然值很小，却有可能对于损失函数 $\mathcal{L}$ 具有较大的梯度，直接移除他们对损失函数的优化会产生坏的影响。

综合来看，对于一个参数 $\boldsymbol{w}$ 而言，其值大小和其梯度大小都是说的通的评判其重要性的因素，因此，后来的方法中，对它们进行了折衷：
$$
\left.w_i=\left\{\begin{array}{ll}w_i&if|\mathcal{L}(w_i)|>\mathcal{Q}_{|\Delta\mathcal{L}(w)|}(\gamma)\\0&otherwise.\end{array}\right.\right.
$$
其中：
$$
\Delta\mathcal{L}(w_i)\approx\frac{\partial\mathcal{L}}{\partial w_i}w_i,
$$
是损失函数 $\mathcal{L}$ 关于 $w_i$ 泰勒展开的一阶项。
