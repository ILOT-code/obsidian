---
zotero-key: XDUTFEJU
zt-attachments:
  - "377"
title: Preprocessing Enhanced Image Compression for Machine Vision
authors: []
conference: xxx
citekey: 2023-preprocessing
tags: []
---
# Preprocessing Enhanced Image Compression for Machine Vision

**文章链接**: [Zotero](zotero://select/library/items/XDUTFEJU) 
**网页链接**: [URL](https://openreview.net/forum?id=3D0mOtnHGR)
## Abstract

>[!abstract]
>> However, most traditional or learned image codecs are designed to minimize the distortion of the human visual system without considering the increased demand from machine vision systems. In this work, we propose a preprocessing enhanced image compression method for machine vision tasks to address this challenge. 
> 
> 计算机视觉任务需要大量图片数据，它们大部分是经过压缩方法压缩过的。但大部分传统的压缩方法旨在平衡人眼视觉和压缩率，但人眼视觉系统和计算机视觉系统不同。此文章提出了一个预处理模块，以此来增强下游的计算机视觉任务。
> > Specifically, we propose a neural preprocessing module before the encoder to maintain the useful semantic information for the downstream tasks and suppress the irrelevant information for bitrate saving. Furthermore, our neural preprocessing module is quantization adaptive and can be used in different compression ratios. 
> 
> 此预处理模块被放在编码之前，为了提取出更多对下游的视觉任务有用的信息，同时淡化掉一些对视觉任务无用的信息，以此来减少比特率。
> 
> > More importantly, to jointly optimize the preprocessing module with the downstream machine vision tasks, we introduce the proxy network for the traditional non-differential codecs in the back-propagation stage. 
> 
> 在此方法中，图像的编码模块可以选用任意传统编码方法（JPEG,BPG等），这使它能够方便地被应用。但这些方法不可导，阻碍了端到端的训练，本文提出了代理网络来进行反向传播。
> 




## Comments


## Introduction
JPEG、BPG等都是为了人眼视觉系统来涉及的，在PSNR等指标上表现很好。但利用此方法压缩的图片未必能迎合下游视觉任务的需要。

提出了一个预处理模块neural preprocessing (NPP)来过滤原始图像的信息。此外，所提出的神经预处理模块是量化自适应的，可以集成到具有不同压缩比的传统编解码器中
![[Pasted image 20231229144240.png]]

尽管learned image compression可以解决上面提到的传统压缩方法的问题，但它需要大的计算资源和没有像JPEG那样的标准化系统。


## Method

### overview
![[Pasted image 20231229150153.png]]

前向：原始图像 $X$ 被预处理模块处理后得到过滤后的图像 $\bar X$，使用 BPG 算法来对 $\bar X$ 进行压缩，解码后得到 $\hat X$，送到下游任务进行处理。
反向：反向过程由代理网络替换掉 BPG。代理网络本身是一个 learned image compression network。

总体的损失函数是：
$$
\mathcal{L}=R_t+\lambda\mathcal{D}_m+\beta D_{pre}
$$
$\mathcal{D}_m$：视觉任务模型的损失
$R_t$：压缩的比特率
$D_{pre}$：$X$ 和 $\bar X$ 的距离

### NPP
**结构**
![[Pasted image 20231229175458.png]]
$X$ 进入了两个分支，
上面的分支使用 $1\times 1$ 的卷积，用来学习像素级别的转化。
下面的分支使用 U 型的网络结构，去学习抽取和视觉任务相关的语义信息。
两个分支的结果取和得到输出 $\bar X$.

**自适应量化**

压缩算法有不同的量化参数，NPP 需要对不同的量化参数作出调整，在这里使用 quantization adaptation layer 去实现这个。
它直接由两个 MLP 来生成 $c$ 个数，（$c$ 是输入的通道数量），每个数与对于的通道直接相乘。
$$
\begin{aligned}f'=f\odot s\end{aligned}
$$
这样，就能根据量化参数，对中间的特征进行调整。

![[Pasted image 20231229181914.png]]

图（a）（b）分别是原图和进过过滤的图，它们被 BPG 压缩后，得到的大小分别是 63.7 kb 和 47.0 kb。
图 (c) 显示了过滤阶段被丢掉的信息，大部分都集中在对分类任务无关紧要的背景上。
使用 GradCAM method 来分析分类网络的关注点，发现经过过滤后，分类网络的关注范围更加合理。

### proxy network

使用 Minnen’s approach (Minnen et al., 2018) 作为代理网络。它使用 Rate-Distortion （$\begin{aligned}R+\lambda_pD\end{aligned}$）来进行训练。
为了保证该网络能够很好地近似 BPG，对它作预训练，找到合适的 $\lambda_p$ ，其重建结果和 BPG 近似。

之后，对该网络进行微调：
$$
\mathcal{L}_p=R_p+\lambda_pD_p=R_p+\lambda_pd(\hat{X},\hat{Y})
$$
使得 BPG 的重建图像和代理网络的重建图像接近。

**训练过程**
![[Pasted image 20231229185544.png]]


## Experiment
![[Pasted image 20231229190115.png]]