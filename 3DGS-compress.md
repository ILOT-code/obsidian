
# 紧凑性表达
![[Pasted image 20250218154655.png]]
高斯一般被描绘成“点云”的结构，具有如上59个属性。这种看法具有割裂性，空间位置上接近的高斯点大概率具有强相关性。挖掘这种相关性能从整体层面减少数据冗余。
## Scaffold-gs: Structured 3d gaussians for view-adaptive rendering
很多研究都表明，3DGS训练过程的“致密化”操作生成了大量冗余的高斯基元。Scaffold-gs直接舍弃了“致密化”操作，整个scene由少量锚点及锚点上的“属性”构成。单个锚点通过它的“属性”，在其邻域内会生成 $k$ 个高斯点。这样的表达方式降低了高斯点空间坐标上的冗余。

具体而言，单个锚点具有 3 种可训练的参数：
- $f_{v}\in \mathbb{R}^{32}$：特征向量，经过 MLP来生成高斯点的属性。
- $l_{v}\in \mathbb{R}^{3}$：scaling factor，用于缩放offset
- $\mathbf{}{O}_{v}\in \mathbb{R}^{k\times 3}$：offset, 生成的高斯基元坐标相对于锚点坐标的 offset

$f_v$会被进一步调制成与相机坐标以及锚点坐标相关的量， 以更好的生成“颜色”这个与观察角度有关的属性。

锚点的位置与数量对scene的重建很重要，scaffold-gs设计了锚点的增长机制：
![[Pasted image 20241017105937.png]]

## HAC: Hash-grid Assisted Context for 3 D  Gaussian Splatting Compression
HAC 建立在 scaffold-gs 之上，如果说 scaffold 是减少了局部区域内 $k$ 个高斯点的冗余的话，HAC 则是进一步减少了全局范围内的锚点间属性的冗余。它是通过一组二值化的哈希网格来实现这一点的：


![[Pasted image 20241018125808.png]]

对于单个锚点，利用其坐标去哈希 grid 中索引特征 $f^{h}$，利用该特征来锚点的属性 $\mathcal{A}$ 进行概率建模。
$f^{h}$ 经由网络 $MLP_{c}$ 生成 $\mu,\sigma$，以多元独立高斯分布建模该属性，同时由网络 $MLP_{q}$ 生成一个自适应量化系数 $r$，进行量化操作。量化操作在训练过程中，用加均匀噪声的方式代替。

# 减少高斯基元
通过去除高斯基元的方式，能直接而有效的减少存储。
该如何减少高斯基元？可以从不透明度来考虑，在 3DGS 的原文中，剔除了不透明度低于某一阈值的高斯基元。
高斯基元的尺度属性也能作为一个考虑点，过小的尺度，在光栅化的过程中难以造成大的影响。但是高斯点的尺度与相机有关，一个再小的高斯点，只要存在一个足够近的观测相机，就能在该视图上产生大的影响。
## Reducing the Memory Footprint of 3D Gaussian Splatting
提出了一种 **Scale- and Resolution-aware** 的高斯基元去除策略。
![[Pasted image 20250218182651.png]]
定义：pixel footprint, 指对于某个视角和某个高斯基元 $g$,  其相机上观察到 $g$ 的那个像素（指的应该是 $g$ 的中心对应的那个像素），投影到 $g$ 所在的深度平面上，所占据的空间大小。这个 pixel footprint 严格上定义的是线段的长度，以该线段为直径作圆，才是占据空间的大小。显然，对于越近的相机，pixel footprint 越小。

> ![[Pasted image 20250218181948.png]]
> 这个 $a_{min}$ 到底是什么？又是长度单位，又是面积单位。

对于某个视角上的相机而言，这样的 pixel footprint 区域内，应该存在越少的高斯基元越好，否则这些高斯基元都会影响该 pixel 的成像，极有可能造成冗余。对于每个高斯基元而言，作者选择了那个最小的 pixel footprint。如果在此最小的 pixel footprint 内，该球形区域依然包含了多个高斯基元，那就说明，在所有可以观测到该高斯基元的相机中，对应的 pixel 都被超过 1 个高斯基元所渲染。这样的选择很保守，尽可能避免了去除有用的高斯基元。这样的策略也保证了新视角的清晰度，假设新视角更近的，依旧可以保持和训练过程中最高的分辨率一致。

