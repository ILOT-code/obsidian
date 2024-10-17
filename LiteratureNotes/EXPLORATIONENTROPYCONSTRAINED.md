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
尽管基于 INR 的方法在一定程度上缓解了这个问题但是对于那些具有优越 RD 性能的方法，其渲染帧速率无法达到实际使用的速度 (Kwan et al., 2023)。其次，在基于 INR 的方法渲染任何帧之前，必须对表示网络进行完全解码，这使得实现流解码具有挑战性。鉴于神经表示更适合编码较长的视频（Chen et al., 2021），这一特性极大地限制了其应用，尤其是在流行的流媒体中。

