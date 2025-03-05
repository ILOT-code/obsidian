---
zotero-key: RFVNJ 6 JH
zt-attachments:
  - "306"
title: "DIFFPC: DIFFUSION-BASED HIGH PERCEPTUAL FIDELITY IMAGE COMPRESSION WITH SEMANTIC REFINEMENT"
authors:
  - Yichong Xia

conference: ICLR2025
citekey:
tags:
---
## Abstract
大体上，本文的方法建立在预训练的 stable diffusion 之上，并额外使用图像的语义特征来指导去噪过程，是一个带条件的 ddpm 模型。
在此基础上，作者专门为图像压缩任务设计了一个控制模块，来强化模型对这些条件的学习。
作者实现低比特率下，在 perceptual fidelity 的 sota 结果。

## Introduction
在低比特率下，像素级指标（如均方误差 MSE）并不是好的选择，直接导致此类压缩方案中大量纹理细节 perceptual fidelity的丢失。这意味着在像素域中寻求低比特率下的紧凑表示不可避免地导致人类感知的下降和图像语义一致性的缺失。这样的缺陷降低了低比特率下压缩算法的实用性。

扩散模型多年来已被证明能够生成高真实感图像，表明扩散模型更适合低比特率图像压缩场景。
Latent Diffusion Model (LDM) 在图像分割和超分任务中，被证明具有强大的先验信息以及多模态语义信息的融合能力。

本文建立在 LDM 上，利用图像级和语义级的信息，来控制去噪过程。这两种信息的融合能平衡图像保真度和语义的保真度，进而取得更好的结果。

## Method
### 总体架构
![[Pasted image 20250305145748.png]]
训练分为两个阶段。
第一阶段，训练压缩器和图像域的控制。
第二阶段，联合训练图像域和语义级的控制。

### Muti-feature Compressor
主要用于去编码 $z_{0}$，减少其冗余信息。它的结构