当然，要计算数百万个区域的相交与否是十分昂贵的，作者采用 KNN 来近似计算。

在对所有的高斯基元计算了其对应的球形区域包含的高斯基元数量后（不妨称为该球形区域的度），作者进一步定义了高斯基元的冗余度：与该高斯基元相交的所有球形区域中，最小的度。这依然是一个保守的计算方法。设某高斯基元和 $a,b$ 两个球形区域相交，$a$ 的度很大（为 10），$b$ 则很小（为 1），如果以 10 作为其冗余度，h 这个高斯元很可能被剔除掉，但对于区域 $b$ 而言，这个高斯基元是十分重要的。

在得到所以高斯点的冗余度后，可以计算出统计上的量：$\mu, \sigma$, 可以去除那些超出了几个标准差的点。但这些点不应该被全部删除，因为高斯点的冗余度是相互关联的，仅去除部分这些点后，剩下的很多点的冗余度都会下降。

## Compact 3D Gaussian Representation for Radiance Field
本文为每个高斯基元设置了一个可学习的掩码，来决定是否去除该高斯基元。该方法设置简单，被广泛应用。
$$
\begin{align}
M_{n}=sg(\mathbb{1}[\sigma(m_{n})>\epsilon]-\sigma(m_{n}))+\sigma(m_{n}) \\
\hat{s}_n = M_{n}s_{n}, \hat{o}_{n} = M_{n}o_{n}
\end{align}
$$
并且，额外引入一个损失函数来鼓励减去更多的基元:
$$
L_{m} = \frac{1}{N}\sum _{n=1}^{N}\sigma(m_{n})
$$
# 数据层面压缩
## VQ
在向量量化的情况下，高斯基元的坐标一般不会被量化，因为这回造成大量高斯基元重叠。
### Reducing the Memory Footprint of 3 D Gaussian Splatting
本文的方法准确来说不是 vq, 因为他的码本的条目存储的是标量。它为不透明度、scaling 的三个成分、四元数的实部、四元数的三个虚部、颜色的直流成分、每个 SH 系数设置一个码本，共 20 个码本。
### Compact 3D Gaussian Representation for Radiance Field
本文设计了一种 L级的码本结构来量化尺度和旋转向量（R-VQ）。
> 为什么不用 VQ?
> R-VQ 计算复杂度和显存使用更低。




![[Pasted image 20250218194159.png]]

$$
\begin{align}
\hat{r}_{n}^{l} = \sum_{k=1}^{l} \mathcal{Z}[i^{k}], l \in\{1,\dots L\} \\
i_{n}^{l}= \arg\underset{k}{\min}||\mathcal{Z}^{l}[k]-(r_{n}-\hat{r}_{n}^{l-1})||_{2}^{2}, ~~\hat{r}_{n}^{0}=\vec{0}
\end{align}
$$
并添加损失函数来训练码本：
$$
L_{r}=\frac{1}{NC}\sum_{k=1}^{L} \sum_{n=1}^{N} ||sg[r_{n}-\hat{r}_{n}^{k-1}]-\mathcal{Z}^{k}[i_{n}^{k}]||_{2}^{2}
$$

### Compressed 3D Gaussian Splatting for Accelerated Novel View Synthesis
这篇文章执行的 vq 策略有两个不同：
1. vq 在训练完成之后进行。使用 k-means 来初始化码本条目，随后对码本条目进行微调。
2. 衡量了参数的重要程度，不直接以参数的 $l_{2}$ 距离为衡量距离的指标。

一个标量 $p$ 的敏感程度被衡量为：
$$
S(p)=\frac{1}{\sum_{i=1}^{N} P_{i}}\sum_{i=1}^{N} \lvert \frac{\partial E_{i}}{\partial p} \rvert 
$$
$N$ 为视图数目，$P$ 为像素总数，$E$ 为渲染图像的能量（rgb 之和）。

一个向量的敏感度，等于其最大分量敏感度。

