---
zotero-key: QTHUVXCX
zt-attachments:
  - "637"
title: Visual Instruction Tuning
authors:
  - Haotian Liu
  - Chunyuan Li
  - Qingyang Wu
  - Yong Jae Lee
doi: 10.48550/arXiv.2304.08485
conference: xxx
citekey: liuVisualInstructionTuning2023
tags: []
---
# Visual Instruction Tuning

**文章链接**: [Zotero](zotero://select/library/items/QTHUVXCX) [attachment](<file:///home/ilot/Documents/Zotero/storage/VA2JBC42/Liu%20%E7%AD%89%20-%202023%20-%20Visual%20Instruction%20Tuning.pdf>)
**网页链接**: [URL](http://arxiv.org/abs/2304.08485)
## Abstract

>[!abstract]
> 使用机器生成的和指令相关的数据，已经被证明可以提高 LLM 的 zero-shot 能力，但这一思想在多模态领域的探索很少。
> 本文使用 GPT-4 生成和图像问答相关的数据，通过在这样的数据上进行训练，得到了 LLaVA。它连接了视觉编码器和 LLM，用于通用视觉与语言理解。

## Introduction
本文首次将指令调优扩展到语言-图像多模态空间，为构建通用视觉助手铺平道路。具体而言，本文做出了以下贡献：  
1. 多模态指令跟随数据。一个关键挑战是缺乏 vision-language instruction-following 的数据。我们提出了一种数据重构视角和流程，利用 GPT-4 将图像-文本对转换为合适的指令跟随格式。  
2. 大型多模态模型。我们开发了一个大型多模态模型，通过将 CLIP 视觉编码器与语言解码器 Vicuna 连接，并在生成的数据上进行端到端微调。
3. 基准。我们提出了 LLaVA-Bench，包含两个具有挑战性的基准。
4. 开源。

## Visual Instruction Data Generation
image-text 对的数据有很多，本文使用这样的数据，借助 gpt 4 来生成 visual instruction data.

首先，提供给 gpt 4 的 prompt 有图像自带的 captions，以及描述了图片中元素及其位置信息的 Boxes。通过一些系统级的提示词，使用 gpt 4 来生成了三种类型的数据：
1. conversation: Question涵盖了图像视觉内容的多样化问题，包括物体类型、物体数量、物体动作、物体位置以及物体之间的相对位置
2. Detailed description: 对图像丰富全面的描述。
3. complex reasoning: 是一些需要深度推理的问题与回答。
![[Pasted image 20250306153344.png]]

## 模型结构和训练
vision encoder 采用了预训练的 clip, language model 采用了 vicuna
![[Pasted image 20250306154753.png]]