---
zotero-key: EGQT7JD3
zt-attachments:
  - "319"
title: "FFNeRV: Flow-Guided Frame-Wise Neural Representations for Videos"
authors:
  - Joo Chan Lee
  - Daniel Rho
  - Jong Hwan Ko
  - Eunbyung Park
doi: 10.1145/3581783.3612444
conference: xxx
citekey: lee2023-ffnerv
tags: []
---
# FFNeRV: Flow-Guided Frame-Wise Neural Representations for Videos

**文章链接**: [Zotero](zotero://select/library/items/EGQT7JD3) [attachment](<file:///home/ilot/Zotero/storage/G4LY5ZW2/Lee%20%E7%AD%89%20-%202023%20-%20FFNeRV%20Flow-Guided%20Frame-Wise%20Neural%20Representati.pdf>)
**网页链接**: [URL](http://arxiv.org/abs/2212.12294)
## Abstract

>[!abstract]
>INR 技术，在视频压缩领域，始终无法达到最先进的水平。本文提出了一种将光流信息 合并到视频逐帧表示的技术。FFNeRV 的性能优于广泛使用的标准视频编解码器（H.264 和 HEVC），并与最先进的视频压缩算法相媲美。

## INTRODUCTION
先前的技术又 pixel-wise, frame-wise 两种形式。前者压缩性能、收敛速度、推理速度都相对而言不高。后者在压缩性能上也有不足。
![[Pasted image 20240904143340.png]]


光流信息包含了帧间的运动向量，有利于减少帧间冗余。
本文进一步提出了 multi-resolution temporal grids

## FFNERV
![[Pasted image 20240904145059.png]]
### Multi-Resolution Temporal Grid
网格 $G\in \mathbb{R}^{s \times c \times h \times w}$,