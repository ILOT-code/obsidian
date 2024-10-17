---
zotero-key: 2SD6TWTS
zt-attachments:
  - "392"
title: AN EXPLORATION WITH ENTROPY CONSTRAINED 3D  GAUSSIANS FOR 2D VIDEO COMPRESSION
authors:
  - Anonymous authors
conference: xxx
citekey: EXPLORATIONENTROPYCONSTRAINED
tags: []
---
# AN EXPLORATION WITH ENTROPY CONSTRAINED 3D  GAUSSIANS FOR 2D VIDEO COMPRESSION

**文章链接**: [Zotero](zotero://select/library/items/2SD6TWTS) [attachment](file:///home/ilot/Documents/Zotero/storage/SJIND36N/_.pdf)
**网页链接**: [URL]()
## Abstract

>[!abstract]
>3DGS 在渲染领域得到了快速发展，实现了高质量重建和实时渲染。它的高速渲染速度得益于其渲染方式可以使用显卡中的管道来实现。
>消费级显卡缺乏对INR压缩器帧重建操作的支持，因此在解码速度上很难达到满意的结果。
>本文提出了 TSW 方法，用于将任何高斯模型转化为视频表示方法。在此基础上，设计了训练该模型的方法来提高重建质量。
## Introduction
当前基于学习的神经视频压缩方法在率失真性能方面取得了长足的进步。这些方法具有较高的解码计算复杂度，限制了它们在现实场景中的实用性。
尽管基于 INR 的方法在一定程度上缓解了这个问题，但它们在渲染任何帧之前，必须对表示网络进行完全解码，这使得实现流解码具有挑战性。

3DGS 与 INR 压缩方法具有很多相似的地方，都是对于信号的表达方法，无非就是显示和隐式的区别。

本文探讨 3DGS 在视频压缩中的可行性。提出了一种新颖渲染方法：一种双向的正交投影，TSW, 它能利用相邻帧之间的冗余，不需要信号中任何显式或隐式 3D 结构。使用该方法，任何与 3DGS 模型都可以轻松扩展到 2D 视频表示模型。
在 [[chenHACHashgridAssisted2024|(Yihang Chen, 2024)]] 的框架下，基于 TSW，本文提出了一种端到端可训练的视频压缩器。利用光流信息来引导可变形的高斯点生成。

## Method
首先，把图像的两个轴看成 x, y, 时间轴看成 z 轴。这样，视频就成了 3 维中的流形。利用 3DGS 可以对此流形进行建模。
在视频压缩的背景下，相机的个数和位置是确定了的，就是每个 t 轴的正中间。并且，透视投影变换也是不需要的（投影变换是模拟相机从任意角度观看，但在相机固定的情况下并不需要他），只需要一个世界坐标系到相机坐标系的一个变换就够了。
$$
\Sigma^{\prime}=W\Sigma W^{T}
$$

**TWS**

假设需要渲染 t 帧的图像，从 $[t-h,t]$ 这一段空间渲染一次，再从 $[t,t+h]$ 空间渲染一次，取它们的平均作为渲染结果。这种方式 h 只需要利用一小部分的高斯点进行渲染，而且可以支持随机渲染。
![[Pasted image 20241017161254.png]]

**TIME-AWARE GAUSSIAN GENERATION**

在 [[luScaffoldGSStructured3D2023|(Tao Lu, 2023)]] 中，直接输入