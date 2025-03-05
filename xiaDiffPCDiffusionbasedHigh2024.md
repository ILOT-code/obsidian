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