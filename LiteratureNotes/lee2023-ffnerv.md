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
网格 $G\in \mathbb{R}^{s \times c \times h \times w}$, $s$ 是时间分辨率。有一系列这样的网格，他们的 $s$ 不同，但具有相同的空间分辨率。
给一个时间坐标 $t$，$\phi (t,G) \in \mathbb{R}^{c\times h \times w}$ 表示从网格中提取特征。
$$
\begin{align*}
\phi(t,G)=|\hat{t}-n|G[m]+|\hat{t}-m|G[n]\\
m=\lfloor \hat{t} \rfloor ,n=\lceil \hat{t} \rceil \\
\hat{t}=\frac{ts}{T} ~~~\text{T is the total number of frames}
\end{align*}
$$
从一系列网格中提取特征，并组合到一起，作为网络的输入。
网络会给出 5 个输出：
1. $I(t)\in \mathbb{R}^{3\times H\times W}$, 预测的 Independent frame
2. $\{M(t+i,t)\in \mathbb{R}^{H\times W}\}_{i\in \mathcal{N}}$, $t$ 与 $t+i$ 之间的 flow map。其中 $\mathcal{N}=\{-2,-1,1,2\}$
3. $\{w_{M}(t+i,t)\in \mathbb{R}^{H\times W}\}_{i\in \mathcal{N}}$，一组权重
4. $w_{A}(t),w_{I}(t)\in \mathbb{R}^{H\times W}$, 权重。

### Flow-Guided Frame Aggregation
如果网络仅仅给出 $I(t)$，它和之前的方法 NeRV, E-NeRV 没有太大的区别，但本文引入了光流来利用帧间冗余。
aggregated frame $\overline{A}(t)$ 由下式表达
$$
\begin{align*}
\overline{A}(t)=\sum_{i\in\mathcal{N}}w_M^{\prime}(t+i,t)\circ\mathrm{warp}(I(t+i),M(t+i,t)),\\w_M^{\prime}(i,t)=\exp(w_M(i,t))\circ\frac1{\sum_{j\in\mathcal{N}}\exp(w_M(t+j,t))},
\end{align*}
$$
把该帧作为额外的细节增加到 $I(t)$ 上去：
$$
f_{\theta}{t}=w_A(t)\circ\overline{A}(t)+w_I(t)\circ I(t).
$$

使用
$$
\begin{align*}
L(\theta)=\frac{1}{T}\sum_{t=1}^{T}\lambda_{1}l(\overline{A}(t),F_{t})+\lambda_{2}l(I(t),F_{t})+l(f_{\theta}(t),F_{t}),\\l(\hat{y},y)=\alpha||\hat{y}-y||_{1}+(1-\alpha)(1-\mathrm{SSIM}(\hat{y},y)),
\end{align*}
$$
进行训练。

![[Pasted image 20240904154313.png]]
frame buffer 的使用，节省了训练时间。

## 模型压缩
卷积组