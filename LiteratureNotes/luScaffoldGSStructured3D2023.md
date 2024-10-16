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
