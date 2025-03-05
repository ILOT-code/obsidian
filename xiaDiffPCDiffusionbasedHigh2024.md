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

扩散模型多年来已被证明能够生成高真实感图像，表明扩散模型更适合低比特率图像压缩场景。近期，文本到图像的潜在扩散模型（LDM）（Rombach 等人，2022）进一步探索了扩散模型在生成任务中的潜力。这些数据驱动的基础扩散模型已被证明能为图像分割（Tian 等人，2024）和图像超分辨率（Lin 等人，2023）等多种视觉任务提供强大的先验信息，并具备融合多模态语义信息的能力。