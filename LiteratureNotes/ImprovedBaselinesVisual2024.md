---
zotero-key: BP2MUVND
zt-attachments:
  - "633"
title: Improved Baselines with Visual Instruction Tuning
authors:
  - Haotian Liu
  - Chunyuan Li
  - Yuheng Li
  - Yong Jae Lee
conference: xxx
citekey: ImprovedBaselinesVisual2024
tags: []
---
# Improved Baselines with Visual Instruction Tuning

**文章链接**: [Zotero](zotero://select/library/items/BP2MUVND) [attachment](<file:///home/ilot/Documents/Zotero/storage/32XQK3KW/Liu%20%E7%AD%89%20-%202024%20-%20Improved%20Baselines%20with%20Visual%20Instruction%20Tuning.pdf>)
**网页链接**: [URL](https://openaccess.thecvf.com/content/CVPR2024/html/Liu_Improved_Baselines_with_Visual_Instruction_Tuning_CVPR_2024_paper.html)
## Abstract

>[!abstract]
> llava 1.5, 对 llava 进行了一些改进。
> 

![[Pasted image 20250306190455.png]]


 
 LLaVA 在通常需要简短回答的 academic benchmarks上表现欠佳，且由于训练数据中缺乏此类信息，往往倾向于对是非问题回答“是”。
 为了平衡模型长回答和短回答的能力，如果需要简短的回答，会加上提示词：Answer the question using a single word or phrase. 使用这个 prompt 与 VQAv2 在数据集上微调后，在 MME 测试上，分数由 809.6 提升到了 1323.8
 ![[Pasted image 20250307113602.png]]
## Scaling the Data and Model

1. 首先是训练数据的扩大，i 采用了多个数据集，根据数据集的特点为他们设计了对应 prompt.
2. 线性投影层扩大为 MLP.
3. 分辨率的扩大：使用在 $336^{2}$ 尺寸上预训练的 CLIP
4. LLM 扩大：改用 13B 的 LLM

![[Pasted image 20250307115043.png]]

**To Higher Resolutions**
![[Pasted image 20250307115452.png]]
下面的实验验证了这个 global context 的重要性
![[Pasted image 20250307120018.png]]
代码实现上，稍微有点不一样。

## Evaluation
llava 1.5 实现了用简单的结构，更少的数据量，达到了 sota
![[Pasted image 20250307120116.png]]