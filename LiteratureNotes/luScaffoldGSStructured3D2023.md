---
zotero-key: ZBH2B7NG
zt-attachments:
  - "398"
title: "Scaffold-GS: Structured 3D Gaussians for View-Adaptive Rendering"
authors:
  - Tao Lu
  - Mulin Yu
  - Linning Xu
  - Yuanbo Xiangli
  - Limin Wang
  - Dahua Lin
  - Bo Dai
doi: 10.48550/arXiv.2312.00109
conference: xxx
citekey: luScaffoldGSStructured3D2023
tags: []
---
# Scaffold-GS: Structured 3D Gaussians for View-Adaptive Rendering

**文章链接**: [Zotero](zotero://select/library/items/ZBH2B7NG) [attachment](<file:///home/ilot/Documents/Zotero/storage/GRDG65UN/Lu%20%E7%AD%89%20-%202023%20-%20Scaffold-GS%20Structured%203D%20Gaussians%20for%20View-Adaptive%20Rendering.pdf>)
**网页链接**: [URL](http://arxiv.org/abs/2312.00109)
## Abstract

>[!abstract]
>3DGaussian Splatting 达到了最先进的渲染质量和速度，但这个模型为了适应训练集中不同视图的 GT，会生成大量冗余的高斯点，忽略了场景本身潜在的几何特征。这不仅造成了存储的冗余，而且如果场景会随着视角的不同变化很大的话，会给出很不稳定的渲染结果。
>本文对此进行了改进，用锚点来描绘好场景的几何形状，并通过锚点来进一步生成高斯点。很好的缓和了以上两个问题。



## Comments

## Method

### 3D-GS
3D-GS 以 3 元高斯分布定义的椭球作为基元，对世界坐标体系下的场景进行建模。
$$
G(x)=e^{-\frac12(x-\mu)^T\Sigma^{-1}(x-\mu)},
$$
当光线射入高斯球球时，$G(x)$ 建模了对该光线强度的削减能力。

相机原点与视角和世界坐标系不一致，但经过简单的仿射变换就能进行转化。高斯球在经过仿射变换后，仍然具有高斯函数的形式。（是封闭的）。3D-GS 技术中定义的投影变换为了保证两点的距离不随变换而改变，破坏了投影变换的仿射结构，因此使用 b 雅可比矩阵来替换原本的变换。最终，在相机的光锥视角下，高斯球的形状变为：
$$
\boldsymbol{\Sigma}^{\prime}=\boldsymbol{J}\boldsymbol{W\Sigma}\boldsymbol{W}^{\top}\boldsymbol{J}^{\top},
$$
每个高斯球自身会发光，在 3D-GS 中，发光强度被看成在高斯球的支撑域上是恒定的。对高斯球沿着纵向维度进行积分，得到的仍旧是一个二维高斯球 $G'(x')$，其形状是把 $\Sigma$ 的第三行第三列直接去掉。
同时，每个高斯球由一组球谐系数定义了不同方向的颜色，最终，渲染的结果用画家 a 算法来给出：

$$
C(x^{\prime})  = \sum_{i\in N}c_{i}\sigma_{i}\prod_{j=1}^{i-1}(1-\sigma_{j}),\sigma_{i}=\alpha_{i}G^{\prime}_{i}(x')
$$
为了保证 $\Sigma$ 的对称性，它由一个对角矩阵和旋转矩阵给出。

### Scaffold-GS
在 Scaffold-GS 中，场景只由少量锚点和其属性组成。

首先，使用稀疏点云生成技术来初始化锚点，$\mathbf{P} \in  \mathbb{R}^{M\times 3}$。
点云进一步被量化到对应的网格中：$\mathbf{V}={\lfloor \frac{\mathbf{P}}{\epsilon} \rceil}\times\epsilon$, 其中 $\epsilon$ 是体素网格的大小

锚点的数量是稀少的，尽管之后会生成新的锚点，但都是少量且谨慎的生成。这些锚点描绘好了场景的几何结构，相当于是场景的骨干了。同时每个锚点还具有其它的特征，这些特征用来描绘该锚点附近局部区域的细节。

具体而言，单个锚点具有 3 种可训练的参数：
- $f_{v}\in \mathbb{R}^{32}$：特征向量，经过 MLP来生成高斯点的参数。
- $l_{v}\in \mathbb{R}^{3}$：scaling factor，用于生成高斯点
- $\mathbf{}{O}_{v}\in \mathbb{R}^{k\times 3}$：每个锚点会生成 $k$ 个高斯点，表示了他们相对于锚点坐标的 offset

在 3D 渲染中，高斯点的颜色，亮度会随着相机位置改变而变化，因此，$f_{v}$ 应该被进一步处理。设相机位置 $\mathbf{x}_{c}$，锚点位置 $\mathbf{x_{v}}$
定义：
$$
\begin{align} \\
\delta_{vc}=\lvert \lvert \mathbf{x}_{v}-\mathbf{x}_{c} \rvert  \rvert_{2} \\
\vec{\mathbf{d}}_{vc}=\frac{\mathbf{x}_{v}-\mathbf{x}_{c}}{\lVert \mathbf{x}_{v}-\mathbf{x}_{c} \rVert_{2} }
\end{align}
$$
产生的特征是：
$$
\begin{align}
{w,w_{1},w_{2}}=Softmax(F_{w}(\delta_{vc},\vec{\mathbf{d}}_{vc})), \\
\hat{f}_{v} = w\times f_{v}+w_{1}f_{v_{2}}
\end{align}
$$