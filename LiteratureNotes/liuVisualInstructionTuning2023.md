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
1. 多模态指令跟随数据。一个关键挑战是缺乏 vision-language instruction-following 的数据。我们提出了一种数据重构视角和流程，利用 ChatGPT/GPT-4 将图像-文本对转换为合适的指令跟随格式。  
2. 大型多模态模型。我们开发了一个大型多模态模型（LMM），通过将 CLIP [40]的开放集视觉编码器与语言解码器 Vicuna [9]连接，并在生成的指令视觉-语言数据上进行端到端微调。我们的实证研究验证了使用生成数据进行 LMM 指令调优的有效性，并为构建通用指令跟随视觉代理提供了实用建议。当与 GPT-4 集成时，我们的方法在 Science QA [34]多模态推理数据集上达到了 SoTA。  
3. 多模态指令跟随基准。我们提出了 LLaVA-Bench，包含两个具有挑战性的基准，并提供了多样化的配对图像、指令和详细注释。  
4. 开源。我们向公众发布了以下资源：生成的多模态指令数据、代码库、模型检查点以及一个视觉聊天演示。