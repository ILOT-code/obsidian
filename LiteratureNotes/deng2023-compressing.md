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
[]()# Compressing Explicit Voxel Grid Representations: Fast NeRFs Become Also Small

**文章链接**: [Zotero](zotero://select/library/items/AMLDJ9JG) [attachment](<file:///home/ilot/Zotero/storage/Y8M3WHZP/Deng%20%E5%92%8C%20Tartaglione%20-%202023%20-%20Compressing%20Explicit%20Voxel%20Grid%20Representations%20F.pdf>)
**网页链接**: [URL](https://openaccess.thecvf.com/content/WACV2023/html/Deng_Compressing_Explicit_Voxel_Grid_Representations_Fast_NeRFs_Become_Also_Small_WACV_2023_paper.html)
## Abstract

>[!abstract]
>

## Methods

### Importance

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
是损失函数 $\mathcal{L}$ 关于 $w_i$ 泰勒展开的一阶项。这样就能综合考虑两者的影响。

在一个网络中，参数的性质往往随着层的不同而不同。但是，上面的方法并未考虑到参数的层的影响，将它们放在一起来考虑。可以将度量的测度根据层来进行归一化：
$$\begin{gathered}
\begin{aligned}\Delta\hat{\mathcal{L}}(w_i)&=\frac{\frac{\partial\mathcal{L}}{\partial w_i}w_i}{\max\left|\frac{\partial\mathcal{L}}{\partial w_j}w_j\right|},w_j\text{ in same layer as }w_i.\end{aligned} \\
\\
\left.w_i=\left\{\begin{array}{ll}w_i&if|\mathcal{L}(w_i)|>\mathcal{Q}_{|\Delta\hat{\mathcal{L}}(w)|}(\gamma)\\0&otherwise.\end{array}\right.\right. 
\end{gathered}$$

### Reinclude

对于前面的研究，作者分析认为：这样的削减参数的方法，从始至终都是将全部的参数单独的进行考虑，没有对它们的依赖进行考虑，并且不管怎样，重要性估计的方法也不精确；这样会造成参数削减地过快，从而导致性能的下降。为此，作者提出了 reinclude 的操作。

假设经过 remove 后，得到了参数的集合 $\mathcal{W},\overline{\mathcal{W}}$，分别对应着留下来的参数和将被置 0 的参数。作者采用下面的策略将 $\overline{\mathcal{W}}$ 中的某些元素重新放回去：
$$
\begin{aligned}
&\left|\frac{\partial\mathcal{L}}{\partial w_i}\right|\geq\mathcal{Q}_{\left|\frac{\partial\mathcal{L}}{\partial w}\right|,w\in\mathcal{W}}(\delta)\\&\quad ~~~~~~~~~~\wedge\quad ~~~~~~~~~~~~~~~~~~~~\Rightarrow w_i\in\mathcal{W}\\&\exists w_j\in\mathcal{W}|w_j\in\Omega(w_i),\\
\\
&\Omega(w_{i})\text{is the subset of parameters that are neighbors of} w_i
\end{aligned}
$$
首先，在评价指标上，作者只使用梯度作为指标。另外，作者加入了一个先验：在 EVG-NeRFs 中，可以利用参数的网格结构，渲染对象本身是 3D 中的流型，因此可以假设网格也是紧凑和稀疏的。第二行的那个式子就是由此而来。

![[Pasted image 20240124192542.png|500]]

### Overview

在 reinclude 阶段，$\mathcal{W}$ 会迭代地变化。下面就是整个算法的流程。

![[Pasted image 20240124192813.png]]



首先，在训练集上微调一遍该模型，将得到的参数根据上面的算法进行一次压缩，在验证集上测试性能。如果压缩后性能依旧满足需求，就可以继续重复此过程。
![[Pasted image 20240124193418.png]]


## Results

下图在 GVGO、TensoRF、Plenoxels 三种方法上使用 ReNerf 方法。第一行是 baseline，对应着不使用 ReNerf 的结果，LOW 对应着低压缩性，对应着在验证集上得到最好的 PSNR ，HIGH 对应着高压缩性，对应着达到性能临界点时的情况。
![[Pasted image 20240127133943.png]]

总体上看，ReNerf 都能很好的进行压缩，而没有太大的性能损失。


下面是一个消融研究。第一行是 baseline，第二行是对模型中所有的层进行 Remove 操作，剩下的行是只对体素层进行操作。第二行和第三行的对比可以看出，只针对体素的操作远好于对所有层进行操作。最后三行的对比表现力各个部分的重要性。
![[Pasted image 20240127135020.png]]