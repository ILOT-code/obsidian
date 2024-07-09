---
zotero-key: 3DNPKS57
zt-attachments:
  - "234"
title: Task-Aware Encoder Control for Deep Video Compression
authors:
  - Xingtong Ge
  - Jixiang Luo
  - Xinjie Zhang
  - Tongda Xu
  - Guo Lu
  - Dailan He
  - Jing Geng
  - Yan Wang
  - Jun Zhang
  - Hongwei Qin
conference: xxx
citekey: ge2024-taskaware
tags: []
---
# Task-Aware Encoder Control for Deep Video Compression

**文章链接**: [Zotero](zotero://select/library/items/3DNPKS57) [attachment](<file:///home/ilot/Zotero/storage/Z4K59GRE/Ge%20%E7%AD%89%20-%202024%20-%20Task-Aware%20Encoder%20Control%20for%20Deep%20Video%20Compress.pdf>)
**网页链接**: [URL](https://openaccess.thecvf.com/content/CVPR2024/html/Ge_Task-Aware_Encoder_Control_for_Deep_Video_Compression_CVPR_2024_paper.html)


**前置文章：DCVC**
## Abstract

>[!abstract]
>Prior research on deep video compression (DVC) for machine tasks typically necessitates training a unique codec for each specific task, mandating a dedicated decoder per task. In contrast, traditional video codecs employ a flexible encoder controller, enabling the adaptation of a single codec to different tasks through mechanisms like mode prediction.
>之前的 DVC 都是对特定的学习任务训练单独的编码器，而传统的视频编码器则可以通过一些参数来控制编码器行为，适应不同的任务。
Drawing inspiration from this, we introduce an innovative encoder controller for deep video compression for machines. This controller features a mode prediction and a Group of Pictures (GoP) selection module. Our approach centralizes control at the encoding stage, allowing for adaptable encoder adjustments across different tasks, such as detection and tracking, while maintaining compatibility with a standard pre-trained DVC decoder. 
本文为 DVC 设计了一个控制器，在编码阶段使用。它能够控制DVC 适应不同的任务（如识别和追踪）





## Introduction
现有的视频编码器大都为人眼识别服务，但现在很多视频进过压缩和解压后是被视觉任务使用。视觉任务通常更注意帧内的某些区域的语义信息，而不必要关注整个帧。

视频压缩有两种范式，分别是基于预测的残差编码模式，和基于条件概率的模式。它们的比特流由运动和残差/上下文相关的信息构成。后者通常占据 80%以上，来生成高质量的帧。然而对于视觉任务来说，它们更关注物体及其运动，而不是整个帧。

帧有 I 帧 P 帧两种类型，本文设计了 dynamic vision mode predicction (DVPM) 来生成新的帧类型 $P_m$, 它为当前帧预测出可以跳过/不跳过的编码的feature element。这个模块可以根据视觉任务自动学习，在保持视觉任务性能的同时降低码率。
具体而言，对于预测结果为跳过的 feature element，使用超先验网络预测到的平均值代替他，而不用进行编码。

同时，保持帧重建质量也很重要，因为需要提取帧之间的参考关系。对于 Group of Pictures (GoP) 的结构，第一个帧是 I 帧，需要考虑后面的 P 帧和 $P_m$ 帧的比例与排列，以此提高重建质量。本文设计了一个 GoP selection 网络，动态选择出 GoP 的结构。

本文把这样的架构使用在已经经过预训练的 DVC 上，并且之后不改变编码器和解码器的参数。提出的这些模块仅仅控制编码阶段。

##  Method

### Overview

![[Pasted image 20240710004347.png]]
a 是总体流程，GoP Vector 决定了帧是 P 还是 $P_m$ 类型，可以看到解码器部分并没有任何改变，直接使用预先训练的解码器。b 表示了 $P_m$ 的生成，由先前的 P 帧为参考和 DVMP 模块来生成，另外，全部的 P 帧也是以先前的 P 帧为参考的，而不是 $P_m$。c 表示了 GoP selection 网络的构成。

当面向人眼时，帧的形式改为 I, P, P, P... 即可。


### Dynamic Vision Mode Prediction

以预测--残差模式的 DVC 为例，来阐述该模块的作用。我们需要去编码一个 residual feature $m_t$，它的形状是 $c\times h\times w$。同时超先验网络会得到一个形状为 $2c\times h \times w$ 的向量（这里的超先验网络不能为自回归模型），每个值表示了对应位置的均值和方差。
![[Pasted image 20240710010304.png]]

