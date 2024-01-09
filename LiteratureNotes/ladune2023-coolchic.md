---
zotero-key: 95P8IT2G
zt-attachments:
  - "390"
title: "COOL-CHIC: Coordinate-based Low Complexity Hierarchical Image Codec"
authors:
  - Théo Ladune
  - Pierrick Philippe
  - Félix Henry
  - Gordon Clare
  - Thomas Leguay
conference: xxx
citekey: ladune2023-coolchic
tags: []
---
# COOL-CHIC: Coordinate-based Low Complexity Hierarchical Image Codec

**文章链接**: [Zotero](zotero://select/library/items/95P8IT2G) 
**网页链接**: [URL](https://openaccess.thecvf.com/content/ICCV2023/html/Ladune_COOL-CHIC_Coordinate-based_Low_Complexity_Hierarchical_Image_Codec_ICCV_2023_paper.html)
## Abstract

>[!abstract]
>>We introduce COOL-CHIC, a Coordinate-based Low Complexity Hierarchical Image Codec. It is a learned alternative to autoencoders with 629 parameters and 680 multiplications per decoded pixel.
>
>COOL-CHIC 是一种低复杂度的具有层次结构的图像编解码器，解码单个像素仅需要 629 个参数和 680 此乘法运算。
>>This method is inspired by Coordinate-based Neural Representations, where an image is represented as a learned function which maps pixel coordinates to RGB values.
>
>此方法由基于坐标的隐式神经表达发展而来。

## Introduction and related work

尽管端到端的自编码器达到了图像压缩领域的 SOTA，它们需要百万级别的参数和乘法运算来编解码当个像素点，比传统的编码器高了数个数量级。

2021 年，[[dupont2021-coin|(Emilien Dupont, 2021)]] 提出了基于 Coordinates Neural Representation (CNR) 的策略，通过过拟合 MLP 来完成从坐标值到 RGB 的映射。这种方法大大降低了参数量与运算复杂度。

早期的 CNR 方法都受限与 MLP 的 non-local 特性，因为 MLP 中的任何一个参数都会对全部的像素值的预测造成影响，这种影响是与像素的位置无关的。后来的一些方法，为 MLP 增加了 latent representation of the image，来解决这个问题。

COOL-CHIC 方法也是基于 latent representation 的，作出的改进是构造了具有层次结构的 latent，并使用自回归模型来配合熵编码，以期望更好的表达与压缩 latent。

## Proposed method
### Overview

![[Pasted image 20240107140632.png]]


$\hat{\mathbf{y}}$ 是 latent，它是一系列的不同分辨率的二维矩阵。它经过上采样后得到 $\mathbf{z}$，并经过 MLP 网络 $f_{\theta}$  来重建图像。
$f_{\psi}$ 是 MLP，它预测出 $\hat{\mathbf{y}}$ 的概率。

在训练过程中，损失函数是：
$$
\min_{\hat{\mathbf{y}},\boldsymbol{\theta},\boldsymbol{\psi}}\text{ D }\left(\mathbf{x},f_{\boldsymbol{\theta}}(\operatorname{upsample}(\hat{\mathbf{y}}))\right)-\lambda\log_2p_{\boldsymbol{\psi}}\left(\hat{\mathbf{y}}\right).
$$
在这里的比特率损失项中，并没有关于网络参数的比特率，因为它们的大小和 $\hat{\mathbf{y}}$ 不在一个数量级上。通过对参数 ${\hat{\mathbf{y}},\mathbf{\theta},\mathbf{\psi}}$ 进行训练，使其过拟合，从而达到重建精度与比特率的一个平衡，这三个参数本身就是对图像的压缩表示。

在解码过程中，首先从比特流中取得 $\theta ~\psi$,然后使用网络 $f_{\psi}$ 来解码出 $\hat{\mathbf{y}}$，最后生成重建的图像。

### Synthesis module

$\hat{\mathbf{y}}$ 是由 $L$ 个不同分辨率的二维矩阵组成的，它的大小定义如下，其中 $H,W$ 分别是原始图像的宽和高

$$
\begin{aligned}\hat{\mathbf{y}}&=\left\{\hat{\mathbf{y}}_k\in\mathbb{Z}^{H_k\times W_k},k=0,\ldots,L-1\right\},\\\mathrm{with~}H_k&=\frac{H}{2^k}\text{ and }W_k=\frac{W}{2^k}.\end{aligned}
$$
这种结构有利于捕获图像的高低频信息。

![[Pasted image 20240107155105.png]]
首先，对 $\hat{\mathbf{y_k}}$ 进行参数为 $2^k$ 的双三次插值，这样每个级别的形状都变成了 $H\times W$，然后得到 $\hat{\mathbf{z}}$：
$$
\hat{\mathbf{z}}_{ij}=\left\{\hat{z}_{ijk},k=0,\ldots,L-1\right\}.
$$
最后，$f_{\theta}$ 接收 $\hat{\mathbf{z}}_{ij}$ 并输出该点的 RGB 值。

当然，在实际训练的时候，无法使用离散的值，实际训练的是一个连续的 $\mathbf{y}$，并给它加噪来模拟量化过程：
$$
\left.\hat{\mathbf{y}}=\left\{\begin{array}{ll}\mathbf{y}+\mathbf{u}&\mathrm{~with~}\mathbf{u}\sim\mathcal{U}\left[-0.5,0.5\right]&\mathrm{~if~}\text{learning }\mathbf{y},\\Q(\mathbf{y})&\text{ with }Q\text{ a uniform quantizer}&\text{ otherwise.}\end{array}\right.\right.
$$
### Auto-regressive probability model

$\hat{\mathbf{y}}$ 的真正概率分布是未知的，试图使用网络 $f_{\psi}$ 去逼近它。
首先，需要对 $\hat{\mathbf{y}}$ 的概率进行建模。尝试对联合概率直接建模是困难的，因此假设是完全分解的概率分布。另外，对某个 $\hat{y}_{ijk}$ （$ij$ 表示位置是 $(i,j)$，$k$ 表示的是分辨率级别）进行概率预测的时候，会参考其邻域的 $C$ 个 点：
$$
\begin{align*}
p_{\boldsymbol{\psi}}(\hat{\mathbf{y}})&=\prod_{i,j,k}p_{\boldsymbol{\psi}}(\hat{y}_{ijk}\mid\mathbf{c}_{ijk}). \\\\
with~~ \mathbf{c}_{ijk}& \in \mathbb{Z}^C
\end{align*}
$$

根据通常的做法，通过对一个连续的概率分布 $g(\mathbf{y})$ 进行积分，来对 $p_{\boldsymbol{\psi}}(\hat{\mathbf{y}})$ 进行建模，并且 $g(\mathbf{y})$ 采用拉普拉斯分布：
$$
\begin{aligned}&p_{\boldsymbol{\psi}}(\hat{y}_{ijk}\mid\mathbf{c}_{ijk})=\int_{\hat{y}_{ijk}-0.5}^{\hat{y}_{ijk}+0.5}g(y)\mathrm{d}y,&\mathrm{~}\\&\mathrm{~with~}g\sim\mathcal{L}\left(\mu_{ijk},\sigma_{ijk}\right)\mathrm{~and~}\mu_{ijk},\sigma_{ijk}=f_{\boldsymbol{\psi}}(\mathbf{c}_{ijk}).\end{aligned}
$$
![[Pasted image 20240107161642.png]]

在训练的过程中，网络 $f_{\psi}$ 用来估计 $\hat{\mathbf{y}}$ 的概率，在解码过程中，它会联合比特流和熵解码器逐步地解码出 $\hat{\mathbf{y}}$。

>[!PS]
>有点搞不明白这个解码 $\hat{\mathbf{y}}$ 的过程是怎么回事，它使用的是 range\_coder 编码器，为什么需要一步一步的来解码？按道理解码端也应该是知道概率表的，那不就像解码出 MLP 的参数那样一下子就全解码出来了？而如果解码端不知道概率表，就算这一步通过 $f_{\psi}$ 得到这一个点 $y_{ijk}$ 的概率又有什么用，在不知道全体概率表的情况下又怎么解码？
>

### Compressing the model parameters

在这里对网络参数 $\theta,\psi$ 的量化与压缩进行介绍。
由前文，$\theta,\psi$ 其实是为了两个不同的任务而建的，它们所需的量化精度是不同的，因此，量化过程中，采取不同的精度：
$$
\begin{aligned}\hat{\boldsymbol{\theta}}&=Q(\boldsymbol{\theta},\Delta_{\boldsymbol{\theta}})\mathrm{~and~}\hat{\boldsymbol{\psi}}=Q(\boldsymbol{\psi},\Delta_{\boldsymbol{\psi}}),\\\mathrm{~with~}Q&(\cdot,\Delta)\text{ a scalar quantizer of step }\Delta.\end{aligned}
$$
对它们的概率的建模和 $\hat{\mathbf{y}}$ 类似，依然采用拉普拉斯分布，以 $\hat{\theta}$ 为例：
$$
\begin{aligned}p(\hat{\theta}_i)&=\int_{\hat{\theta}_i-0.5}^{\hat{\theta}_i+0.5}g(\theta)\mathrm{d}\theta,\\&\mathrm{~with~}g\sim\mathcal{L}\left(0,\sigma_{\hat{\boldsymbol{\theta}}}\right)\mathrm{~and~}\sigma_{\hat{\boldsymbol{\theta}}}=\mathrm{stddev}(\hat{\boldsymbol{\theta}})\end{aligned}
$$
由此，MLP 参数的比特率：
$$
\begin{aligned}
R_{MLP}& =R_{\hat{\boldsymbol{\theta}}}+R_{\hat{\boldsymbol{\psi}}}  \\
&=\sum_{\hat{\boldsymbol{\imath}}}-\log_2p(\hat{\theta}_i)+\sum_{\hat{\boldsymbol{\imath}}}-\log_2p(\hat{\boldsymbol{\psi}}_j).
\end{aligned}
$$
## Experimental results

COOL-CHIC 在 BD-rate 上略低于 [[balle2018-variational|(Johannes Ballé, 2018)]].

![[Pasted image 20240107163755.png]]

$\hat{\mathbf{y}}$ 所占的比特是远高于 MLP 的参数的：
![[Pasted image 20240107163930.png]]

下面的左图展示了 COOL-CHIC 解码时的超低复杂度。右图是增加了 COOL-CHIC 的 MLP 层数等，来提高重建质量，于此同时会增加复杂度。右边的纵轴表示了 COOL-CHIC 为了达到和 [[balle2018-variational|(Johannes Ballé, 2018)]] 相同的重建性能所需要增长的比特，横坐标显示的是 COOL-CHIC 的解码运算数。COOL-CHIC 需要额外的百分之 7 的比特率才能达到和 [[balle2018-variational|(Johannes Ballé, 2018)]] 相同的质量，仅需要 680 次乘法运算，但如果增加到 2500 次左右，那就仅仅多需要百分之 3 的比特了。

![[Pasted image 20240107164822.png]]

下图展示了编码的复杂度。
![[Pasted image 20240107165636.png]]
下图展示了不同比特率下，latent 的情况。可以看到，高比特率下，全部的分辨率级别的 latent 都被使用了，而在低比特下，发现最高级别的 latent 是空白的，而且各个级别都要更加的稀疏。这说明 latent 的熵根据权衡因子 $\lambda$ 自动调整。
![[Pasted image 20240107170225.png]]