距离被重写为：
$$
\mathcal{D}(\mathbf{x},\mathbf{c})=S(\mathbf{x})()
$$

## Compact 3D Scene Representation via  Self-Organizing Gaussian Grids
本文介绍了一种紧凑的场景表示方法，将高斯基元的参数组织成一个具有局部同质性的 2D 网格，2D 网格的局部同质性使得其很适用现有的图像压缩算法来压缩。

算法流程：把全部的高斯点映射成一个 2D 的网格，排序该 2D 网格，i 使得它满足一定的“光滑性”，并通过一些正则化的方法来促进光滑性。训练完成后，用图像压缩算法来压缩该 2D 网格。
![[Pasted image 20250219145022.png]]
### sort
首先把 $N$ 个高斯基元随机摆列成一个 2D 网格，只是一个近似 $\sqrt{ N }\times \sqrt{ N }\times 59$ 的网格。排序数百万个高维向量是很困难的，作者设计了一种平衡计算复杂性与效果的算法：Parallel Linear Assignment Sorting (PLAS).

使用高斯滤波器对原图像进行光滑操作，生成一个理想的目标图像，把原图像进行重排，逼近该目标。
把图像进行分块，分块的大小和高斯滤波核的大小有关。重排操作只在块内进行，全部的块并行运算。为了块之间 “像素”的重排，每个块在计算之前被赋予了一个随机的位移。
块内的重排计算也是并行化的，每随机四个像素分为一组，计算这 24 种排列中 $L_{2}$ 损失最小的排列。

整个计算过程迭代进行。
![[Pasted image 20250219151112.png]]
### Smoothness Regularization
重排后的 2D 网格，并没有为高斯基元的参数带来有梯度的操作，无法促进光滑化。作者在此引入了一个光滑正则项，由高斯模糊后的图像与本身的距离来衡量。

### Quantization & Compression
使用有损 JPEG XL 压缩 RGB 网格，将其作为 8 位图像，qp为 100。所有其他属性均以无损 JPEG XL 格式存储。

# SH压缩
SH 系数占据 75%的参数量。
## 自适应SH等级
$SH$ 系数中，阶越高，系数个数呈平方项增加。
3DGS 为每个高斯基元的每种颜色分配了一个直流成分和三阶 view dependent 成分。然而，不同材质的物体，颜色的各项异性程度是不同的，出少部分具有反射能力的物体外，大部分物体从各个方向看颜色几乎每多大区别，甚至只保留一个直流成分即可。
### Reducing the Memory Footprint of 3 D Gaussian Splatting
这篇文献直接采取数值分析的方法来进行分析。对于某个高斯基元，计算其在各个视图上的均值和方差，如果方差低于某个阈值，那就只保留直流成分。均值和方差需由透射度加权：
$$
\begin{align} 
\mu=\frac{\sum_{i}^{N}c_{i}\bar{T_{i}}}{ \sum _{i}^{N} \bar{T_{i}}},~&\sigma=\frac{\sum_{i}^{N}(c_{i}-\mu)^{2}\bar{T_{i}}}{ \sum _{i}^{N} \bar{T_{i}}} \\
\bar{T_{i}}&=\frac{\sum_{k}^{P}T_{ik}}{P}
\end{align}
$$
其中，$N$ 是观察到该高斯点的相机数量，$P$ 是该视图中，该高斯点渲染到的像素数量，$T_{ik}$ 是该视角下对该像素的投射比。

然后，只保留低阶的系数，得到与全阶系数的差，并在各个视角下与平均透射度加权，若低于阈值，则保留这些阶，否则，加一阶继续计算。在实验中发现，各波段的点数分布分别为：0 波段 89%，1 波段 0.1%，2 波段 2.7%，3 波段 8.2%。

## 紧凑的 view-dependent颜色表达
### Compact 3 D Gaussian Representation for Radiance Field
作者抛弃了显式存储每个高斯点的 SH 系数，改为隐式表达。具体而言，借助了 grid-based neural field。
首先，把世界坐标下高斯点的坐标进行有界化，利用有界化后的坐标去 grid 中索取对应的特征，该特征与视角方向一起被送入网络中，得到颜色的值。